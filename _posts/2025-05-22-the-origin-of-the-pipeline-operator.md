---
title: "The origin of the pipeline operator (`|>`)"
date: 2025-05-22 13:49 +0300
tags:
- F#
- OCaml
- Elixir
---

These days a lot of programming languages (especially those leaning towards functional programming)
offer a pipeline operator (|>), that allows you to feed some data through a "pipeline" of transformation
steps.[^1] Here's a trivial example in F#:

```fsharp
let processNumbers numbers =
    numbers
    |> List.filter (fun x -> x % 2 = 0)        // keep even numbers
    |> List.map (fun x -> x * x)               // square them
    |> List.sum                                // sum the result
```

I think I first saw the pipeline operator in Elixir and afterwards in OCaml.
When I started to play with F# recently, I learned that F# was widely credited as
the language that made the pipeline operator popular. While reading the excellent paper
[The Early History of F#](https://fsharp.org/history/hopl-final/hopl-fsharp.pdf) I learned
even more on the topic. Below, you'll find the relevant excerpt from the paper.

--------------

One of the first things to become associated with F# was also one of the simplest: the “pipe-forward”
operator, added to the F# standard library in 2003:

```fsharp
let (|>) x f = f x
```

In conjunction with curried function application this allows an intermediate result to be passed
through a chain of functions, e.g.

```fsharp
[ 1 .. 10 ]
    |> List.map (fun x -> x *x)
    |> List.filter (fun x -> x % 2 = 0)
```

instead of

```fsharp
List.filter (fun x -> x % 2 = 0)
    (List.map (fun x -> x *x) [ 1 .. 10 ])
```

Despite being heavily associated with F#, the use of the pipeline symbol in ML dialects actually
originates from Tobias Nipkow, in May 1994 (with obvious semiotic inspiration from UNIX pipes)
[archives 1994; Syme 2011].

> ... I promised to dig into my old mail folders to uncover the true story behind |> in Isabelle/ML, which
> also turned out popular in F#...
> In the attachment you find the original mail thread of the three of us [ Larry Paulson; Tobias Nipkow;
> Marius Wenzel], coming up with this now indispensable piece of ML art in April/May 1994. The mail
> exchange starts as a response of Larry to my changes.
> ...Tobias ...came up with the actual name |> in the end...

--------------

Note that in F# the `|>` operator is called "pipe-forward" mostly because they have
several variations of it, including a pretty confusing "pipe-backward" operator.

Before reading this article I had never heard of Isabelle/ML, and I had never realized
that `|>` is somewhat modeled after the `|` (pipe) operator in Unix shells. I guess the
reason for this is that I first got exposed to a similar concept in Clojure, where
there `->` (thread-first) and `->>` (thread-last) macros serve a pretty similar purpose.
While there are no "pipes" in them, I think the fundamental idea is pretty much the same.
Still, it was OCaml and F# that made me really appreciate the "real" pipeline operator
and develop a deep fondness for it.

That's all I have for you today! Keep hacking!

[^1]: Funny enough, even Ruby has a pipeline operator these days, although it's not particularly useful there.
