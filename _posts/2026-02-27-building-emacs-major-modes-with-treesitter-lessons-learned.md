---
title: 'Building Emacs Major Modes with TreeSitter: Lessons Learned'
date: 2026-02-27 10:00 +0200
tags:
- Emacs
- OCaml
- Clojure
- AsciiDoc
---

Over the past year I've been spending a lot of time building TreeSitter-powered
major modes for Emacs -- [clojure-ts-mode](https://github.com/clojure-emacs/clojure-ts-mode)
(as co-maintainer), [neocaml](https://github.com/bbatsov/neocaml) (from scratch),
and [asciidoc-mode](https://github.com/bbatsov/asciidoc-mode) (also from scratch).
Between the three projects I've accumulated enough battle scars to write about the
experience. This post distills the key lessons for anyone thinking about writing
a TreeSitter-based major mode, or curious about what it's actually like.

<!--more-->

## Why TreeSitter?

Before TreeSitter, Emacs font-locking was done with regular expressions and
indentation was handled by ad-hoc engines (SMIE, custom indent functions, or
pure regex heuristics). This works, but it has well-known problems:

- **Regex-based font-locking is fragile.** Regexes can't parse nested structures,
  so they either under-match (missing valid code) or over-match (highlighting
  inside strings and comments). Every edge case is another regex, and the patterns
  become increasingly unreadable over time.

- **Indentation engines are complex.** SMIE (the generic indentation engine for non-TreeSitter modes) requires defining operator
  precedence grammars for the language, which is hard to get right. Custom
  indentation functions tend to grow into large, brittle state machines. Tuareg's
  indentation code, for example, is thousands of lines long.

TreeSitter changes the game because you get a **full, incremental, error-tolerant
syntax tree** for free. Font-locking becomes "match this AST pattern, apply this
face" and indentation becomes "if the parent node is X, indent by Y". The rules
are declarative, composable, and much easier to reason about than regex chains.

In practice, `neocaml`'s entire font-lock and indentation logic fits in about 350
lines of Elisp. The equivalent in tuareg is spread across thousands of lines.
That's the real selling point: **simpler, more maintainable code that handles more
edge cases correctly**.

## Challenges

That said, TreeSitter in Emacs is not a silver bullet. Here's what I ran into.

### Every grammar is different

TreeSitter grammars are written by different authors with different philosophies.
The [tree-sitter-ocaml](https://github.com/tree-sitter/tree-sitter-ocaml)
grammar provides a rich, detailed AST with named fields. The
[tree-sitter-clojure](https://github.com/sogaiu/tree-sitter-clojure) grammar,
by contrast, deliberately keeps things minimal -- it only models syntax, not
semantics, because Clojure's macro system makes static semantic analysis
unreliable.[^1] This means font-locking `def` forms in Clojure requires
predicate matching on symbol text, while in OCaml you can directly match
`let_binding` nodes with named fields.

You can't learn "how to write TreeSitter queries" generically -- you need to
learn each grammar individually. The best tool for this is `treesit-explore-mode`
(to visualize the full parse tree) and `treesit-inspect-mode` (to see the node
at point). Use them constantly.

### Grammar quality varies wildly

You're dependent on someone else providing the grammar, and quality is all over
the map. The OCaml grammar is mature and well-maintained -- it's hosted under the
official [tree-sitter](https://github.com/tree-sitter) GitHub org. The Clojure
grammar is small and stable by design. But not every language is so lucky.

[asciidoc-mode](https://github.com/bbatsov/asciidoc-mode) uses a
[third-party AsciiDoc grammar](https://github.com/cathaysia/tree-sitter-asciidoc)
that employs a dual-parser architecture -- one parser for block-level structure
(headings, lists, code blocks) and another for inline formatting (bold, italic,
links). This is the same approach used by Emacs's built-in `markdown-ts-mode`,
and it makes sense for markup languages where block and inline syntax are largely
independent.

The problem is that the two parsers run independently on the same text, and they
can **disagree**. The inline parser misinterprets `*` and `**` list markers as
emphasis delimiters, creating spurious bold spans that swallow subsequent inline
content. The workaround is to use `:override t` on all block-level font-lock
rules so they win over the incorrect inline faces:

```emacs-lisp
;; Block-level rules use :override t so block-level faces win over
;; spurious inline emphasis nodes (the inline parser misreads `*'
;; list markers as emphasis delimiters).
:language 'asciidoc
:override t
:feature 'list
'((ordered_list_marker) @font-lock-constant-face
  (unordered_list_marker) @font-lock-constant-face)
```

This doesn't fix inline elements consumed by the spurious emphasis -- that
requires an upstream grammar fix. When you hit grammar-level issues like this,
you either fix them yourself (which means diving into the grammar's JavaScript
source and C toolchain) or you live with workarounds. Either way, it's a
reminder that your mode is only as good as the grammar underneath it.

Getting the font-locking right in `asciidoc-mode` was probably the most
challenging part of all three projects, precisely because of these grammar
quirks. I also ran into a subtle `treesit` behavior: the default font-lock mode
(`:override nil`) skips an entire captured range if *any* position within it
already has a face. So if you capture a parent node like `(inline_macro)` and a
child was already fontified, the whole thing gets skipped silently. The fix is
to capture specific child nodes instead:

```emacs-lisp
;; BAD: entire node gets skipped if any child is already fontified
;; (inline_macro) @font-lock-function-call-face

;; GOOD: capture specific children
(inline_macro (macro_name) @font-lock-function-call-face)
(inline_macro (target) @font-lock-string-face)
```

These issues took a lot of trial and error to diagnose. The lesson: **budget
extra time for font-locking when working with less mature grammars**.

### Grammar versions and breaking changes

Grammars evolve, and breaking changes happen. `clojure-ts-mode` switched from
the stable grammar to the [experimental
branch](https://github.com/sogaiu/tree-sitter-clojure/tree/unstable-20250526)
because the stable version had metadata nodes as children of other nodes, which
caused `forward-sexp` and `kill-sexp` to behave incorrectly. The experimental
grammar makes metadata standalone nodes, fixing the navigation issues but
requiring all queries to be updated.

`neocaml` pins to
[v0.24.0](https://github.com/tree-sitter/tree-sitter-ocaml/tree/v0.24.0) of the
OCaml grammar. If you don't pin versions, a grammar update can silently break
your font-locking or indentation.

The takeaway: **always pin your grammar version**, and include a mechanism to
detect outdated grammars. `clojure-ts-mode` tests a query that changed between
versions to detect incompatible grammars at startup.

### Grammar delivery

Users shouldn't have to manually clone repos and compile C code to use your
mode. Both `neocaml` and `clojure-ts-mode` include grammar recipes:

```emacs-lisp
(defconst neocaml-grammar-recipes
  '((ocaml "https://github.com/tree-sitter/tree-sitter-ocaml"
           "v0.24.0"
           "grammars/ocaml/src")
    (ocaml-interface "https://github.com/tree-sitter/tree-sitter-ocaml"
                     "v0.24.0"
                     "grammars/interface/src")))
```

On first use, the mode checks `treesit-language-available-p` and offers to install
missing grammars via `treesit-install-language-grammar`. This works, but requires
a C compiler and Git on the user's machine, which is not ideal.[^2]

### The Emacs TreeSitter APIs are a moving target

The TreeSitter support in Emacs has been improving steadily, but each version
has its quirks:

**Emacs 29** introduced TreeSitter support but lacked several APIs. For instance,
`treesit-thing-settings` (used for structured navigation) doesn't exist -- you
need a fallback:

```emacs-lisp
;; Fallback for Emacs 29 (no treesit-thing-settings)
(unless (boundp 'treesit-thing-settings)
  (setq-local forward-sexp-function #'neocaml-forward-sexp))
```

**Emacs 30** added `treesit-thing-settings`, sentence navigation, and better
indentation support. But it also had a bug in `treesit-range-settings` offsets
([#77848](https://debbugs.gnu.org/cgi/bugreport.cgi?bug=77848)) that broke
embedded parsers, and another in `treesit-transpose-sexps` that required
`clojure-ts-mode` to disable its TreeSitter-aware version.

**Emacs 31** has a bug in `treesit-forward-comment` where an off-by-one error
causes `uncomment-region` to leave ` *)` behind on multi-line OCaml comments. I
had to skip the affected test with a version check:

```emacs-lisp
(when (>= emacs-major-version 31)
  (signal 'buttercup-pending
          "Emacs 31 treesit-forward-comment bug (off-by-one)"))
```

The lesson: **test your mode against multiple Emacs versions**, and be prepared
to write version-specific workarounds. CI that runs against Emacs 29, 30, and
snapshot is essential.

### No .scm file support (yet)

Most TreeSitter grammars ship with `.scm` query files for syntax highlighting
(`highlights.scm`) and indentation (`indents.scm`). Editors like Neovim and
Helix use these directly. Emacs doesn't -- you have to manually translate the
`.scm` patterns into `treesit-font-lock-rules` and `treesit-simple-indent-rules`
calls in Elisp.

This is tedious and error-prone. You end up maintaining a parallel set of
queries that can drift from upstream. Emacs 31 will introduce
[`define-treesit-generic-mode`](https://github.com/emacs-mirror/emacs/blob/master/lisp/treesit-x.el#L47)
which will make it possible to use `.scm` files for font-locking, which should
help significantly. But for now, you're hand-coding everything.

## Tips and tricks

### Debugging font-locking

When a face isn't being applied where you expect:

1. Use `treesit-inspect-mode` to verify the node type at point matches your
   query.
2. Set `treesit--font-lock-verbose` to `t` to see which rules are firing.
3. Check the font-lock feature level -- your rule might be in level 4 while the
   user has the default level 3. The features are assigned to levels via
   `treesit-font-lock-feature-list`.
4. Remember that **rule order matters**. Without `:override`, an earlier rule that
   already fontified a region will prevent later rules from applying. This can be
   intentional (e.g. builtin types at level 3 take precedence over generic types)
   or a source of bugs.

### Debugging indentation

Indentation issues are harder to diagnose because they depend on tree structure,
rule ordering, and anchor resolution:

1. Set `treesit--indent-verbose` to `t` -- this logs which rule matched for each
   line, what anchor was computed, and the final column.
2. Use `treesit-explore-mode` to understand the parent chain. The key question
   is always: "what is the parent node, and which rule matches it?"
3. Watch out for the **empty-line problem**: when the cursor is on a blank line,
   TreeSitter has no node at point. The indentation engine falls back to the root
   `compilation_unit` node as the parent, which typically matches the top-level
   rule and gives column 0. In neocaml I solved this with a `no-node` rule that
   looks at the previous line's last token to decide indentation:

   ```emacs-lisp
   (no-node prev-line neocaml--empty-line-offset)
   ```

### Build a comprehensive test suite

This is the single most important piece of advice. Font-lock and indentation are
easy to break accidentally, and manual testing doesn't scale. Both projects use
[Buttercup](https://github.com/jorgenschaefer/emacs-buttercup) (a BDD testing
framework for Emacs) with custom test macros.

**Font-lock tests** insert code into a buffer, run `font-lock-ensure`, and assert
that specific character ranges have the expected face:

```emacs-lisp
(when-fontifying-it "fontifies let-bound functions"
  ("let greet name = ..."
   (5 9 font-lock-function-name-face)))
```

**Indentation tests** insert code, run `indent-region`, and assert the result
matches the expected indentation:

```emacs-lisp
(when-indenting-it "indents a match expression"
  "match x with"
  "| 0 -> \"zero\""
  "| n -> string_of_int n")
```

**Integration tests** load real source files and verify that both font-locking
and indentation survive `indent-region` on the full file. This catches
interactions between rules that unit tests miss.

`neocaml` has 200+ automated tests and `clojure-ts-mode` has even more.
Investing in test infrastructure early pays off enormously -- I can refactor
indentation rules with confidence because the suite catches regressions
immediately.

#### A personal story on testing ROI

When I became the maintainer of
[clojure-mode](https://github.com/clojure-emacs/clojure-mode) many years ago, I
really struggled with making changes. There were no font-lock or indentation
tests, so every change was a leap of faith -- you'd fix one thing and break three
others without knowing until someone filed a bug report. I spent years working
on a testing approach I was happy with, alongside many great contributors, and
the return on investment was massive.

The same approach -- almost the same test macros -- carried over directly to
`clojure-ts-mode` when we built the TreeSitter version. And later I reused the
pattern again in `neocaml` and `asciidoc-mode`. One investment in testing
infrastructure, four projects benefiting from it.

I know that automated tests, for whatever reason, never gained much traction in
the Emacs community. Many popular packages have no tests at all. I hope stories
like this convince you that investing in tests is really important and pays off
-- not just for the project where you write them, but for every project you build
after.

### Pre-compile queries

This one is specific to `clojure-ts-mode` but applies broadly: compiling
TreeSitter queries at runtime is expensive. If you're building queries
dynamically (e.g. with `treesit-font-lock-rules` called at mode init time),
consider pre-compiling them as `defconst` values. This made a noticeable
difference in `clojure-ts-mode`'s startup time.

## A note on naming

The Emacs community has settled on a `-ts-mode` suffix convention for
TreeSitter-based modes: `python-ts-mode`, `c-ts-mode`, `ruby-ts-mode`, and so
on. This makes sense when both a legacy mode and a TreeSitter mode coexist in
Emacs core -- users need to choose between them. But I think the convention is
being applied too broadly, and I'm afraid the resulting name fragmentation will
haunt the community for years.

For new packages that don't have a legacy counterpart, the `-ts-mode` suffix is
unnecessary. I named my packages `neocaml` (not `ocaml-ts-mode`) and
`asciidoc-mode` (not `adoc-ts-mode`) because there was no prior `neocaml-mode`
or `asciidoc-mode` to disambiguate from. The `-ts-` infix is an implementation
detail that shouldn't leak into the user-facing name. Will we rename everything
again when TreeSitter becomes the default and the non-TS variants are removed?

Be bolder with naming. If you're building something new, give it a name that
makes sense on its own merits, not one that encodes the parsing technology in the
package name.

## The road ahead

I think the full transition to TreeSitter in the Emacs community will take
3--5 years, optimistically. There are hundreds of major modes out there, many
maintained by a single person in their spare time. Converting a mode from regex
to TreeSitter isn't just a mechanical translation -- you need to understand the
grammar, rewrite font-lock and indentation rules, handle version compatibility,
and build a new test suite. That's a lot of work.

Interestingly, this might be one area where agentic coding tools can genuinely
help. The structure of TreeSitter-based major modes is fairly uniform: grammar
recipes, font-lock rules, indentation rules, navigation settings, imenu. If you
give an AI agent a grammar and a reference to a high-quality mode like
`clojure-ts-mode`, it could probably scaffold a reasonable new mode fairly
quickly. The hard parts -- debugging grammar quirks, handling edge cases, getting
indentation *just right* -- would still need human attention, but the boilerplate
could be automated.

Still, knowing the Emacs community, I wouldn't be surprised if a full migration
never actually completes. Many old-school modes work perfectly fine, their
maintainers have no interest in TreeSitter, and "if it ain't broke, don't fix
it" is a powerful force. And that's okay -- diversity of approaches is part of
what makes Emacs Emacs.

## Closing thoughts

TreeSitter is genuinely great for building Emacs major modes. The code is
simpler, the results are more accurate, and incremental parsing means everything
stays fast even on large files. I wouldn't go back to regex-based font-locking
willingly.

But it's not magical. Grammars are inconsistent across languages, the Emacs APIs
are still maturing, you can't reuse `.scm` files (yet), and you'll hit
version-specific bugs that require tedious workarounds. The testing story is
better than with regex modes -- tree structures are more predictable than regex
matches -- but you still need a solid test suite to avoid regressions.

If you're thinking about writing a TreeSitter-based major mode, do it. The
ecosystem needs more of them, and the experience of working with syntax trees
instead of regexes is genuinely enjoyable. Just go in with realistic
expectations, pin your grammar versions, test against multiple Emacs releases,
and build your test suite early.

Anyways, I wish there was an article like this one when I was starting out
with `clojure-ts-mode` and `neocaml`, so there you have it. I hope that
the lessons I've learned along the way will help build better modes
with TreeSitter down the road.

That's all I have for you today. Keep hacking!

[^1]: See the excellent [scope discussion](https://github.com/sogaiu/tree-sitter-clojure/blob/master/doc/scope.md) in the tree-sitter-clojure repo for the rationale.
[^2]: There's ongoing discussion in the Emacs community about distributing pre-compiled grammar binaries, but nothing concrete yet.
