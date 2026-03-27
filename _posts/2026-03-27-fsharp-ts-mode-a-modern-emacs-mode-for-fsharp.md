---
layout: post
title: 'fsharp-ts-mode: A Modern Emacs Mode for F#'
date: 2026-03-27 17:00 +0200
tags:
- F#
- Emacs
---

I'm pretty much done with the focused development push on
[neocaml](https://github.com/bbatsov/neocaml) -- it's reached a point where I'm
genuinely happy using it daily and the remaining work is mostly incremental
polish. So naturally, instead of taking a break I decided it was time to start
another project that's been living in the back of my head for a while:
a proper Tree-sitter-based F# mode for Emacs.

Meet [fsharp-ts-mode](https://github.com/bbatsov/fsharp-ts-mode).

## Why F#?

I've written before about my fondness for the ML family of languages, and while
OCaml gets most of my attention, last year I developed a soft spot for F#. In some
ways I like it even a bit more than OCaml -- the tooling is excellent, the .NET
ecosystem is massive, and computation expressions are one of the most elegant
abstractions I've seen in any language. F# manages to feel both practical and
beautiful, which is a rare combination.

The problem is that Emacs has never been particularly popular with F# programmers
-- or .NET programmers in general. The existing
[fsharp-mode](https://github.com/fsharp/emacs-fsharp-mode) works, but it's
showing its age: regex-based highlighting, SMIE indentation with quirks, and
some legacy code dating back to the caml-mode days. I needed a good F# mode for
Emacs, and that's enough of a reason to build one in my book.

## The Name

I'll be honest -- I spent quite a bit of time trying to come up with
a clever name.[^1] Some candidates that didn't make the cut:

- **fsharpe-mode** (fsharp(evolved/enhanced)-mode)
- **Fa Dièse** (French for F sharp -- because after spending time with OCaml you
  start thinking in French, apparently)
- **fluoride** (a play on [Ionide](https://ionide.io/), the popular F# IDE
  extension)

In the end none of my fun ideas stuck, so I went with the boring-but-obvious
`fsharp-ts-mode`. Sometimes the straightforward choice is the right one. At
least nobody will have trouble finding it.[^2]

## Built on neocaml's Foundation

I modeled `fsharp-ts-mode` directly after `neocaml`, and the two packages share
a lot of structural similarities -- which shouldn't be surprising given how much
OCaml and F# have in common. The same architecture (base mode + language-specific
derived modes), the same approach to font-locking (shared + grammar-specific
rules), the same REPL integration pattern (`comint` with tree-sitter input
highlighting), the same build system interaction pattern (minor mode wrapping CLI
commands).

This also meant I could get the basics in place really quickly. Having already
solved problems like trailing comment indentation, `forward-sexp` hybrid
navigation, and `imenu` with qualified names in neocaml, porting those solutions
to F# was mostly mechanical.

## What's in 0.1.0

The initial release covers all the essentials:

- **Syntax highlighting** via Tree-sitter with 4 customizable levels, supporting
  `.fs`, `.fsx`, and `.fsi` files
- **Indentation** via Tree-sitter indent rules
- **Imenu** with fully-qualified names (e.g., `MyModule.myFunc`)
- **Navigation** -- `beginning-of-defun`, `end-of-defun`, `forward-sexp`
- **F# Interactive (REPL)** integration with tree-sitter highlighting for input
- **dotnet CLI integration** -- build, test, run, clean, format, restore, with
  watch mode support
- **.NET API documentation lookup** at point (`C-c C-d`)
- **Eglot integration** for
  [FsAutoComplete](https://github.com/fsharp/FsAutoComplete)
- **Compilation error parsing** for `dotnet build` output
- Shift region left/right, auto-detect indent offset, prettify symbols, outline
  mode, and more

### Migrating from fsharp-mode

If you're currently using `fsharp-mode`, switching is straightforward:

```emacs-lisp
(use-package fsharp-ts-mode
  :vc (:url "https://github.com/bbatsov/fsharp-ts-mode" :rev :newest))
```

The main thing `fsharp-ts-mode` doesn't have yet is automatic LSP server
installation (the `eglot-fsharp` package does this for `fsharp-mode`). You'll
need to install FsAutoComplete yourself:

```shellsession
$ dotnet tool install -g fsautocomplete
```

After that, `(add-hook 'fsharp-ts-mode-hook #'eglot-ensure)` is all you need.

See the [migration
guide](https://github.com/bbatsov/fsharp-ts-mode#migrating-from-fsharp-mode) in
the README for a detailed comparison.

## Lessons Learned

Working with the [ionide/tree-sitter-fsharp](https://github.com/ionide/tree-sitter-fsharp)
grammar surfaced some interesting challenges compared to the OCaml grammar:

### F#'s indentation-sensitive syntax is tricky

Unlike OCaml, where indentation is purely cosmetic, F# uses significant
whitespace (the "offside rule"). The tree-sitter grammar needs correct
indentation to parse correctly, which creates a chicken-and-egg problem: you
need a correct parse tree to indent, but you need correct indentation to
parse. For example, if you paste this unindented block:

```fsharp
let f x =
if x > 0 then
x + 1
else
0
```

The parser can't tell that `if` is the body of `f` or that `x + 1` belongs
to the `then` branch -- it produces ERROR nodes everywhere, and
`indent-region` has nothing useful to work with. But if you're typing the code
line by line, the parser always has enough context from preceding lines to
indent the current line correctly. This is a fundamental limitation of any
indentation-sensitive grammar.

### The two grammars are more different than you'd expect

OCaml's tree-sitter-ocaml-interface grammar inherits from the base grammar, so
you can share queries freely. F#'s `fsharp` and `fsharp_signature` grammars are
independent with different node types and field names for equivalent concepts.
For instance, a `let` binding is `function_or_value_defn` in the `.fs` grammar
but `value_definition` in the `.fsi` grammar. Type names use a `type_name:`
field in one grammar but not the other. Even some keyword tokens (`of`, `open`,
`type`) that work fine as query matches in `fsharp` fail at runtime in
`fsharp_signature`.

This forced me to split font-lock rules into shared and grammar-specific sets --
more code, more testing, more edge cases.

### Script files are weird

F# script (`.fsx`) files without a `module` declaration can mix `let` bindings
with bare expressions like `printfn`. The grammar doesn't expect a declaration
after a bare expression at the top level, so it chains everything into nested
`application_expression` nodes:

```fsharp
let x = 1
printfn "%d" x    // bare expression
let y = 2         // grammar nests this under the printfn node
```

Each subsequent `let` ends up one level deeper, causing progressive
indentation. I worked around this with a heuristic that detects declarations
whose ancestor chain leads back to `file` through these misparented nodes and
forces them to column 0. Shebangs (`#!/usr/bin/env dotnet fsi`) required a
different trick -- excluding the first line from the parser's range entirely
via `treesit-parser-set-included-ranges`.

I've filed issues upstream for the grammar pain points -- hopefully they'll
improve over time.

## Current Status

Let me be upfront: this is a 0.1.0 release and it's probably quite buggy. I've
tested it against a reasonable set of F# code, but there are certainly
indentation edge cases, font-lock gaps, and interactions I haven't encountered
yet. If you try it and something looks wrong, please [open an
issue](https://github.com/bbatsov/fsharp-ts-mode/issues) -- `M-x
fsharp-ts-mode-bug-report-info` will collect the environment details for you.

The package can currently be installed only from GitHub (via `package-vc-install`
or manually). I've filed a [PR with
MELPA](https://github.com/melpa/melpa/pull/9917) and I hope it will get merged
soon.

## Wrapping Up

I really need to take a break from building Tree-sitter major modes at this
point. Between `clojure-ts-mode`, `neocaml`, `asciidoc-mode`, and now
`fsharp-ts-mode`, I've spent a lot of time staring at tree-sitter node types and
indent rules.[^3] It's been fun, but I think I've earned a vacation from
`treesit-font-lock-rules`.

I really wanted to do something nice for the (admittedly small) F#-on-Emacs
community, and a modern major mode seemed like the most meaningful contribution I
could make. I hope some of you find it useful!

That's all from me, folks! Keep hacking!

[^1]: Way more time than I needed to actually implement the mode.
[^2]: Many people pointed out they thought `neocaml` was some package for neovim. Go figure why!
[^3]: I've also been helping a bit with [erlang-ts-mode](https://github.com/erlang/emacs-erlang-ts) recently.
