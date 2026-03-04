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

There are also simple conversion functions like `string_of_int` and
`string_of_float` when you just need a string:

```ocaml
let s = "Age: " ^ string_of_int 42;;
(* "Age: 42" *)
```

And of course there's `Printf.printf` for formatted output:

```ocaml
Printf.printf "Name: %s, Age: %d, Score: %.2f\n" "Alice" 30 95.5;;
```

> A quick cheat sheet for the format specifiers you'll use most often: `%s`
> for strings, `%d` for ints, `%f` for floats (`%.2f` for 2 decimal places),
> `%b` for bools, and `%S` for strings with quotes around them (handy for
> debugging). You can use `Printf.sprintf` instead of `printf` to get a
> string back rather than printing to stdout.
{: .prompt-tip }

This is all straightforward. The trouble starts when you want to print your
own types.

## The Running Example

Let's use something more fun than the usual `person` record. We'll model
superheroes:

```ocaml
type power = Flight | SuperStrength | Telepathy | Speed | Gadgets

type strength = Human | Enhanced | Superhuman | Cosmic

type superhero = {
  name : string;
  alias : string;
  powers : power list;
  strength : strength;
  first_appearance : int;  (* year *)
}
```

And a few heroes to work with:

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

let wonder_woman = {
  name = "Diana Prince";
  alias = "Wonder Woman";
  powers = [Flight; SuperStrength; Telepathy];
  strength = Superhuman;
  first_appearance = 1941;
}
```

Now try `print_endline batman` and... you get a type error. OCaml has no
idea how to turn a `superhero` into a string. Let's fix that.

## Writing Manual Printers

The most basic approach is to write dedicated print functions. For our
superhero types, we'd need to handle each layer:

```ocaml
let string_of_power = function
  | Flight -> "Flight"
  | SuperStrength -> "Super Strength"
  | Telepathy -> "Telepathy"
  | Speed -> "Speed"
  | Gadgets -> "Gadgets"

let string_of_strength = function
  | Human -> "Human"
  | Enhanced -> "Enhanced"
  | Superhuman -> "Superhuman"
  | Cosmic -> "Cosmic"

let show_superhero h =
  Printf.sprintf "%s (%s) - %s, first appeared in %d, powers: %s"
    h.alias h.name
    (string_of_strength h.strength)
    h.first_appearance
    (h.powers |> List.map string_of_power |> String.concat ", ")
```

```ocaml
print_endline (show_superhero batman);;
(* Batman (Bruce Wayne) - Human, first appeared in 1939, powers: Gadgets *)

print_endline (show_superhero wonder_woman);;
(* Wonder Woman (Diana Prince) - Superhuman, first appeared in 1941, powers: Flight, Super Strength, Telepathy *)
```

This works, but it's tedious. We had to write a conversion function for
every variant type and compose them manually. Every time you add a new
power or a new field to the record, you have to remember to update all
the printers. And for nested types the boilerplate multiplies fast.
Coming from languages where printing just works out of the box, this
feels like a lot of ceremony for something that should be trivial.

That said, manual printers give you full control over formatting, which is
exactly what you want for user-facing output (error messages, CLI output,
log lines).

## Automatic Printing with ppx_deriving

This is where [ppx_deriving](https://github.com/ocaml-ppx/ppx_deriving)
saves the day. Its `show` plugin generates printer functions automatically
from your type definitions. Just add `[@@deriving show]`:

```ocaml
type power = Flight | SuperStrength | Telepathy | Speed | Gadgets
[@@deriving show]

type strength = Human | Enhanced | Superhuman | Cosmic
[@@deriving show]

type superhero = {
  name : string;
  alias : string;
  powers : power list;
  strength : strength;
  first_appearance : int;
} [@@deriving show]
```

That annotation generates two functions per type:

- `pp_superhero : Format.formatter -> superhero -> unit` -- a pretty-printer
  for use with OCaml's `Format` module
- `show_superhero : superhero -> string` -- returns the string representation
  directly

Now printing is trivial:

```ocaml
print_endline (show_superhero batman);;
(* { name = "Bruce Wayne"; alias = "Batman";
     powers = [Gadgets]; strength = Human;
     first_appearance = 1939 } *)
```

The generated printers are compositional -- they know how to handle lists,
options, and other standard types automatically, as long as the element type
also derives `show`. So printing a list of heroes just works:

```ocaml
let justice_league = [batman; superman; wonder_woman]

let () = print_endline ([%show: superhero list] justice_league)
```

The `[%show: superhero list]` syntax is really handy -- it lets you derive a
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

## Debugging Tips

A few practical things I've picked up:

### Debug Printing Mid-pipeline

You can sneak a print into a `|>` chain without breaking the flow:

```ocaml
let debug label x =
  Printf.printf "[DEBUG] %s: %s\n" label (show_superhero x);
  x

let result =
  batman
  |> debug "before upgrade"
  |> upgrade_powers
  |> debug "after upgrade"
```

### Inline `[%show: ...]` for Complex Types

When you need to print something and don't want to define a type just for it:

```ocaml
Printf.printf "Heroes and powers: %s\n"
  ([%show: (string * power list) list]
    (List.map (fun h -> (h.alias, h.powers)) justice_league))
```

### Watch Out for Missing `show` on Nested Types

If your record contains a type that doesn't derive `show`, you'll get a
compile error about a missing `pp_` function. The error message can be
cryptic -- just look for the type that's missing the derivation and add
`[@@deriving show]` to it.

### `{ with_path = false }` for Cleaner Output

By default, derived printers qualify variant constructors with their module
path (e.g. `Superhero.Flight`). If you find this noisy:

```ocaml
type power = Flight | SuperStrength | Telepathy | Speed | Gadgets
[@@deriving show { with_path = false }]

(* Now prints "Flight" instead of "Superhero.Flight" *)
```

## Printing in the Toplevel

If you're exploring code in `utop` (or the plain `ocaml` toplevel), you'll
notice that it already displays values of built-in types:

```ocaml
# 42;;
- : int = 42
# [1; 2; 3];;
- : int list = [1; 2; 3]
# "hello";;
- : string = "hello"
```

But for your own types, you just see the structure without much help:

```ocaml
# batman;;
- : superhero = {name = "Bruce Wayne"; alias = "Batman"; ...}
```

Actually, the toplevel does a decent job with simple records and variants --
it knows the structure from the type definition. Where it falls short is
with abstract types or types from external modules where the representation
is hidden.

If you've derived `show` for your types, you can register the generated
pretty-printer with the toplevel using `#install_printer`:

```ocaml
# #install_printer pp_superhero;;
# batman;;
- : superhero = { name = "Bruce Wayne"; alias = "Batman"; ... }
```

This tells the toplevel to use your `pp_superhero` function whenever it
needs to display a `superhero` value. This is especially useful for abstract
types or when you want a custom representation. The function you install
must have the signature `Format.formatter -> 'a -> unit` -- which is exactly
what `[@@deriving show]` generates as `pp_*`.

## When to Use What

For debugging and development, `[@@deriving show]` is the obvious winner.
It's become such a standard part of my OCaml workflow that I barely think
about it anymore.

For user-facing output (error messages, CLI formatting, log lines), you'll
still want manual formatting with `Printf.sprintf` or `Format.asprintf`.
The derived printers produce a generic representation that's great for
debugging but not something you'd want to show end users.

And in the toplevel, make friends with `#install_printer` -- it makes
interactive exploration much more pleasant.

That's all I have for you today. Keep hacking!
