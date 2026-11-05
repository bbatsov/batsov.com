---
layout: post
title: 'Neocaml 0.11: OxCaml, Indentation Styles, and odoc'
date: 2026-11-05 07:30 +0200
tags:
- OCaml
- Emacs
---

I'm happy to announce that [neocaml 0.11][release] is out! I never got around
to writing about 0.10, which was mostly about `neocaml-utop` (a [`utop`][utop]
backend that talks to `utop` via its native editor protocol), but 0.11 is a
different story. Its changelog is twice as long as that of any release before
it, and by now I've stopped pretending that a grand 1.0 is just around the
corner.[^1] There's a lot in there, but three things stand out for me - OxCaml
support, indentation styles, and much better support for documentation. Here's
why...

<!--more-->

## OxCaml

[OxCaml][oxcaml] is Jane Street's set of extensions to OCaml - modes and
modalities, kinds, unboxed types, stack allocation and so on. You can use them
today with an OxCaml compiler switch, and Jane Street's libraries use them all
over the place. A lot of the heavy lifting happened upstream, as
[`tree-sitter-ocaml`][tree-sitter-ocaml] 0.26 learned to parse OxCaml, and
[Tim McGilchrist][tim] bumped neocaml's grammars to it. [Pieter
Goetschalckx][314eter] (the `tree-sitter-ocaml` maintainer) once again cut an
[ABI 14 build][abi14] of the release, so we didn't have to break anyone's
Emacs. (see the [0.9 post]({% post_url 2026-06-24-neocaml-0-9-repl-completion-and-robustness %})
for the whole ABI saga) Huge thanks to both of them!

A grammar that parses the code is only half of the job, though. Here's a bit of
OxCaml:

```ocaml
type point : immutable_data = { x : float; y : float }

type vec = #{ dx : float#; dy : float# }

let length (xs @ local) = List.length xs

let make () : int list @ local = exclave_ [ 1; 2; 3 ]
```

neocaml now highlights and indents all of this. The new keywords (`stack_`,
`exclave_`, `local_`, `global_`, `borrow_` and `kind_`), modes and modalities,
kinds, unboxed types and literals (`float#`, `#1.0`, `#(...)`), immutable
arrays (`[: ... :]`) and comprehensions are all covered. The indentation
follows the way [`ocamlformat`][ocamlformat] lays out Jane Street's own OxCaml
libraries (e.g. `portable`, `unique` and `parallel`), which is why the body of
an `exclave_` isn't indented, while those of `stack_` and `local_` are.[^2]
Kind definitions also show up in imenu and work with the defun navigation
commands.

If you don't write OxCaml, nothing changes for you - plain OCaml looks exactly
the way it did before. If you do, there's nothing to turn on in neocaml. Just
make sure Emacs sees your OxCaml switch and you'll get its [`ocamllsp`][ocamllsp]
and `ocamlformat` as well. The [docs][oxcaml-docs] have all the details.

## Indentation styles

Here's a confession - neocaml's indentation used to be a bit of a mix. Some
layouts followed `ocamlformat`'s `conventional` profile, some followed
`janestreet`, and a few followed neither. That's a problem, as OCaml doesn't
have one indentation style everyone follows. `ocamlformat` ships several
profiles, Jane Street's code doesn't look like the compiler's, and plenty of
projects never run a formatter at all.

So 0.11 lets you pick one of three styles via `neocaml-indent-style`:

- `ocp-indent`, which indents the way [`ocp-indent`][ocp-indent] does out of the box
- `conventional`, after `ocamlformat`'s default profile (used by ocaml.org, ppxlib and Irmin)
- `janestreet`, after `ocamlformat`'s `janestreet` profile (used by Jane Street's libraries, [`dune`][dune] and `ocaml-lsp`)

They agree on most things and differ on a handful of layouts, e.g. how far the
body of a match case goes:

```ocaml
(* ocp-indent and janestreet *)
match value with
| Some payload ->
  finish payload
| None -> 0

(* conventional *)
match value with
| Some payload ->
    finish payload
| None -> 0
```

The best part is that most of the time you won't have to pick a style yourself.
By default neocaml looks for the project's `.ocamlformat` and picks the style
that matches its `profile` (it also respects `cases-exp-indent`). Without an
`.ocamlformat` it checks for an `.ocp-indent` file, and without either you get
the `ocp-indent` style, as that's what most projects that don't use
`ocamlformat` indent with. (e.g. [`opam`][opam], Rocq, Frama-C, Eio and the
compiler itself) You can
also set the style per project in `.dir-locals.el`, or switch it for the
current buffer with `M-x neocaml-set-indent-style`.

I also wanted to know how well all of this works on real code, instead of
guessing. So I wrote a couple of small scripts (they live in `dev/`) that format
a bunch of OCaml files with each `ocamlformat` profile and count the lines
neocaml would indent differently. I ran them on the almost 300 files of the
`opam` packages in my default switch (mostly Jane Street's `base`), and 0.10
didn't look great. About one line in ten had no indentation rule at all, so
`TAB` simply left it wherever you typed it, and only 81.9% of the lines landed
where `ocamlformat`'s `conventional` profile puts them (77.8% for `janestreet`).
Ouch! With the matching style, 0.11 gets 97.5% and 98% of them right.[^3] Not
perfect, but pretty close!

Keep in mind that indentation only places the lines you've broken, it doesn't
break them for you. If you run `ocamlformat` on save it has the final word
anyway, but with the matching style less of what you type moves around when
you save.

## odoc and doc comments

The third big theme is documentation. A few months ago [mlemerre][mlemerre]
suggested ([#60][issue-60]) that neocaml should render the `ocamldoc` markup in
comments, instead of showing it as a soup of braces. I loved the idea, and in
0.11 doc comments finally look like documentation - `{b bold}` is bold, `{i
italic}` is italic, `[code]`, `{[ ... ]}` and `{@ocaml[ ... ]}` are shown as
code, and `{!references}` and tags like `@param` get faces of their own. This
is the new `doc-markup` font-lock feature, and it's enabled at the default
font-lock level.

Tim also contributed `neocaml-odoc-mode` ([#46][pr-46]) for [`odoc`][odoc]'s
standalone `.mld` documentation pages, built on Tim's own [Tree-sitter grammar
for `odoc`][odoc-grammar]. You get font-lock for headings, inline markup,
references, links, tags, lists and tables, plus indentation and imenu. On
Emacs 30+ the code in `{@ocaml[...]}`, `{@dune[...]}` and `{@opam[...]}` blocks
is highlighted as well, using the grammars for those languages. Run `M-x
neocaml-odoc-install-grammar` to get started, and check out the
[docs][odoc-docs] for more.

While I was at it, I also fixed `M-q` and `M-j` in comments. Embarrassingly,
`M-q` used to always fill the first paragraph of a comment, no matter where you
were in it, and it merged code blocks into the text around them. `M-j` now
continues the `*` of comments that start each line with one.

## Grab Bag

Those are just the highlights, of course. Here are a few more things that I
think you'll like:

- `neocaml-mlx-mode` for `.mlx` files ([OCaml with JSX][mlx], as used by
  [Melange][melange] and React), contributed by [Akira Komamura][akira]
- commands for running the tests of the current file and dealing with `dune`'s
  promotions (`C-c C-o T`, `C-c C-o =` and `C-c C-o P`), with their own
  bindings in `neocaml-cram-mode`
- the toplevel's responses in the REPL are now highlighted with Tree-sitter,
  just like your code
- each `dune` project in a monorepo gets its own REPL
- `neocaml-format-buffer` replaces only the text that changed, so point and
  the window positions stay put

## Upgrading

Most setups will keep working just fine, but a few changes in this release
require some action on your part:

- neocaml needs `tree-sitter-ocaml` 0.26 now, so reinstall the OCaml grammars
  with `C-u M-x neocaml-install-grammars`. The `dune` grammar moved to its new
  home, [`ocaml-dune/tree-sitter-dune`][dune-grammar], so reinstall it as well
  with `C-u M-x neocaml-dune-install-grammar`. neocaml will warn you if your
  grammars are too old.
- Some keybindings clashed with [`ocaml-eglot`][ocaml-eglot] and
  [`merlin`][merlin], so they had to move. In
  the REPL minor modes sending the phrase at point is now `C-c C-e`, sending it
  and moving to the next one `C-c C-s`, loading the file `C-c C-u` and
  interrupting the toplevel `C-c C-q`. The `dune` commands moved from `C-c C-d`
  to `C-c C-o`.
- The default indentation changed. If your project uses neither `ocamlformat`
  nor `ocp-indent` a few layouts will move, so set `neocaml-indent-style` if the
  `ocp-indent` style is not your cup of tea.

## Epilogue

With OxCaml, mlx and `odoc` on board, neocaml now covers pretty much every kind
of OCaml file I can think of. Famous last words, right?

So, what's next? I don't really have any big plans for 0.12. I'd like to switch
`neocaml-mlx-mode` to the dedicated grammar from
[`tree-sitter-mlx`][tree-sitter-mlx] once it has an ABI 14 release, and there's
the usual pile of smaller ideas in the [roadmap][roadmap]. No promises, though.

Big thanks to [Tim McGilchrist][tim], who did a ton of work on the grammars
(OCaml, `dune`, `opam` and [`menhir`][menhir]) and the `odoc` support! Thanks
to [Akira Komamura][akira] for bringing mlx to neocaml, and to [Alessio
Duè][alessio] for fixing a sexp navigation bug! And thanks to everyone who reported problems
and shared ideas - many of the improvements in 0.11 started as an issue on
GitHub.

As usual - update from [MELPA](https://melpa.org/#/neocaml), play with it, and
let me know how it goes. The full list of changes is in the [release
notes][release].

That's all I have for you today. Keep hacking!

[^1]: See the [0.6 post]({% post_url 2026-03-25-neocaml-0-6-opam-dune-and-more %}) for how that went the last time.

[^2]: That's how `ocamlformat` formats Jane Street's code, and I've learned to trust it more than my own intuition here.

[^3]: That's about 77,000 lines formatted with `ocamlformat` 0.27.0. The numbers depend on the code and the ocamlformat version, so take them with a grain of salt.

[release]: https://github.com/bbatsov/neocaml/releases/tag/v0.11.0
[utop]: https://github.com/ocaml-community/utop
[tree-sitter-ocaml]: https://github.com/tree-sitter/tree-sitter-ocaml
[ocamlformat]: https://github.com/ocaml-ppx/ocamlformat
[ocamllsp]: https://github.com/ocaml/ocaml-lsp
[dune]: https://dune.build/
[opam]: https://opam.ocaml.org/
[odoc]: https://github.com/ocaml/odoc
[mlx]: https://github.com/ocaml-mlx/mlx
[melange]: https://melange.re/
[ocaml-eglot]: https://github.com/tarides/ocaml-eglot
[merlin]: https://github.com/ocaml/merlin
[menhir]: https://gallium.inria.fr/~fpottier/menhir/
[oxcaml]: https://oxcaml.org/
[oxcaml-docs]: https://neocaml.org/usage/#oxcaml
[tim]: https://github.com/tmcgilchrist
[314eter]: https://github.com/314eter
[abi14]: https://github.com/tree-sitter/tree-sitter-ocaml/tree/v0.26.0-abi14
[ocp-indent]: https://github.com/OCamlPro/ocp-indent
[mlemerre]: https://github.com/mlemerre
[issue-60]: https://github.com/bbatsov/neocaml/issues/60
[pr-46]: https://github.com/bbatsov/neocaml/pull/46
[odoc-grammar]: https://github.com/tmcgilchrist/tree-sitter-odoc
[odoc-docs]: https://neocaml.org/odoc/
[akira]: https://github.com/akirak
[alessio]: https://github.com/dalz
[dune-grammar]: https://github.com/ocaml-dune/tree-sitter-dune
[tree-sitter-mlx]: https://github.com/ocaml-mlx/tree-sitter-mlx
[roadmap]: https://github.com/bbatsov/neocaml/blob/main/ROADMAP.md
