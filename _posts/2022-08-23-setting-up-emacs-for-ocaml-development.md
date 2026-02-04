---
title: Setting up Emacs for OCaml Development
date: 2022-08-23 10:17 +0300
tags:
- Emacs
- OCaml
---

I've [promised you articles about OCaml]({% post_url 2022-08-19-learning-ocaml %}) and here they come! The first order of business when learning a new programming language is to setup Emacs for effective programming in it.

Emacs has a long history with OCaml[^1] and there are many Emacs packages for you to choose from:

* [tuareg](https://github.com/ocaml/tuareg):
OCaml major mode for Emacs that can also run the toplevel and the debugger within Emacs.
* [caml-mode](https://github.com/ocaml/caml-mode):
Another (older) OCaml major mode for Emacs. While it has less features than Tuareg it covers the basics well and also features toplevel and debugger integration.
* [dune](https://github.com/ocaml/dune/blob/main/editor-integration/emacs/dune.el): Dune major mode and utility commands for Emacs.
* [utop.el](https://github.com/ocaml-community/utop#integration-with-emacs): utop integration for Emacs. Features code completion.
* [merlin-mode](https://ocaml.github.io/merlin/editor/emacs/): Code completion, linting, code navigation and type analysis mode for Emacs.
* [merlin-eldoc](https://github.com/Khady/merlin-eldoc): Eldoc support for OCaml, using Merlin internally.
* [flycheck-ocaml](https://github.com/flycheck/flycheck-ocaml): OCaml linter for Emacs, using Merlin internally.

I still haven't had the time to compare `tuareg` with `caml-mode`, but I've noticed that `tuareg` has more active development and most OCaml developers
are using it. On paper both major modes have pretty similar features, but `tuareg`'s most obvious advantage being that it uses Emacs's relatively modern
[Simple Minded Indentation Engine](https://www.gnu.org/software/emacs/manual/html_node/elisp/SMIE.html) (aka SMIE).

[Dune](https://dune.build/) is the most popular OCaml build tool these days and `dune.el` provides font-locking for its project files and the ability to run
some dune commands straight from Emacs.

[utop](https://opam.ocaml.org/blog/about-utop/) is a popular OCaml toplevel (REPL) that has a lot more bells and whistles than the standard toplevel `ocaml`. `utop.el` allows you to run utop within Emacs and send OCaml code there straight from your source buffers. `utop.el` doesn't expose all of the fancy utop features, but it does
have code completion, which is probably its main advantage over the default toplevel.

[Merlin](https://ocaml.github.io/merlin/) is an editor service that provides advanced IDE features for OCaml. Think completion, type information, navigation to definition, find usages, refactoring, linting, code generation, etc. It's the backbone of any OCaml development tool and has great integration with Emacs via [merlin-mode](https://ocaml.github.io/merlin/editor/emacs/).[^2]

`utop` and `merlin` are tools you need to install if you want to use them together with Emacs:

```console
$ opam install utop merlin
```

`merlin-eldoc` and `flycheck-ocaml` are actually implemented in terms of Merlin and basically fit some information coming from Merlin in the uniform interfaces of ElDoc and Flycheck.

I'd say that the only really essential packages for everyone are:

* tuareg-mode
* merlin-mode

Still, some ElDoc support is always welcome and I'm a big fan of Flycheck for linting (`merlin-mode` has its own linting as well), so I'd recommend adding those extra packages as well. I'm also guessing it's unlikely you won't be using Dune if you ever get past toy OCaml programs.

Here's an example Emacs config for OCaml (based on my own) that you can borrow:

``` emacs-lisp
(require 'package)

(add-to-list 'package-archives
             '("melpa" . "https://melpa.org/packages/") t)
;; keep the installed packages in .emacs.d
(setq package-user-dir (expand-file-name "elpa" user-emacs-directory))
(package-initialize)
;; update the package metadata is the local cache is missing
(unless package-archive-contents
  (package-refresh-contents))

(unless (package-installed-p 'use-package)
  (package-install 'use-package))

(require 'use-package)
(setq use-package-verbose t)

;; Major mode for OCaml programming
(use-package tuareg
  :ensure t
  :mode (("\\.ocamlinit\\'" . tuareg-mode)))

;; Major mode for editing Dune project files
(use-package dune
  :ensure t)

;; Merlin provides advanced IDE features
(use-package merlin
  :ensure t
  :config
  (add-hook 'tuareg-mode-hook #'merlin-mode)
  (add-hook 'merlin-mode-hook #'company-mode)
  ;; we're using flycheck instead
  (setq merlin-error-after-save nil))

(use-package merlin-eldoc
  :ensure t
  :hook ((tuareg-mode) . merlin-eldoc-setup))

;; This uses Merlin internally
(use-package flycheck-ocaml
  :ensure t
  :config
  (flycheck-ocaml-setup))
```

If you're into utop you can also setup some integration with it like this:

``` emacs-lisp
;; utop configuration
(use-package utop
  :ensure t
  :config
  (add-hook 'tuareg-mode-hook #'utop-minor-mode))
```

Note that this will shadow the keybinding for starting/switching to the toplevel in
`tuareg-mode` (`C-c C-z`), which is intentional.

Now you'll have everything you'd expect from an OCaml IDE within the comfort of
your Emacs. One thing to keep in mind is that Merlin works best with Dune
projects and you'll need to run the following command occasionally to refresh
the Merlin metadata:

```console
$ dune build
```

I'm still a bit puzzled as to when exactly this needs to be done - generally I
do it when I start getting errors from Merlin that it doesn't have knowledge
about some source file.

I'm working with OCaml in a similar way to [working with
Clojure](https://docs.cider.mx/cider/usage/cider_mode.html#basic-workflow) - I
write a bit of code, send it to the toplevel to play with it and keep tweaking
it until I'm happy with the result. Sadly, the OCaml toplevel is quite primitive
and limited by Lisp standards, but it still gets the job done. It will be hard
for me to adapt to any workflow that's not extremely REPL-centric. Perhaps there's
a much better workflow that's eluding me at this point.

Note that Emacs also has LSP support[^3], but I've opted to for a more
traditional setup mostly because I don't use LSP with any of the programming
languages that I work with frequently. Also - I've noticed that
[OCaml-LSP](https://github.com/ocaml/ocaml-lsp) is mostly a thin wrapper around
Merlin, so you're getting a pretty similar experience anyways. I even played a
bit with OCaml in VS Code to make sure I'm not missing out of something major
without LSP and I'm fairly certain that I'm not. I'll write a follow-up article
if I ever adopt (OCaml-)LSP in my workflow.

I've spent quite some time trying to understand how the different pieces of the
puzzle fit together and to tweak them to my liking. Hopefully this article will
help you save some time. I've also contributed a few fixes and improvements to
`tuareg-mode` and `utop.el`, and I became the maintainer of the orphaned
`flycheck-ocaml` package.

That's all I have for you today.
I'd love to hear any feedback, tips and tricks you might have about hacking with OCaml in Emacs!

[^1]: I believe OCaml's creators (Xavier Leroy, Jérôme Vouillon, Damien Doligez, and Didier Rémy) are (were?) Emacs users.
[^2]: If you're into Clojure - basically `merlin-mode` is the closest thing to CIDER that exists for OCaml.
[^3]: See [lsp-mode](https://emacs-lsp.github.io/lsp-mode/) and [eglot](https://github.com/joaotavora/eglot) for more details.
