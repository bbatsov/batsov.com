---
title: 'Learning OCaml: Printing Data Structures'
date: 2026-03-01 18:00:00 +0200
tags:
- OCaml
- Learning OCaml
---

If there's one thing that frustrated me early on in my OCaml journey, it was
printing stuff. In Ruby I can `p` anything and get a useful representation.
In Clojure, `prn` just works on every data structure. In OCaml? There's no
generic `print` that works on any type -- the type information is erased at
runtime, so the language simply doesn't know how to stringify an arbitrary
value.

This means you need to be explicit about how to print every type you define.
That sounds tedious (and it can be), but the community has developed tools
that make it mostly painless. Let me walk you through the common approaches.

<!--more-->

## Printing Built-in Types

For basic types, OCaml provides dedicated print functions in `Stdlib`:

```ocaml
print_int 42;;
print_float 3.14;;
print_string "hello";;
print_endline "hello with newline";;
print_char 'x';;
print_newline ();;
```

And of course there's `Printf.printf` for formatted output:

```ocaml
Printf.printf "Name: %s, Age: %d, Score: %.2f\n" "Alice" 30 95.5;;
```

This is all straightforward. The trouble starts when you want to print your own types.

## Writing Manual Printers

The most basic approach is to write a dedicated print function for each type.
Let's say we have a `person` record:

```ocaml
type person = {
  name : string;
  age : int;
  is_developer : bool;
}
```

We'd write something like:

```ocaml
let print_person p =
  Printf.printf "{ name = %S; age = %d; is_developer = %b }"
    p.name p.age p.is_developer

let show_person p =
  Printf.sprintf "{ name = %S; age = %d; is_developer = %b }"
    p.name p.age p.is_developer
```

For lists, you'd typically combine an element printer with `List.iter`:

```ocaml
let print_int_list lst =
  print_string "[";
  List.iteri (fun i x ->
    if i > 0 then print_string "; ";
    print_int x
  ) lst;
  print_string "]"
```

This works, but it's tedious. Every time you add a field to a record, you
have to remember to update the printer. And for nested types (say, a list of
records containing other records) the boilerplate multiplies fast. Coming
from languages where printing just works out of the box, this feels like
a lot of ceremony for something that should be trivial.

## Automatic Printing with ppx_deriving

This is where [ppx_deriving](https://github.com/ocaml-ppx/ppx_deriving)
saves the day. Its `show` plugin generates printer functions automatically
from your type definitions:

```ocaml
type person = {
  name : string;
  age : int;
  is_developer : bool;
} [@@deriving show]
```

That single annotation generates two functions:

- `pp_person : Format.formatter -> person -> unit` -- a pretty-printer for use
  with OCaml's `Format` module
- `show_person : person -> string` -- returns the string representation directly

Now printing is trivial:

```ocaml
let bbatsov = { name = "Bozhidar Batsov"; age = 42; is_developer = true }

let () = print_endline (show_person bbatsov)
(* { name = "Bozhidar Batsov"; age = 42; is_developer = true } *)
```

The generated printers are compositional -- if you have a list of persons
and the element type derives `show`, everything just works:

```ocaml
type person = {
  name : string;
  age : int;
} [@@deriving show]

let people = [
  { name = "Bozhidar"; age = 42 };
  { name = "Jane"; age = 34 };
]

let () = print_endline ([%show: person list] people)
```

The `[%show: person list]` syntax is really handy -- it lets you derive a
printer for any type expression inline, without needing a separate type
declaration.

To use `ppx_deriving` in your project, add it to your `dune` file:

```dune
(library
 (name mylib)
 (preprocess (pps ppx_deriving.show)))
```

I slap `[@@deriving show]` on pretty much every type I define these days.
The small compile-time cost is well worth the debugging convenience. If you
want to learn more about PPX in general, I wrote a [longer
article]({% post_url 2026-03-03-ppx-for-mere-mortals %}) about it.

## Which Approach to Use?

For debugging and development, `[@@deriving show]` is the obvious winner.
It's become such a standard part of my OCaml workflow that I barely think
about it anymore.

For user-facing output (error messages, CLI formatting, log lines), you'll
still want manual formatting with `Printf.sprintf` or `Format.asprintf`.
The derived printers produce a generic representation that's great for
debugging but not something you'd want to show end users.

That's all I have for you today. Keep hacking!
