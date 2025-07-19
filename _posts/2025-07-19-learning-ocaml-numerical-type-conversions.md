---
title: 'Learning OCaml: Numerical Type Conversions'
date: 2025-07-19 11:12 +0300
tags:
- OCaml
- Learning OCaml
---

Today I'm going to cover a very basic topic - conversions between
OCaml's primary numeric types `int` and `float`. I guess most of you
are wondering if such a basic topic deserves a special treatment, but
if you read on I promise that it will be worth it.

So, let's start with the basics that probably everyone knows:

- you can convert integers to floats with `float_of_int`
- you can convert floats to integers with `int_of_float`

```ocaml
int_of_float 10.5;;
- : int = 10
float_of_int 9;;
- : float = 9
```

Both functions live in `Stdlib` module, which is opened by default in OCaml.
Here it gets a bit more interesting. For whatever reasons there's
also a `float` function, that's a synonym to `float_of_int`. There's
no `int` function, however. Go figure why...

More interestingly, OCaml 4.08 introduced the modules `Int` and `Float`
that bring together common functions for operating on integers and floats.[^1]
And there are plenty of type conversion functions in those modules as well:

- `Int.to_float` and `Int.of_float`
- `Float.of_int` and `Float.to_int`
- `Int.to_string` and `Float.to_string`

The introduction of the `Int` and `Float` modules was part of (ongoing)
effort to make OCaml's library more modular and more useful. I think
that's great and I hope you'll agree that most of the time it's a
better idea to use the new modules instead of reaching to the
"historical" functions in the `Stdlib` module. Sadly, most OCaml
tutorials out there make no mention of the new modules, so I'm hoping that
my article (and tools like ChatGPT) will steer more people in the right direction.

If you're familiar with Jane Street's `Base`, you'll probably notice that
it also employs similar structure when it comes to integer and float functionality.

Which type conversion functions do you prefer? Why?

That's all I have for you today. Keep hacking!

[^1]: Technically speaking, `Float` existed before 4.08, but it was extended then.
4.08 also introduced the modules `Bool`, `Fun`, `Option` and `Result`. Good stuff!
