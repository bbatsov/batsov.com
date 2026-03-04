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

Let's explore records using a fun data model -- superheroes:

```ocaml
type power = Flight | SuperStrength | Telepathy | Speed | Gadgets

type strength = Human | Enhanced | Superhuman | Cosmic

type superhero = {
  name : string;
  alias : string;
  powers : power list;
  strength : strength;
  first_appearance : int;
}
```

<!--more-->

## Creating Records

Creating a record is straightforward -- list all fields with their values:

```ocaml
let batman = {
  name = "Bruce Wayne";
  alias = "Batman";
  powers = [Gadgets];
  strength = Human;
  first_appearance = 1939;
}

let superman = {
  name = "Clark Kent";
  alias = "Superman";
  powers = [Flight; SuperStrength];
  strength = Cosmic;
  first_appearance = 1938;
}
```

Note that you don't specify the type anywhere -- the compiler infers it from
the field names. This works great when field names are unique, but can cause
trouble when two record types in scope share a field name:

```ocaml
type hero = { name : string; power : string }
type villain = { name : string; evil_plan : string }

(* Which type is this? The compiler picks the most recently defined one -- villain *)
let mystery = { name = "Enigma"; power = "puzzles" }
(* Error: Unbound record field power *)
```

You can disambiguate by annotating the type:

```ocaml
let h : hero = { name = "Enigma"; power = "puzzles" }
```

Or by prefixing a field name with the module:

```ocaml
let h = { Hero.name = "Enigma"; power = "puzzles" }
```

> In practice, this ambiguity rarely bites you because OCaml's module system
> naturally separates types into different scopes. The idiomatic approach is
> to define one main record type per module (named `t` by convention), which
> avoids collisions entirely.
{: .prompt-tip }

## Accessing Fields

Field access uses the usual dot notation:

```ocaml
let name = batman.name               (* "Bruce Wayne" *)
let year = batman.first_appearance   (* 1939 *)
```

You can use fields directly in expressions:

```ocaml
let is_golden_age hero = hero.first_appearance < 1956
let () = Printf.printf "%s: golden age = %b\n"
  batman.alias (is_golden_age batman)
(* Batman: golden age = true *)
```

## Functional Updates

Records are immutable by default in OCaml. You can't modify a field in
place -- instead, you create a new record with some fields changed using the
`with` keyword:

```ocaml
let upgraded_batman = { batman with
  powers = [Gadgets; SuperStrength];
  strength = Enhanced;
}
```

This creates a new `superhero` record that's a copy of `batman` with only
the specified fields changed. The original is untouched. If you've used
Haskell's record update syntax or Erlang's map update syntax, this will feel
familiar.

Functional updates are especially handy when your records have many fields
and you only want to change one or two:

```ocaml
let justice_league = [batman; superman]

(* Promote everyone to Cosmic strength *)
let cosmic_league =
  List.map (fun h -> { h with strength = Cosmic }) justice_league
```

## Mutable Fields

While records are immutable by default, OCaml lets you mark individual
fields as `mutable`:

```ocaml
type battle_stats = {
  hero : string;
  mutable wins : int;
  mutable losses : int;
}

let stats = { hero = "Batman"; wins = 0; losses = 0 }
let () = stats.wins <- stats.wins + 1   (* wins is now 1 *)
```

The `<-` operator mutates the field in place. Note that only fields
explicitly marked `mutable` can be changed this way -- trying to mutate an
immutable field is a compile error.

I'd use mutable fields sparingly -- they're there when you need them for
performance-critical code, but immutable records with functional updates are
generally the idiomatic approach in OCaml.

## Pattern Matching on Records

Like everything in OCaml, records work with pattern matching. You can
destructure them directly in function arguments:

```ocaml
let describe { alias; strength; first_appearance; _ } =
  let strength_str = match strength with
    | Human -> "human-level"
    | Enhanced -> "enhanced"
    | Superhuman -> "superhuman"
    | Cosmic -> "cosmic"
  in
  Printf.printf "%s (%d) - %s strength\n"
    alias first_appearance strength_str
```

The `_` in the pattern tells the compiler you're intentionally ignoring the
other fields. Without it, you'd get a warning about inexhaustive field
patterns.

You can also pattern match in `let` bindings:

```ocaml
let { alias; powers; _ } = batman in
Printf.printf "%s has %d powers\n" alias (List.length powers)
```

And in `match` expressions for more complex logic:

```ocaml
let classify = function
  | { strength = Cosmic; _ } -> "overpowered"
  | { strength = Human; powers; _ } when List.length powers > 0 ->
    "resourceful"
  | { strength = Human; _ } -> "ordinary"
  | _ -> "super"
```

## Field Punning

You might have noticed the shorthand in the examples above --
`{ alias; strength; _ }` instead of `{ alias = alias; strength = strength; _ }`.
This is called "field punning" and it works both in patterns and when
constructing records:

```ocaml
let alias = "Flash"
let name = "Barry Allen"
let powers = [Speed]
let strength = Superhuman
let first_appearance = 1956

(* these are equivalent *)
let flash_v1 = { name = name; alias = alias; powers = powers;
                 strength = strength; first_appearance = first_appearance }
let flash_v2 = { name; alias; powers; strength; first_appearance }
```

If you're coming from JavaScript, this is the same idea as ES6's shorthand
property names in object literals. It's particularly nice when you're
constructing a record from variables that already have the right names.

## Records and Modules

The idiomatic OCaml pattern is to define one main type per module, named `t`:

```ocaml
(* superhero.ml *)
type power = Flight | SuperStrength | Telepathy | Speed | Gadgets
type strength = Human | Enhanced | Superhuman | Cosmic

type t = {
  name : string;
  alias : string;
  powers : power list;
  strength : strength;
  first_appearance : int;
}

let create ~name ~alias ~powers ~strength ~first_appearance =
  { name; alias; powers; strength; first_appearance }

let is_golden_age t = t.first_appearance < 1956
```

This convention means you refer to the type as `Superhero.t` from outside
the module, and field access reads naturally as `hero.Superhero.alias` (or
just `hero.alias` if you've opened the module). It also avoids the field
name collision problem entirely, since each module has its own namespace.

## Printing Records

You can't just `print` a record in OCaml -- there's no generic print that
works on any type. The easiest solution is `[@@deriving show]` from
`ppx_deriving`:

```ocaml
type superhero = {
  name : string;
  alias : string;
  powers : power list;
} [@@deriving show]

let () = print_endline (show_superhero batman)
```

I wrote a [dedicated article]({% post_url 2026-03-01-printing-data-in-ocaml %})
on printing data structures if you want the full story.

## Records vs Tuples

One question that comes up for beginners: when should you use a record
instead of a tuple?

Tuples are great for quick, throwaway groupings -- returning two values from
a function, for example. But once you have more than two or three fields, or
when the meaning of each position isn't obvious, records are almost always
better:

```ocaml
(* Tuple -- what does each position mean? *)
let hero = ("Bruce Wayne", "Batman", 1939)

(* Record -- self-documenting *)
let hero = { name = "Bruce Wayne"; alias = "Batman"; first_appearance = 1939 }
```

Records also give you pattern matching with named fields, functional
updates with `with`, and better error messages from the compiler. If you
find yourself reaching for a tuple with more than 2-3 elements, it's
probably time for a record.

## Further Reading

- [Records](https://dev.realworldocaml.org/records.html) -- the Real World
  OCaml chapter on records. Goes deeper into functional updates, first-class
  fields (via `ppx_fields_conv`), and the interaction between records and modules.
- [Basic Data Types and Pattern Matching](https://ocaml.org/docs/basic-data-types) --
  the official ocaml.org guide covering records alongside other core data types.

That's all I have for you today. Keep hacking!
