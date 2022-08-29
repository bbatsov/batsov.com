---
title: OCaml at First Glance
date: 2022-08-29 07:31 +0300
toc: true
tags:
- OCaml
- Clojure
- Haskell
---

> You never get a second chance to make a first impression.

Lately I've been [learning OCaml]({% post_url 2022-08-19-learning-ocaml %}) and
I thought it might be a good time to share a few initial impressions, while they
are still fresh. Of course, you should take those with a grain of salt, given
that my knowledge of OCaml is still quite basic and I might be missing out
on some stuff.

Also you should keep in mind my own background, experiences and preferences:

- Over the course of almost 20 years I've been programming professionally (mostly) in C, C++, Java, Scala and Ruby. The last 10 years have been almost exclusively dedicated to Ruby.
- I've always been super passionate about programming languages and on the side I've learned to some extent about a dozen more languages (e.g. several Lisps, Haskell, Erlang, etc)
- I've got a massive fondness for the simplicity of Lisps, I've written a ton of code in Emacs Lisp and Clojure, and I really love REPL-driven (interactive/incremental) programming.
- I've never been a strong proponent of either dynamically or statically typed programming languages. I've yet to see a compelling argument in favor of one side or the other.

Obviously, all of this tends to influence a lot my perception of (new) programming languages. That's why throughout the article I'll be drawing some comparisons to other (functional) programming languages that I'm familiar with.

## General Notes

> OCaml is a general-purpose, industrial-strength programming language with an emphasis on expressiveness and safety.
>
> -- ocaml.org

The language strikes a good balance between being functional and practical. In this sense it really reminds me of Clojure on the practical side and Haskell on the somewhat impractical side (the pursuit of purity comes at a cost). OCaml's strict evaluation model makes it easier to reason than Haskell and also results in programs with more predictable performance.

## Language Syntax

Language syntax is a favorite bikeshedding topic for most programmers, so I'll do my best to stay constructive here. I think OCaml's syntax won't present much of a challenge for any seasoned programmer, especially for someone with a passing knowledge of a similar language like Haskell.

### Things I Liked

Some aspects of the OCaml syntax that I liked:

- using `;;` as expression terminator - that's useful in toplevels as it makes it easy to input a multi-line expression. It's also optional elsewhere, so it doesn't add unneeded verbosity.
- being explicit about recursive bindings. Notice the `rec` keyword here:

``` ocaml
let rec last = function
  | [] -> None
  | [ x ] -> Some x
  | _ :: t -> last t
```

- type signatures are optional
- module types
- the ability to restrict an open module to a very small scope

``` ocaml
let average x y =
  let open Int64 in
  (x + y) / of_int 2

let average x y =
  Int64.((x + y) / of_int 2)
```

- labeled arguments

``` ocaml
let div ~num ~denom = num / denom

div ~num:3 ~denom:10
```

- the ability to place the signature of a module in a dedicated `.mli` file.
- the ability to have user-defined types that look as something built-in (e.g. lists, `ref`s, etc)

``` ocaml
(* here's how you can define ref yourselves *)
type 'a ref = { mutable contents : 'a }

let ref x = { contents = x }

let (!) r = r.contents

let (:=) r x = r.contents <- x
```

- having handy functions like `|>` out-of-the-box (I'm quite used to this from Clojure)

``` ocaml
some_list |> List.filter is_even |> sum
```

By the way, there's nothing really special about `|>`, it's a function like any other. One of the nice aspects of OCaml's syntax in general.

- the usages of `snake_case` instead of `camelCase` most of the time. I find `snake_case` identifiers to be more readable, although obviously that's super subjective.

### Things I Disliked

Some aspects of the syntax that I disliked or found confusing (at least initially):

- using `(* *)` for comments and lack of line comments. Not the end of the world, but the comment syntax makes some things some things trickier (e.g. referring to the `*` operator) and is just too verbose.

``` ocaml
(* this will work *)
List.fold_left (+) 0 [1;2;3;4;5]

(* this will work *)
List.fold_left ( * ) 1 [1;2;3;4;5]

(* this will not work *)
List.fold_left (*) 1 [1;2;3;4;5]
```

Here's one more comment that won't work - `(* " *)`. I'll leave to the curious reader to figure out why that's the case.

- using `;` heavily as element separator (e.g. in lists and records):

``` ocaml
let some_list = [1; 2; 3; 4; 5]
```

You get used to this, but it's a pretty big departure from the norm to use commas. I wonder what's the reasoning behind this. Most likely it has to do with being able to define tuples without parentheses around them:

``` ocaml
(1, 2, 3);;
(* - : int * int * int = (1, 2, 3) *)

1, 2, 3;;
(* - : int * int * int = (1, 2, 3) *)

(* a more realistic example of the usefulness of this *)
let a, b, c = 1, 2, 3 in
a + b + c

(* but we pay a steep price for the above convenience *)
[1, 2, 3];;
(* - : (int * int * int) list = [(1, 2, 3)] *)
```

- introducing [multiple `let` bindings]({% post_url 2022-08-28-ocaml-tips-multiple-let-bindings %}) is a bit too verbose for my taste
- no syntax for list comprehensions (I often use those in Clojure, Haskell and Erlang, although I'm well aware they are not something essential)
- the syntax for array literals is somewhat weird (`[|1; 2; 3|]`) - I don't like such usage of multiple symbols as literal boundaries, as this makes it harder for dev tools to figure out what you're doing. The indexing syntax `arr.(i)` is slightly weird as well, as it doesn't fit very well with the rest of the language.
- no first-class syntax for maps and sets (Clojure spoiled on this front)

``` clojure
;; Clojure map
{:name "Bruce Wayne" :alias "Batman"}

;; Clojure set
#{1 2 3 4 5}
```

- nothing like Haskell's typeclasses, which means in some cases you get different operators for similar types (e.g. numeric types):

``` ocaml
(* int version *)
let average a b =
    (a + b) / 2.0

(* float version *)
let average a b =
    (a +. b) /. 2.0
```

On the bright side - it's really easy to spot float operations.

By the way, this also means you get different operators for things like string and
list concatenation, as you can't use `+` which is defined in terms of integers:

``` ocaml
"Hello, " ^ "world!"

[1; 2] @ [3; 4]
```

### Syntax Summary

> This too shall pass.
>
> -- Ancient Persian adage

Overall, there's not much to write home about when it comes to OCaml's syntax - it certainly won't shock anyone the way Lisp or Prolog would. OCaml has a very long legacy as part of the ML-family of languages and I guess it accumulated a few syntactic oddities over time. I don't expect any changes to happen and I got used to whatever I considered to be "weird" fairly quickly.

## OCaml Platform (Compiler, Libraries, Frameworks)

In brief:

- the compiler is super fast, but that was expected given that OCaml is famous for it.
- the compiler is also quite helpful, although its messages can definitely be more descriptive in some cases.
- the standard library is quite basic and that's probably my main gripe with the language so far - e.g. the support for things like string manipulation (the standard library doesn't support Unicode strings) and regular expressions is abysmal given what you'd get with most other languages today.
- the above has spurred the creation of all sorts of extensions and replacements to the standard library, which creates a lot of learning overhead and fragmentation. I was amused to see that the popular book [Real World OCaml](https://dev.realworldocaml.org/) directly recommends replacing the standard library with a third-party alternative.
- the package manager [Opam](https://opam.ocaml.org) is relatively easy to use, although using switches (isolated OCaml environments) takes a while to get used to. I'm still not sure that should be part of the responsibilities of a package manager, but it gets the job done. Opam could use some better [documentation](https://opam.ocaml.org/doc/Manual.html).

There's also support to target JavaScript from OCaml sources, which looks interesting, but I haven't tried it yet. [ReasonML](https://reasonml.github.io/) (basically OCaml with a "more familiar" syntax) is also appealing in this space, but I hear that the project has lost traction and has been on the decline lately.

There are plenty of third-party OCaml libraries, although most of them seem somewhat under-maintained and under-documented. I've noticed that for many libraries all the documentation you'll find is just API signatures.

The only framework I looked into was [Dream](https://aantron.github.io/dream/) (a web framework). It looked pretty nice, but it's still in alpha, has only one developer and it seems he has been not been very active lately. Oh, well - that's a common problem in smaller programming communities.

## Development Tooling

I'd say the development tooling for OCaml is pretty decent, although I wouldn't go as far as saying it's great. I've documented my current Emacs setup [here]({% post_url 2022-08-23-setting-up-emacs-for-ocaml-development %}) and I've also played a bit with the "official" VS Code extension to get a feel for OCaml-LSP.

Without a doubt, [Merlin](https://ocaml.github.io/merlin/) is the primary workhorse of the development tools and it's definitely pretty awesome. It provides editor features like code completion, typing information, navigation to definition, refactoring, code generation, linting, etc. It's a beast! Emacs's `merlin-mode` reminds me in a way of CIDER (for Clojure).

[utop](https://github.com/ocaml-community/utop/) is a good toplevel (REPL) and has become a major part of my OCaml toolbox. It'd be nice if down the road someone improves a bit the default `ocaml` toplevel, which is extremely basic by every modern standard.

[Dune](https://dune.build/) is a solid build tool and I definitely enjoyed working with it. It does need better documentation, though, as I learned more about it (and Opam) from third-party documentation instead of from their official documentation.

I think for most people a combination of VS Code + a `utop` and `dune` running in dedicated terminals will provide the optimal development experience. Of course, if you can stomach Emacs - even better. ;-)

## Community

The OCaml community is tiny by the standards of more popular programming languages, but I really enjoyed all my interactions with its members. I've been mostly using the official [Discord](https://discord.gg/cCYQbqN) and [Discourse](https://discus.ocaml.org), but there are plenty of [other options](https://ocaml.org/community) for you to chose form.

I've gotten plenty of answers to every question I asked and I really enjoyed learning more about [the workflows OCaml developers have](https://discuss.ocaml.org/t/whats-your-development-workflow/10358), as finding a productive workflow was one of the things I've struggled with early on.[^1]

I also learned a lot simply be handing on there and perusing the topics that we being posted and channels like `#general`, `#beginners` and `#share`.

One final community resource I'd like to recommend is the awesome wiki [OCamlverse](https://ocamlverse.github.io/). It complements really well the information on the official OCaml site.

## Learning Resources

You certainly won't find dozens of books, hundreds of tutorials and a few online courses for OCaml. It seems that the most popular resources to learn OCaml are:

- [Real World OCaml](https://dev.realworldocaml.org/): A free book about OCaml, that's considered one of the best starting points in the community. On the flip side - it's somewhat controversial for its decision to advocate the use of an alternative standard library (`Base` from Jane Street).
- [Cornell University's course on OCaml](https://cs3110.github.io/textbook/cover.html): It's taught at the university, but the textbook and the video lectures are freely available online. I enjoyed the course, although it's clearly geared towards students and not practicing programmers.
- [OCamlverse](https://ocamlverse.github.io/): An OCaml community wiki. The articles there give you a lot of practical pointers about day-to-day OCaml programming and complement nice the tutorial/manual texts.

There's also some [tutorial-like section](https://ocaml.org/docs/up-and-running) on the official site, but it could have been structured better in my opinion.

I definitely think there's a lot of room for more resources for newcomers and
that's one area that will need work if OCaml is grow its mindshare. When everyone in the community is discussing the merits of a single book (RWO), you immediately figure out the problem is not the contents of the book, but rather the lack of alternative books.

While Clojure and Haskell are considered niche languages as well, there are a lot more learning resources of every flavor for them. I think that even Erlang has more resources than OCaml these days.

## A Closing Note About Libraries

A while ago I made a comment about the standard library situation [on Discourse](https://discuss.ocaml.org/t/what-i-dislike-about-ocaml/10248/50?u=bbatsov) that triggered a a follow-up conversation about two things:

- that the standard library wasn't a major focus for OCaml's team and they prefer for libraries to be developed independently by experts in a particular area (fair enough)
- that it's actually easy to find the right libraries for each problem (it was mostly focused on strings)

While, I can agree with the first point in principle, I'd still expect at least a set of well-known libraries to be widely recommended, etc. Digging for libraries while learning a language is not always fun and sometimes leads in the wrong direction. On the second point - I was amused to see back-to-back comments that were contradicting one another. First I got the follow suggestion:

> For example, for Unicode support you need to decide between Camomile and `uutf`, depending on your needs.

And then it got this response:

> I'm not sure I understand this comment. I don't think there's anything to decide between the two things you mention.
>
> First the only thing that uutf provides, namely UTF codecs is nowaday available in `Stdlib.Buffer` and `Stdlib.String`. Except if you rely on the non-blocking stuff, which most people don't, there's no reason to use uutf anymore. People should move away from it and consider it deprecated.
>
> Second if you need to access Unicode character data or perform normalisations, AFAIK Camomile has not updated its Unicode data for quite some time. Last time I looked it was Unicode 3.2 which was released in 2002, twenty years ago.
>
> Since then thousands of characters, dozen of scripts and few new characters properties have been added. This makes Camomile entirely impractical to deal with the Unicode of today.
>
> So I don't think there's any choice to be had here. You will be better served by `uucp`, `uunf` and `uuseg` (this one not provided by Camomile).[^2]

And finally the acknowledgment:

> Exactly the kind of thing newcomers or casual users would absolutely never know unless they did a lot of research.

Indeed! Clearly finding the right libraries is not trivial in this case. Hopefully the situation will be improved with time.

## Conclusion

I can't say that my initial experience with OCaml was mind-blowing, but it was definitely pleasant and with time the language grows on me. I miss the simplicity and uniformity of Clojure (still my favorite programming language by far) or some of Haskell's goodies (e.g. typeclasses, [Hackage](https://hackage.haskell.org/) and `ghci`), but I feel OCaml strikes a very good balance between functional programming, pragmatism and performance.

At this point I've seen enough to be convinced to continue with my exploration of OCaml and dig deeper. Keep hacking!

[^1]: Pro tip - `dune runtest -w --no-buffer` is essential!
[^2]: Don't even get me started on names like `uucp`, `uund` and `uuseg`. :-) Naming continues to be hard!
