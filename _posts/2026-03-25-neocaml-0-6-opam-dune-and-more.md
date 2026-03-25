---
layout: post
title: 'Neocaml 0.6: Opam, Dune, and More'
date: 2026-03-25 10:00 +0200
tags:
- OCaml
- Emacs
---

When I released [neocaml 0.1]({% post_url 2026-02-14-neocaml-0-1-ready-for-action %})
last month, I thought I was more or less done with the (main) features for the
foreseeable future. The original scope was deliberately small — a couple of
Tree-sitter-powered OCaml major modes (for `.ml` and `.mli`), a REPL
integration, and not much else. I was quite happy with how things turned out and
figured the next steps would be mostly polish and bug fixes.

Versions 0.2-0.5 brought polish and bug fixes, but fundamentally the feature
set stayed the same. I was even more convinced a grand 1.0 release was just
around the corner.

I was wrong.

Of course, OCaml files don't exist in isolation. They live alongside
[Opam](https://opam.ocaml.org/) files that describe packages and
[Dune](https://dune.readthedocs.io/) files that configure builds. And as I was
poking around the Tree-sitter ecosystem, I discovered that there were already
grammars for both
[Opam](https://github.com/tmcgilchrist/tree-sitter-opam) and
[Dune](https://github.com/tmcgilchrist/tree-sitter-dune) files. Given how
simple both formats are (Opam is mostly key-value pairs, Dune is
s-expressions), adding support for them turned out to be fairly
straightforward.

So here we are with neocaml 0.6, which is quite a bit bigger than I expected
originally.

**Note:** One thing worth mentioning — all the new modes are completely
isolated from the core OCaml modes. They're separate files with no hard
dependency on `neocaml-mode`, loaded only when you open the relevant file
types. I didn't want to force them upon anyone — for me it's convenient to get
Opam and Dune support out-of-the-box (given how ubiquitous they are in the OCaml
ecosystem), but I totally get it if someone doesn't care about this.

Let me walk you through what's new.

## neocaml-opam-mode

The new `neocaml-opam-mode` activates automatically for `.opam` and `opam`
files. It provides:

- Tree-sitter-based font-lock (field names, strings, operators, version
  constraints, filter expressions, etc.)
- Indentation (lists, sections, option braces)
- Imenu for navigating variables and sections
- A **flymake backend** that runs `opam lint` on the current buffer, giving you
  inline diagnostics for missing fields, deprecated constructs, and syntax
  errors

The flymake backend registers automatically when `opam` is found in your PATH,
but you need to enable `flymake-mode` yourself:

```emacs-lisp
(add-hook 'neocaml-opam-mode-hook #'flymake-mode)
```

[Flycheck](https://github.com/flycheck/flycheck) users get `opam lint` support
out of the box via Flycheck's built-in `opam` checker — no extra configuration
needed.

This bridges some of the gap with [Tuareg](https://github.com/ocaml/tuareg),
which also bundles an Opam major mode (`tuareg-opam-mode`). The Tree-sitter-based
approach gives us more accurate highlighting, and the flymake integration is a
nice bonus on top.

## neocaml-dune-mode

`neocaml-dune-mode` handles `dune`, `dune-project`, and `dune-workspace` files
— all three use the same s-expression syntax and share a single Tree-sitter
grammar. You get:

- Font-lock for stanza names, field names, action keywords, strings, module
  names, library names, operators, and brackets
- Indentation with 1-space offset
- Imenu for stanza navigation
- Defun navigation and `which-func` support

This removes the need to install the separate
[dune](https://melpa.org/#/dune) package (the standalone `dune-mode` maintained
by the Dune developers) from MELPA. If you prefer to keep using it, that's fine
too — neocaml's README has
[instructions](https://github.com/bbatsov/neocaml#using-the-legacy-dune-mode)
for overriding the `auto-mode-alist` entries.

## neocaml-dune-interaction-mode

Beyond editing Dune files, I wanted a simple way to run Dune commands from any
neocaml buffer. `neocaml-dune-interaction-mode` is a minor mode that provides
keybindings (under `C-c C-d`) and a "Dune" menu for common operations:

| Keybinding | Command |
|---|---|
| `C-c C-d b` | Build |
| `C-c C-d t` | Test |
| `C-c C-d c` | Clean |
| `C-c C-d p` | Promote |
| `C-c C-d f` | Format |
| `C-c C-d u` | Launch utop with project libraries |
| `C-c C-d r` | Run an executable |
| `C-c C-d d` | Run any Dune command |
| `C-c C-d .` | Find the nearest `dune` file |

All commands run via Emacs's `compile`, so you get error navigation, clickable
source locations, and the full `compilation-mode` interface for free. With a
prefix argument (`C-u`), build, test, and fmt run in **watch mode**
(`--watch`), automatically rebuilding when files change.

The `utop` command is special — it launches through `neocaml-repl`, so you get
the full REPL integration (send region, send definition, etc.) with your
project's libraries preloaded.

This mode is completely independent from `neocaml-dune-mode` — it doesn't care
which major mode you're using. You can enable it in OCaml buffers like this:

```emacs-lisp
(add-hook 'neocaml-base-mode-hook #'neocaml-dune-interaction-mode)
```

## Rough Edges

Both the Opam and Dune Tree-sitter grammars are relatively young and will need
some more work for optimal results. I've been filing issues and contributing
patches upstream to improve them — for instance, the Dune grammar currently
[flattens field-value
pairs](https://github.com/tmcgilchrist/tree-sitter-dune/issues/9) in a way
that makes indentation less precise than it could be, and neither grammar
supports [variable
interpolation](https://github.com/tmcgilchrist/tree-sitter-dune/issues/10)
(`%{...}`) yet. These are very solvable problems and I expect the grammars to
improve over time.

## What's Next?

At this point I think I'm (finally!) out of ideas for new functionality. This
time I mean it! Neocaml now covers pretty much everything I ever wanted,
especially when paired with the awesome
[ocaml-eglot](https://github.com/tarides/ocaml-eglot).

Down the road there might be support for OCamllex (`.mll`) or Menhir (`.mly`)
files, but only if adding them doesn't bring significant complexity — both are
mixed languages with embedded OCaml code, which makes them fundamentally harder
to support well than the simple Opam and Dune formats.

I hope OCaml programmers will find the new functionality useful. If you're using neocaml,
I'd love to hear how it's working for you — bug reports, feature requests, and
general feedback are all welcome on
[GitHub](https://github.com/bbatsov/neocaml). You can find the full list of
changes in the
[changelog](https://github.com/bbatsov/neocaml/blob/main/CHANGELOG.md).

As usual — update from [MELPA](https://melpa.org/#/neocaml), kick the tires,
and let me know what you think.

That's all I have for you today! Keep hacking!
