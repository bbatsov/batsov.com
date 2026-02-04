---
title: 'neocaml: a new Emacs package for OCaml programming'
date: 2025-03-14 10:01 +0200
tags:
- OCaml
- Emacs
---

I wasn't an early adopter of [TreeSitter in Emacs](https://www.masteringemacs.org/article/how-to-get-started-tree-sitter), as usually such
big transitions are not smooth and the initial support for TreeSitter in
Emacs left much to be desired. Recently, however, Emacs 30 was released with many
improvements on that front, and I felt the time was right for me to (try to) embrace
TreeSitter.

I'm the type of person who likes to learn by deliberate practice, that's why I
wanted to do some work on TreeSitter-powered major modes. I've already been a
co-maintainer of
[clojure-ts-mode](https://github.com/clojure-emacs/clojure-ts-mode) for a while
now, and I picked up the basics around it, but I didn't spend much time hacking
on it until recently. After spending a bit more time studying the current
implementation of `clojure-ts-mode` and the various Emacs TreeSitter APIs, I decided to
start a new experimental project from scratch -
[neocaml](https://github.com/bbatsov/neocaml), a TreeSitter-powered package for
OCaml development.[^1]

Why did I start a new OCaml package, when there are already a few existing out
there? Because `caml-mode` is ancient (and probably has to be deprecated), and
`tuareg-mode` is a beast. (it's very powerful, but also very complex) The time
seems ripe for a modern, leaner, TreeSitter-powered mode for OCaml.

There have been two other attempts to create TreeSitter-powered
major modes for Emacs, but they didn't get very far:

- [ocaml-ts-mode](https://github.com/dmitrig/ocaml-ts-mode) (first one, available in MELPA)
- [ocaml-ts-mode](https://github.com/terrateamio/ocaml-ts-mode) (second one)

Looking at the code of both modes, I inferred that the authors were probably knowledgable in
OCaml, but not very familiar with Emacs Lisp and Emacs major modes in general.
For me it's the other way around, and that's what makes this a fun and interesting project for me:

- I enjoy working on Emacs packages
- As noted above I want to do more work TreeSitter
- I really like OCaml and it's one of my favorite "hobby" languages

One last thing - we really need more Emacs packages with fun names! :D Naming is hard, and I'm
notorious "bad" at it![^2]

They say that third time's the charm, and I hope that `neocaml` will get farther than
the other `ocaml-ts-mode`s. Time will tell!

I've documented the code extensively inline, and in the README you'll find my development notes detailing
some of my decisions, items that need further work and research, etc. If nothing else - I think
anyone can learn a bit about how TreeSitter works in Emacs and what are the common challenges
that one might face when working with it. To summarize my experience so far:

- font-locking (syntax highlighting) with TreeSitter is fairly easy
- structured navigation seems reasonably straight-forward as well
- the indentation queries are a bit more complicated, but they are definitely not black magic

Fundamentally, the main problem is that we still don't have
easy ways to try out TreeSitter queries in Emacs, so there's a lot of trial and error involved. (especially when it
comes to indentation logic) My other big problem is that most TreeSitter grammars
have pretty much no documentation, so one has to learn about their AST format
via experimentation (e.g. `treesit-explore-mode` and `treesit-inspect-mode`) and
reading their bundled queries for font-locking and indentation. As someone who's
used to work with Ruby parser I really miss the docs and tools that come with
something like the Ruby [parser](https://github.com/whitequark/parser) library.

What's the state of project right now? Well, `neocaml` kind of works right now,
but the indentation logic needs a lot of polish, and I've yet to implement
properly structured navigation and some of the newer Emacs TreeSitter APIs
(e.g. `things`).  We can always "cheat" a bit with the indentation, by
delegating it to another Emacs package like `ocp-indent.el` or even Tuareg, but
I'm hoping to come up with a self-contained TreeSitter implementation in the end
of the day.

If you're feeling adventurous you can easily install the package like this:

    M-x package-vc-install <RET> https://github.com/bbatsov/neocaml <RET>

In Emacs 30 you can you `use-package` to both install the package from GitHub
and configure it:

```emacs-lisp
(use-package neocaml
  :vc (:url "https://github.com/bbatsov/neocaml" :rev :newest))
```

**Note:** `neocaml` will auto-install the required TreeSitter grammars the
first time one of the provided major modes is activated.

Please refer to the README for usage information, like the various configuration
options and interactive commands.

I'm not sure how much time I'll be able to spend working on `neocaml` and how far
will I be able to push it.  Perhaps it will never amount to anything, perhaps it
will just be a research platform to bring TreeSitter support to Tuareg. And
perhaps it will become a viable simple, yet modern solution for OCaml
programming in Emacs. The dream is alive!

Contributions, suggestions and feedback are most welcome. Keep hacking!

[^1]: I didn't name it `neocaml-mode` intentionally - many Emacs packages contain more things
    than just major modes, so I prefer a more generic naming.

[^2]: On a more serious note - there was never an `ocaml-mode`, so naming something `ocaml-ts-mode` is not
    strictly needed. But I think an actual `ocaml-mode` should be blessed by the the maintainers of OCaml,
    hosted in the primary GitHub org, and endorsed as a recommended way to program in OCaml with Emacs.
    Pretty tall order!
