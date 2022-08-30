---
title: Learning OCaml
date: 2022-08-19 08:57 +0300
tags:
- OCaml
---

> Live as if you were to die tomorrow. Learn as if you were to live forever.
>
> -- Mahatma Gandhi

For as long as I've been into programming I've been learning some new
programming languages on the side. The more "exotic" and "niche" they were - the
better.[^1] While my professional career was mostly focused on C, C++, Java and
Ruby, over the years I've been playing to a different extent with languages like
Prolog, Common Lisp, Scheme/Racket, Clojure, Smalltalk, Erlang, Elixir, Scala,
Haskell, just to name a few. It's funny that every couple of years I revisit
Erlang and Haskell, but I always fail to truly master them. Anyways, for some
reason (laziness?) I haven't been seriously learning anything new since my last
(failed) stint with Haskell 3-4 years ago and I've decided it's time to change
this. After compiling a short list of appealing candidates[^2] I've opted for
[OCaml](https://ocaml.org) - one of the most famous members of the [ML](https://en.wikipedia.org/wiki/ML_(programming_language)) family of programming languages.

Why OCaml? One can argue that if Clojure, Erlang and Haskell are niche then OCaml is super niche and they'd probably be right. OCaml has been around since 1996 and it's unlikely that it will ever gain much traction at this point. I can't think of a single OCaml killer app.

Still, it had plenty of appeal for me:

- I've heard some praise about it from people I respect a lot - it particular that it was a really pragmatic multi-paradigm language with a lightning fast compiler
- I wanted to play with another language with a solid type system
- I was curious how it would stand up to Haskell
- I knew it had good tooling for Emacs (quite important thing for me)
- It seems that the tooling around the language has improved a lot in the past 2-3 years
- [ReasonML](https://reasonml.github.io/) generated some buzz and added fresh blood to the community a few years ago
- [OCaml 5](https://discuss.ocaml.org/t/the-road-to-ocaml-5-0/8584) is round around the corner and promises to introduce great multicore programming support
- I've historically preferred languages with "fat" runtimes (e.g. Java and Erlang), so I thought it'd be nice to opt for a language with a "slim" runtime for a change
- I was curious about OCaml's object-oriented capabilities, even though I know almost no one uses them in practice
- I was intrigued by the origin of the language in the French academic circles, as I had positive experience with Prolog in the past, and languages that originated in Europe are not that many in general

I won't deny I've got a huge fondness for Lisps and [interactive
programming](https://docs.cider.mx/cider/usage/interactive_programming.html). For
as long as it has existed, Clojure has been my favorite programming language and
I don't think that's going to change any time soon. Still, I think it's always a
good idea to occasionally go outside of your comfort zone and try something very
different.

``` ocaml
print_endline "Hello, World!"
```

I've elected to start learning OCaml using the following resources:

- [Real World OCaml](https://dev.realworldocaml.org/): A free book about OCaml, that's considered one of the best starting points in the community.
- [Cornell University's course on OCaml](https://cs3110.github.io/textbook/cover.html): It's taught at the university, but the textbook and the video lectures are freely available online.
- [OCamlverse](https://ocamlverse.github.io/): An OCaml community wiki.

So far I enjoy all the resources and I've been making steady progress with the language. In my typical style I've also made a few small improvements to the existing Emacs tooling (e.g. `tuareg-mode`, `flycheck-ocaml` and `utop.el`), as I was going along.

Going forward expect to read here some initial impressions and tips to get
started with OCaml. I'm also planning to start digging deeper in Haskell at some
point, so I can compare the languages better. We'll see if this time I'll be
more committed than in the past. I can tell you one thing, though - playing with
OCaml is a lot of fun[^3]. Keep learning!

## Learning OCaml Articles

<ul>
{% for post in site.posts reversed %}
  {% if post.tags contains 'OCaml' %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endif %}  <!-- tags if -->
{% endfor %} <!-- posts for -->
</ul>


[^1]: I've also noticed I had a strong draw to "ancient" languages with rich history.
[^2]: TypeScript, Pharo, Rust and OCaml. Clearly TypeScript and Rust weren't nice enough for me, even if they are great langauges.
[^3]: Pun intended!
