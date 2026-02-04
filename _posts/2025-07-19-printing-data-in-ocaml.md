---
title: "Printing Data Structures in OCaml"
date: 2025-07-19 18:00:00 +0300
tags:
- OCaml
- Learning OCaml
---

One of the first things developers want to do in any language is print out the value of a variable. In languages with dynamic typing or extensive reflection, this is often a trivial, built-in operation. In OCaml, the story is a bit different. Its strong, static type system requires you to be explicit about how to turn a data structure into a string representation. There is no universal `print` or `console.log` that works on any type.

While this might seem like a limitation, it's a direct consequence of OCaml's design philosophy: be explicit and type-safe. The good news is that the community has developed powerful tools to make this process painless. Let's explore the common approaches.

For our examples, we'll use the `person` record from my previous post and a simple list of integers.

```ocaml
type person = {
  name: string;
  age: int;
  is_developer: bool;
}

let people = [
  { name = "Bozhidar Batsov"; age = 42; is_developer = true };
  { name = "Jane Doe"; age = 34; is_developer = false };
]
```

### Approach 1: Manual Printer Functions

The most fundamental approach is to write a dedicated function to print your data structure. This gives you complete control over the output format.

For our `person` record, the function would look like this:

```ocaml
let print_person p =
  Printf.printf
    "{ name = \"%s\"; age = %d; is_developer = %b }"
    p.name p.age p.is_developer
```

Here, we use the `Printf` module to create a formatted string. For a list, you typically combine a printer for the element type with `List.iter`.

```ocaml
let print_int_list lst =
  print_string "[";
  List.iter (fun i -> Printf.printf "%d; " i) lst;
  print_string "]"

print_int_list [1; 2; 3]; (* [1; 2; 3; ] *)
```

To print our list of people, we can compose these two ideas:

```ocaml
let print_people lst =
  print_string "[\n";
  List.iter (fun p ->
    print_string "  ";
    print_person p;
    print_string ";\n"
  ) lst;
  print_string "]"

print_people people;
```

**Trade-offs:**

*   **Pros:** No external dependencies. You have full control over the formatting, which is great for producing user-facing output. It forces you to think about the structure of your data.
*   **Cons:** It's incredibly verbose. Writing these printers for every new type is tedious and error-prone. If you add a field to your record, you must remember to update the printer, or it will be silently ignored.

### Approach 2: Automatic Derivation with PPX

To eliminate the boilerplate of manual printers, the OCaml community relies on PPX (Preprocessor eXtensions). A PPX is a tool that rewrites the abstract syntax tree (AST) of your code at compile time. We can use this to automatically generate printer functions from our type definitions.

The most popular choice for this is `ppx_deriving_show`.

First, you need to have it installed (`opam install ppx_deriving_show`) and configured in your build system (e.g., Dune).

Once set up, you simply annotate your type definition with `[@@deriving show]`.

```ocaml
type person = {
  name: string;
  age: int;
  is_developer: bool;
} [@@deriving show]
```

This single line of code does two things:

1.  It generates a "pretty-printer" function named `pp_person`. This function takes a formatter and a value (e.g., `pp_person Format.std_formatter bbatsov`).
2.  It generates a `show_person` function that returns the string representation directly (e.g., `show_person bbatsov`).

Now, printing is trivial:

```ocaml
let bbatsov = { name = "Bozhidar Batsov"; age = 42; is_developer = true };;

print_endline (show_person bbatsov);
(* Output: { name = "Bozhidar Batsov"; age = 42; is_developer = true } *)
```

The real power of `ppx_deriving_show` is that it's compositional. It automatically knows how to print lists, options, and other standard types if the element type has a `show` function.

```ocaml
(* We need to tell the PPX how to show a list of people *)
(* The generated function will be `show_list` *)
[@@@deriving show { with_path = false }]

let people = [
  { name = "Bozhidar Batsov"; age = 42; is_developer = true };
  { name = "Jane Doe"; age = 34; is_developer = false };
]

print_endline (show_list show_person people);
(* Output:
[({ name = "Bozhidar Batsov"; age = 42; is_developer = true });
  ({ name = "Jane Doe"; age = 34; is_developer = false })]
*)
```

**Trade-offs:**

*   **Pros:** Drastically reduces boilerplate. It's the standard, idiomatic way to make types printable for debugging. It's less error-prone because the printer is always in sync with the type definition.
*   **Cons:** Adds a dependency on a PPX. It can feel a bit like "magic" if you're not familiar with how PPXs work. The default output format is generic and may not be what you want for user-facing text.

### Conclusion

So, which approach should you use?

*   For **debugging and development**, `ppx_deriving_show` is the undisputed winner. The time and effort it saves are immense, and it has become a standard part of any modern OCaml workflow.
*   For **final, user-facing output**, a manual printer is often the better choice. It gives you the precise control needed to format the output exactly as you want it, without the syntactic noise of the derived printers.

OCaml's explicitness is a feature, not a bug. By forcing you to define how to print your types, it maintains type safety and clarity. And with tools like PPX, you get the best of both worlds: safety and convenience.
