---
title: "Learning OCaml: Having Fun with the Fun Module"
date: 2025-07-19 14:20 +0300
tags:
- OCaml
- Learning OCaml
---

When I started to play with OCaml I was kind of surprised that there was no
`id` (identity) function that was available out-of-box (in `Stdlib` module,
that's auto-opened). A quick search lead me to the
[Fun](https://ocaml.org/manual/5.3/api/Fun.html) module, which is part of the
standard library and is nested under
`Stdlib`. It was introduced in OCaml 4.08, alongside other
modules such as `Int`, `Result` and `Option`.[^1]

The `Fun` module provides a few basic combinators for working with functions.
Let's go over them briefly:

- **`Fun.id`**

  ```ocaml
  Fun.id : 'a -> 'a
  ```

  The identity function: returns its input unchanged.

- **`Fun.const`**

  ```ocaml
  Fun.const : 'a -> 'b -> 'a
  ```

  Returns a function that always returns the first argument, ignoring its second argument.

- **`Fun.flip`**

  ```ocaml
  Fun.flip : ('a -> 'b -> 'c) -> 'b -> 'a -> 'c
  ```

  Reverses the order of arguments to a two-argument function.

- **`Fun.compose`**

  ```ocaml
  Fun.compose : ('b -> 'c) -> ('a -> 'b) -> 'a -> 'c
  ```

  Composes two functions, applying the second function to the result of the first. Haskell and F# have
  special syntax for function composition, but that's not the case in OCaml. (although you can easily
  map this to some operator if you wish to do so)

- **`Fun.negate`**

  ```ocaml
  Fun.negate : ('a -> bool) -> 'a -> bool
  ```

  Negates a boolean-returning function, returning the opposite boolean value. Useful when you want to provide
  a pair of inverse predicates (e.g. `is_positive` and `is_negative`)

I believe that those functions are pretty self-explanatory, but still in the next section
I'll provide some examples of how to use them in practice:

```ocaml
(* Fun.id *)
Fun.id 42
(* 42 *)

(* Fun.const *)
let always_hello = Fun.const "hello"
always_hello 12345
(* "hello" *)

(* Fun.flip *)
let subtract a b = a - b
let flipped_subtract = Fun.flip subtract
flipped_subtract 2 10
(* 8 *)

(* Fun.negate *)
let is_odd = negate is_even
is_odd 5
(* true *)

(* Fun.compose *)
let comp_f = Fun.compose (fun x -> x * x) (fun x -> x + 1)
comp_f 5
(* 36 *)
```

Admittedly the examples are not great, but I hope they managed to convey how to use
the various combinators.

Those are definitely not the type of functions that you would use every day, but they can
be useful in certain situations. Obviously I needed `id` at some point to discover the
`Fun` module in the first place, and all of the functions there can be considered as
"classic" combinators in functional programming.

Most often I need `id` and `negate`, and infrequently `comp` and `const`.
Right now I'm struggling to come up with good use-cases for `flip`, but
I'm sure those exist. Perhaps you'll share some examples in the comments?

How often do you use the various combinators? Which ones do you find most useful?

I find myself wondering if such fundamental functions shouldn't have been part of
`Stdlib` directly, but overall I really like the modular standard library approach
that OCaml's team has been working towards in the past several years.

That's all I have for you today. Keep hacking!

[^1]: It was part of some broader efforts to slim down `Stdlib` and move in the direction of a more modular standard library.
