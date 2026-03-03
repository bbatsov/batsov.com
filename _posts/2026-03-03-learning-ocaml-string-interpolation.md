---
title: 'Learning OCaml: String Interpolation'
date: 2026-03-03 12:00 +0200
tags:
- OCaml
- Learning OCaml
---

Most programming languages I've used have some form of string
interpolation. Ruby has `"Hello, #{name}!"`, Python has f-strings,
JavaScript has template literals, even Haskell has a few popular
interpolation libraries. It's one of those small conveniences you don't
think about until it's gone.

OCaml doesn't have built-in string interpolation. And here's the funny
thing -- I didn't even notice when I was first learning the language. Looking
back at my [first impressions]({% post_url 2022-08-29-ocaml-at-first-glance %})
article, I complained about the comment syntax, the semicolons in lists, the
lack of list comprehensions, and a dozen other things -- but never once about
string interpolation. I was happily concatenating strings with `^` and using
`Printf.sprintf` without giving it a second thought.

I only started thinking about this while working on my
[PPX article]({% post_url 2026-03-03-ppx-for-mere-mortals %}) and going
through the catalog of popular PPX libraries. That's when I stumbled upon
`ppx_string` and thought "wait, why *doesn't* OCaml have interpolation?"

<!--more-->

## Why OCaml Doesn't Have String Interpolation

The short answer: OCaml has no way to generically convert a value to a
string. There's no universal `to_string` method, no `Show` typeclass, no
runtime reflection that would let the language figure out how to stringify
an arbitrary expression inside a string literal.

In Ruby, every object responds to `.to_s`. In Python, everything has
`__str__`. These languages can interpolate anything because there's always
a fallback conversion available at runtime. OCaml's type information is
erased at compile time, so the compiler would need to know *at compile
time* which conversion function to call for each interpolated expression --
and the language has no mechanism for that.[^1]

OCaml does have `Printf.sprintf`, which is actually quite nice and
type-safe:

```ocaml
let greeting name age =
  Printf.sprintf "Hello, %s! You are %d years old." name age
```

The format string `"%s ... %d"` is statically checked by the compiler --
if you pass an `int` where `%s` expects a string, you get a compile-time
error, not a runtime crash. That's genuinely better than what most
dynamically typed languages offer. But it's not interpolation -- the
values aren't inline in the string, and for complex expressions it gets
unwieldy fast.

There's also plain string concatenation with `^`:

```ocaml
let greeting name age =
  "Hello, " ^ name ^ "! You are " ^ string_of_int age ^ " years old."
```

This works, but it's ugly and error-prone for anything beyond trivial cases.

## ppx_string to the Rescue

[ppx_string](https://github.com/janestreet/ppx_string) is a Jane Street PPX
that adds string interpolation to OCaml at compile time. The basic usage is
straightforward:

```ocaml
let greeting name =
  [%string "Hello, %{name}!"]
```

For non-string types, you specify the module whose `to_string` function
should be used:

```ocaml
let status name age score =
  [%string "Player %{name}, age %{age#Int}, score %{score#Float}"]
```

The `#Int` suffix tells the PPX to call `Int.to_string` on `age`, and
`#Float` calls `Float.to_string` on `score`. Any module that exposes a
`to_string` function works here -- including your own:

```ocaml
module Player = struct
  type t = { name : string; score : int }
  let to_string p = Printf.sprintf "%s (%d)" p.name p.score
end

let announce player =
  [%string "Next up: %{player#Player}"]
```

You can also use arbitrary expressions inside the interpolation braces:

```ocaml
let summary items =
  [%string "Found %{List.length items |> Int.to_string} items"]
```

Though at that point you might be better off with a `let` binding or
`sprintf` for readability.

### Tips and Limitations

A few practical things worth knowing:

**You need the `pps` stanza in your dune file:**

```dune
(library
 (name mylib)
 (preprocess (pps ppx_string)))
```

**String values interpolate directly, everything else needs a conversion
suffix.** Unlike Ruby where `.to_s` is called implicitly, `ppx_string`
requires you to be explicit about non-string types. This is annoying at
first, but it's consistent with OCaml's philosophy of being explicit about
types.

**It's a Jane Street library.** If you're already in the Jane Street
ecosystem (`Core`, `Base`, etc.), adding `ppx_string` is trivial. If you're
not, pulling in a Jane Street dependency just for string interpolation might
feel heavy. In that case, `Printf.sprintf` is honestly fine.

**It doesn't work with the `Format` module.** If you're building strings for
pretty-printing, you'll still want `Format.fprintf` or `Format.asprintf`.
`ppx_string` is for building plain strings, not format strings.

**Nested interpolation doesn't work** -- you can't nest `%{...}` inside
another `%{...}`. Keep it simple.

## Do You Actually Need String Interpolation?

Honestly? Probably not as much as you think. I've been writing OCaml for a
while now without it, and it rarely bothers me. Here's why:

- **`Printf.sprintf` is good.** It's type-safe, it's concise enough for most
  cases, and it's available everywhere without extra dependencies.
- **Most string building in OCaml happens through `Format`.** If you're
  writing pretty-printers (which you will be, thanks to `[@@deriving show]`),
  you're using `Format.fprintf`, not string concatenation or interpolation.
- **OCaml code tends to be more compute-heavy than string-heavy.** Compared
  to, say, a Rails app or a shell script, the typical OCaml program just
  doesn't build that many ad-hoc strings.

That said, when you *do* need to build a lot of human-readable strings --
error messages, log output, CLI formatting -- interpolation is genuinely
nicer than `sprintf`. If you're in the Jane Street ecosystem, there's no
reason not to use `ppx_string`.

## Closing Thoughts

The lack of string interpolation in OCaml is one of those things that sounds
worse than it actually is. In practice, `Printf.sprintf` and `Format` cover
the vast majority of use cases, and the code you write with them is arguably
clearer about types than magical interpolation would be.

It's also a nice example of OCaml's general philosophy: keep the language
core small, provide solid primitives (`Printf`, `Format`), and let the PPX
ecosystem fill in the syntactic sugar for those who want it. The same pattern
plays out with `[@@deriving show]` for printing, `ppx_let` for monadic
syntax, and many other conveniences.

Will OCaml ever get built-in string interpolation? Maybe. There have been
discussions on the forums over the years, and the language did absorb binding
operators (`let*`, `let+`) from the PPX world. But I wouldn't hold my breath
-- and honestly, I'm not sure I'd even notice if it landed.

That's all I have for you today. Keep hacking!

[^1]: This is the same fundamental problem that makes [printing data structures]({% post_url 2026-03-01-printing-data-in-ocaml %}) harder than in dynamically typed languages.
