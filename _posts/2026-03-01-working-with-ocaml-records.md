---
title: 'Learning OCaml: Working with Records'
date: 2026-03-01 11:30:00 +0200
tags:
- OCaml
- Learning OCaml
---

Records are one of those things that look almost identical across ML-family
languages, so I didn't expect many surprises when I started using them in
OCaml. For the most part I was right -- but there were a few things worth
noting, especially if you're coming from a language where records/structs
are mutable by default.

<!--more-->

## Defining and Creating Records

Record definitions in OCaml look pretty much like what you'd expect:

```ocaml
type person = {
  name : string;
  age : int;
  is_developer : bool;
}
```

Creating a record is straightforward:

```ocaml
let bbatsov = {
  name = "Bozhidar Batsov";
  age = 42;
  is_developer = true;
}
```

Note that you don't specify the type anywhere -- the compiler infers it from
the field names. This is nice most of the time, but can cause ambiguity if two
record types in scope share a field name. In that case the compiler picks the
most recently defined type, which might not be what you want. You can
disambiguate by annotating the type explicitly:

```ocaml
let bbatsov : person = {
  name = "Bozhidar Batsov";
  age = 42;
  is_developer = true;
}
```

## Accessing Fields

Field access uses the usual dot notation:

```ocaml
let name = bbatsov.name       (* "Bozhidar Batsov" *)
let age = bbatsov.age         (* 42 *)
```

Nothing surprising here.

## Functional Updates

Records are immutable by default in OCaml. You can't modify a field in
place -- instead, you create a new record with some fields changed using the
`with` keyword:

```ocaml
let older_bbatsov = { bbatsov with age = 43 }
```

This creates a new `person` record that's a copy of `bbatsov` with only
`age` changed. The original is untouched. If you've used Haskell's record
update syntax or Erlang's map update syntax, this will feel familiar.

You can update multiple fields at once:

```ocaml
let retired_bbatsov = { bbatsov with age = 65; is_developer = false }
```

## Mutable Fields

While records are immutable by default, OCaml does let you mark individual
fields as `mutable`:

```ocaml
type counter = {
  name : string;
  mutable count : int;
}

let c = { name = "hits"; count = 0 }
let () = c.count <- c.count + 1   (* count is now 1 *)
```

I'd use this sparingly -- it's there when you need it for performance or
interop reasons, but immutable records with functional updates are generally
the way to go in OCaml.

## Pattern Matching on Records

Like everything in OCaml, records work with pattern matching. You can
destructure them directly in `match` expressions and function arguments:

```ocaml
let greet { name; is_developer; _ } =
  if is_developer then
    Printf.printf "Hey %s, fellow hacker!\n" name
  else
    Printf.printf "Hello %s!\n" name
```

The `_` in `{ name; is_developer; _ }` tells the compiler you're
intentionally ignoring the other fields. Without it, you'd get a warning
about inexhaustive field patterns.

You can also pattern match in `let` bindings:

```ocaml
let { name; age; _ } = bbatsov in
Printf.printf "%s is %d years old\n" name age
```

## A Note on Field Punning

You might have noticed the shorthand in the pattern matching examples above --
`{ name; is_developer; _ }` instead of `{ name = name; is_developer = is_developer; _ }`.
This is called "field punning" and it works both in patterns and when constructing
records:

```ocaml
let name = "Bozhidar Batsov"
let age = 42
let is_developer = true

(* these are equivalent *)
let p1 = { name = name; age = age; is_developer = is_developer }
let p2 = { name; age; is_developer }
```

If you're coming from JavaScript, this is the same idea as ES6's shorthand
property names in object literals.

## Printing Records

One thing that will probably frustrate you early on is that you can't just
print a record. There's no generic `print` in OCaml that works on any type.
The easiest solution is to derive a printer automatically using
`ppx_deriving`:

```ocaml
type person = {
  name : string;
  age : int;
  is_developer : bool;
} [@@deriving show]

let () = print_endline (show_person bbatsov)
(* { name = "Bozhidar Batsov"; age = 42; is_developer = true } *)
```

I wrote more about this in my article on
[printing data structures]({% post_url 2026-03-01-printing-data-in-ocaml %}).

That's all I have for you today. Keep hacking!
