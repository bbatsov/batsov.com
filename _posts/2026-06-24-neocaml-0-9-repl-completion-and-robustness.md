---
layout: post
title: 'Neocaml 0.9: A Better REPL, Dune/Opam Completion, and More Robustness'
date: 2026-06-24 10:00 +0300
tags:
- OCaml
- Emacs
---

It's been a couple of months since the last [neocaml](https://github.com/bbatsov/neocaml)
release, and the reason is simple — for a while there I was genuinely out of ideas.
Back when I shipped [0.6]({% post_url 2026-03-25-neocaml-0-6-opam-dune-and-more %})
I declared (again!) that I was done with new features, and this time I almost meant
it. But ideas have a way of creeping back in, and 0.9 turned out to be a meaty
release. Here are the highlights.

## A much nicer REPL experience

The biggest chunk of work went into the REPL (toplevel) integration. I'm well aware
that the OCaml toplevel isn't terribly popular with seasoned OCaml developers — most
of them reach for a proper build and a debugger instead. But I think newcomers get a
lot of mileage out of a REPL, and (no surprise to anyone who's followed my work) I'm
a Lisper at heart with a real soft spot for interactive development. Clojure and
Emacs Lisp spoiled me, and I want OCaml beginners to taste a bit of that too.

So, what's new:

- **A dedicated REPL per project.** The REPL buffer is now named after its project
  (e.g. `*OCaml: myproject*`), and the send commands route to the current buffer's
  project REPL. You can have several projects running side by side without them
  stepping on each other.
- **Choose your toplevel.** The new `neocaml-repl-flavor` lets you pick between
  `ocaml`, `utop`, and `dune-utop`. Set it globally, or per project via
  `.dir-locals.el`:

  ```emacs-lisp
  ((neocaml-mode . ((neocaml-repl-flavor . dune-utop))))
  ```

  The active flavor shows up in the REPL's mode line, so you always know what you're
  talking to.
- **Send a phrase and step.** `C-c C-n` (`neocaml-repl-send-phrase-and-step`) sends
  the phrase at point to the REPL and moves on to the next one — perfect for walking
  through a file top to bottom while you experiment.
- **`#require` from Emacs.** `neocaml-repl-require` loads a `findlib` package into the
  running toplevel without you having to type the directive by hand.
- **Restart on demand.** `neocaml-repl-restart` kills and restarts the toplevel when
  things get into a weird state.

## Completion for Opam and Dune, no LSP required

This one I'm particularly happy with. `.ml`/`.mli` files have `ocaml-lsp-server` to
lean on for completion, but the auxiliary file formats have no language server at all.
That's exactly the kind of gap neocaml is meant to fill, so both `neocaml-dune-mode`
and `neocaml-opam-mode` now ship a `completion-at-point` backend.

In a `dune` file you get completion for stanza names, the field names valid for the
*enclosing* stanza, and library names inside `libraries`/`pps` fields:

``` dune
(library
 (name my_lib)
 (libraries str re|))   ; <- completes both your own libraries and installed findlib ones
```

The library candidates combine your project's own libraries with whatever's installed
in the active Opam switch. And it's switch-aware — if there's a project-local switch
(an `_opam/` directory), neocaml detects it and queries it via `opam exec --`,
without any configuration on your part. The results are cached per project, so it
stays snappy.

In `opam` files you get completion for field and section names, and package names
inside `depends`/`depopts`/`conflicts` (sourced from `opam list`):

``` opam
depends: [
  "dune" {>= "3.0"}
  "cmd|"   ; <- completes to cmdliner and friends
]
```

Both backends can be toggled off via `neocaml-dune-complete-libraries` and
`neocaml-opam-complete-packages` if you'd rather not have them.

## Robustness improvements

A good chunk of this release is the unglamorous but important work of making things
just behave correctly:

- **Character literals and quoted strings at the syntactic layer.** Tree-sitter
  fontifies `'"'`, `'('`, and `{|raw "strings"|}` correctly, but the syntax table
  underneath was getting confused — which broke `forward-sexp`, `delete-pair`, and
  `electric-pair-mode` around those constructs. The fix was a good old
  `syntax-propertize-function`. I wrote up the whole story over on Emacs Redux in
  [Tree-sitter Modes Still Need a Syntax Table](https://emacsredux.com/blog/2026/06/22/tree-sitter-modes-still-need-a-syntax-table/),
  if you're into mode-writing internals.
- **`project.el` integration.** A directory with a `dune-project` file is now
  recognized as a project root (even without version control), `_build/` and `_opam/`
  are ignored, and `compile-command` defaults to `dune build` in dune projects.

There's more in there too — `ocamlformat` integration (`C-c C-f`), clickable URLs and
bug references in comments, a font-lock level selector, and richer menus across
the modes.

## OCaml 5.5 support, and the ABI 14 balancing act

[OCaml 5.5 was released](https://discuss.ocaml.org/t/ocaml-5-5-0-released/18265) on
June 19th, so this felt like a good moment to ship 5.5 support in neocaml. The `ocaml`
and `ocaml-interface` grammars now track tree-sitter-ocaml v0.25.0, which brings the
5.5 grammar along with it.

There's a wrinkle here that's worth explaining, because it's shaped the last few
releases. A tree-sitter grammar gets compiled into a parser that speaks a particular
**ABI version**, and Emacs can only load parsers up to the latest ABI supported by the
`libtree-sitter` it was *built against* — it's not the Emacs version itself that sets
the ceiling. In practice a lot of Emacs 30 builds out there (notably Homebrew's on
macOS) are linked against tree-sitter 0.24, which tops out at **ABI 14**; you need an
Emacs built against tree-sitter 0.25+ to load **ABI 15** grammars. The trouble is that
the tree-sitter 0.25 CLI now generates ABI 15 parsers by default, so any grammar
regenerated with current tooling produces something those builds simply can't load —
you install it and it just errors out. Emacs 31 will ship with newer tree-sitter and
make ABI 15 the common case, but it's not out yet. (This isn't a neocaml problem as
such; it's been biting tree-sitter modes across the ecosystem.)

After a few users ran into exactly this, I've made a deliberate decision: **stick to
ABI 14 grammars until Emacs 31 is widely available.** That effort started a couple of
releases back — in 0.8.1 I lowered the ABI requirement from 15 to 14 across the opam,
dune, and ocamllex modes, switched the menhir recipe to
[tmcgilchrist/tree-sitter-menhir](https://github.com/tmcgilchrist/tree-sitter-menhir),
and pinned ocamllex back to v0.24.0, all of which target ABI 14
([#42](https://github.com/bbatsov/neocaml/issues/42)). 0.9 extends that policy to the
core OCaml grammars.

The catch with v0.25.0 is precisely that it generates an ABI 15 parser. Happily, the
5.5 grammar didn't actually need any ABI 15 features — the bump rode along with the CLI
upgrade — so an ABI 14 regeneration of the very same grammar is a drop-in. Big thanks
to [314eter](https://github.com/314eter), the `tree-sitter-ocaml` maintainer, for cutting
a `v0.25.0-abi14` tag for exactly this purpose
([#141](https://github.com/tree-sitter/tree-sitter-ocaml/issues/141)). The one snag was
that tagging normally triggers releases to NPM, crates.io, and PyPI, so I sent a small
PR to skip publishing for ABI-suffixed tags
([#142](https://github.com/tree-sitter/tree-sitter-ocaml/pull/142)), and the tag
followed. neocaml now pins both grammars to it.

## Roadmap and docs

If you're curious where neocaml is headed, I've started keeping a
[ROADMAP.md](https://github.com/bbatsov/neocaml/blob/main/ROADMAP.md) with ideas and
guiding principles (short version: tree-sitter first, lean on the LSP stack for
`.ml`/`.mli`, and own the auxiliary modes that have no language server). The project
also has a proper documentation site now at
[neocaml.org](https://neocaml.org), so there's a real
home for the details beyond the README.

## Give it a Try

As always — update from [MELPA](https://melpa.org/#/neocaml), play with it, and let
me know how it goes. The full list of changes is in the
[0.9.0 release notes](https://github.com/bbatsov/neocaml/releases/tag/v0.9.0). Bug
reports, feature requests, and pull requests are all welcome on
[GitHub](https://github.com/bbatsov/neocaml).

That's all from me, folks! Keep hacking!
