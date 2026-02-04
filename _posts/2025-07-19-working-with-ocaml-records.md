---
layout: post
title: "Working with OCaml Records"
date: 2025-07-19 11:30:00 +0300
categories: ocaml programming fsharp rust
---

Records are one of the most common and useful composite types in programming. They are equivalent to `struct`s in many C-family languages and provide a way to group named fields together into a single unit. In OCaml, records are simple, efficient, and immutable by default, which fits perfectly with the functional paradigm.

Let's explore how to define and work with them, and then compare OCaml's approach to what you might find in F# and Rust.

### Defining and Creating Records

Defining a record in OCaml requires a `type` definition. The fields are listed with their names and types.

```ocaml
type person = {
  name: string;
  age: int;
  is_developer: bool;
}
```

Once the type is defined, you can create an instance of the record with a simple literal syntax.

```ocaml
let bbatsov = {
  name = "Bozhidar Batsov";
  age = 42;
  is_developer = true;
}
```

The type inference in OCaml is excellent, but the compiler must know the type definition of the record you're creating. The field names and types must match the definition exactly.

### Accessing and Updating Records

Accessing a field is done using the familiar dot notation, which is efficient and requires no special syntax.

```ocaml
print_endline bbatsov.name; (* "Bozhidar Batsov" *)
```

The more interesting part is updating a record. Since OCaml records are immutable, you cannot change a field in place. Instead, you create a *new* record with one or more fields updated. OCaml provides a clean syntax for this using the `with` keyword.

```ocaml
let bbatsov_older = { bbatsov with age = 43 }
```

This expression creates a new `person` record that is a copy of `bbatsov`, but with the `age` field set to `43`. The original `bbatsov` record remains unchanged. This is a powerful feature for maintaining immutability without excessive boilerplate.

### Comparison with F#

F#, another language from the ML family, has a record implementation that is remarkably similar to OCaml's. The syntax for definition and creation is almost identical.

```fsharp
type Person = {
  Name: string
  Age: int
  IsDeveloper: bool
}

let bbatsov = {
  Name = "Bozhidar Batsov"
  Age = 42
  IsDeveloper = true
}

// F# also uses the 'with' keyword for copy-and-update
let bbatsovOlder = { bbatsov with Age = 43 }

printfn "%s" bbatsovOlder.Name // "Bozhidar Batsov"
printfn "%d" bbatsovOlder.Age   // 43
```

The core concepts are the same: immutable by default, with a dedicated syntax for non-destructive updates. This shared heritage makes it easy to switch between the two languages for this particular feature.

### Comparison with Rust

Rust also has `struct`s, which serve the same purpose. However, Rust's philosophy on mutability is different and more explicit.

In Rust, a `struct` is defined similarly:

```rust
struct Person {
    name: String,
    age: u32,
    is_developer: bool,
}
```

Mutability in Rust is a property of the *binding*, not the type. If you want to mutate a struct, you must declare its binding with `mut`.

```rust
let mut bbatsov = Person {
    name: String::from("Bozhidar Batsov"),
    age: 42,
    is_developer: true,
};

// Direct mutation is allowed if the binding is mutable
bbatsov.age = 43;
```

However, Rust also provides a way to perform functional-style updates, similar to OCaml's `with`. This is done with the `..` struct update syntax.

```rust
let bbatsov = Person {
    name: String::from("Bozhidar Batsov"),
    age: 42,
    is_developer: true,
};

// Create a new struct based on the old one
let bbatsov_older = Person {
    age: 43,
    ..bbatsov
};
```

This creates a new `Person` instance, moving the values from `bbatsov` for the fields that were not explicitly specified (`name` and `is_developer` in this case). This is a powerful feature that gives Rust developers the flexibility to choose between direct mutation and a more functional, immutable style.

### Pattern Matching

Like other data types in OCaml, records have first-class support for pattern matching, which is useful for deconstructing them.

```ocaml
let describe_person person =
  match person with
  | { name; age; is_developer = true } ->
    Printf.printf "%s is a %d-year-old developer.
" name age
  | { name; age; is_developer = false } ->
    Printf.printf "%s is a %d-year-old non-developer.
" name age

describe_person bbatsov_older;
```

This allows you to bind record fields to local variables directly in the pattern, leading to concise and readable code.

### Final Thoughts

OCaml records are a simple and effective tool. Their immutability-by-default nature, combined with the convenient `with` syntax for updates, makes them a great fit for writing safe, predictable functional code. The parallels with F# and the interesting contrasts with Rust's approach highlight the different ways languages can tackle the same fundamental problem of data aggregation.
