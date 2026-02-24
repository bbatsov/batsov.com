---
title: 'Setting up Emacs for OCaml Development: Neocaml Edition'
date: 2026-02-24 12:00 +0200
tags:
- Emacs
- OCaml
---

A few years ago I wrote about [setting up Emacs for OCaml
development]({% post_url 2022-08-23-setting-up-emacs-for-ocaml-development %}).
Back then the recommended stack was `tuareg-mode` + `merlin-mode`, with Merlin
providing the bulk of the IDE experience. A lot has changed since then -- the
OCaml tooling has evolved considerably, and I've been working on some new tools
myself. Time for an update.

## The New Stack

Here's what I recommend today:

- [neocaml](https://github.com/bbatsov/neocaml) instead of `tuareg-mode`
- [ocaml-eglot](https://github.com/tarides/ocaml-eglot) instead of `merlin-mode`
- [Eglot](https://www.gnu.org/software/emacs/manual/html_mono/eglot.html) (built into Emacs 29+) as the LSP client

The key shift is from Merlin's custom protocol to LSP.
[ocaml-lsp-server](https://github.com/ocaml/ocaml-lsp) has matured
significantly since my original article -- it's no longer a thin wrapper around
Merlin. It now offers project-wide renaming, semantic highlighting, Dune RPC
integration, and OCaml-specific extensions like pattern match generation and
typed holes. `ocaml-eglot` is a lightweight Emacs package by
[Tarides](https://tarides.com/) that bridges Eglot with these OCaml-specific
LSP extensions, giving you the full Merlin feature set through a standardized
protocol.

And `neocaml` is my own TreeSitter-powered OCaml major mode -- modern, lean,
and built for the LSP era. You can read more about it in the [0.1 release
announcement]({% post_url 2026-02-14-neocaml-0-1-ready-for-action %}).

## The Essentials

First, install the server-side tools:

```console
$ opam install ocaml-lsp-server
```

> You no longer need to install `merlin` separately -- `ocaml-lsp-server`
> vendors it internally.
{: .prompt-tip }

Then set up Emacs:

```emacs-lisp
;; Modern TreeSitter-powered OCaml major mode
(use-package neocaml
  :ensure t)

;; Major mode for editing Dune project files
(use-package dune
  :ensure t)

;; OCaml-specific LSP extensions via Eglot
(use-package ocaml-eglot
  :ensure t
  :hook (neocaml-mode . ocaml-eglot-setup))
```

That's it. Eglot ships with Emacs 29+, so there's nothing extra to install for
the LSP client itself. When you open an OCaml file, Eglot will automatically
start `ocaml-lsp-server` and you'll have completion, type information, code
navigation, diagnostics, and all the other goodies you'd expect.

Compare this to the old setup -- no more `merlin-mode`, `merlin-eldoc`,
`flycheck-ocaml`, or manual Company configuration. LSP handles all of it
through a single, uniform interface.

## The Toplevel

`neocaml` includes built-in REPL integration via `neocaml-repl-minor-mode`. The
basics work well:

- `C-c C-z` -- start or switch to the OCaml toplevel
- `C-c C-c` -- send the current definition
- `C-c C-r` -- send the selected region
- `C-c C-b` -- send the entire buffer

If you want `utop` specifically, you're still better off using
[utop.el](https://github.com/ocaml-community/utop) alongside `neocaml`. Its
main advantage is that you get code completion inside the `utop` REPL within
Emacs -- something `neocaml`'s built-in REPL integration doesn't provide:

```emacs-lisp
(use-package utop
  :ensure t
  :hook (neocaml-mode . utop-minor-mode))
```

This will shadow `neocaml`'s REPL keybindings with `utop`'s, which is the
intended behavior.

That said, as I've grown more comfortable with OCaml I find myself using the
toplevel less and less. These days I rely more on a test-driven workflow --
write a test, run it, iterate. In particular I'm partial to the workflow
described in [this OCaml Discuss
thread](https://discuss.ocaml.org/t/whats-your-development-workflow/10358/7) --
running `dune runtest` continuously and writing expect tests for quick feedback.
It's a more structured approach that scales better than REPL-driven development,
especially as your projects grow.

## Give neocaml a Try

If you're an OCaml programmer using Emacs, I'd love for you to take
[neocaml](https://github.com/bbatsov/neocaml) for a spin. It's available on
MELPA, so getting started is just an `M-x package-install` away. The project is
still young and I'm actively working on it -- your feedback, bug reports, and
pull requests are invaluable. Let me know what works, what doesn't, and what
you'd like to see next.

That's all I have for you today. Keep hacking!
