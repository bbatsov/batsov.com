---
layout: post
title: 'neat: a language-agnostic nREPL client for Emacs'
date: 2026-05-20 09:00 +0300
tags:
- nREPL
- Emacs
- neat
- Clojure
---

> I think I'll take my REPL neat <br>
> My parens black and my bed at three <br>
> CIDER's too sweet for me...
>
> -- Bozier

Last week I [announced Port]({% post_url 2026-05-12-port-a-minimalist-prepl-client-for-emacs %}),
a small prepl client for Emacs. Today I'm following it up with another small
Emacs package. Meet [neat](https://github.com/nrepl/neat), a tiny, deliberately
language-agnostic nREPL client.

## Why?

For years I've been hearing some version of the same request: "could CIDER work
with my non-Clojure nREPL server?". Babashka, Basilisp, nREPL-CLR, even some
homegrown servers people built on top of nREPL for languages I'd never heard
of.[^1] The answer was always the same kind of squishy "sort of, in theory, with
caveats", because while bare nREPL is genuinely language-agnostic, CIDER is
not. CIDER was built for Clojure and assumes Clojure pretty much everywhere.

I always thought the right answer was "let's gradually make CIDER more
language-agnostic." That's the kind of plan that sounds reasonable until
you actually try it.

The thing that pushed me over the edge was, oddly enough, building Port.  Port
is small, focused, and doesn't try to be CIDER. Working on it for a couple of
weeks reminded me how (deceptively) productive it is to start from a clean slate
when the new requirements don't match the assumptions baked into a mature
codebase. Trying to retrofit CIDER into a language-agnostic shape would have
meant fighting with every helper that ever assumed `clojure.repl` exists, every
middleware contract `cider-nrepl` defines, every project-type heuristic that
knows about `deps.edn` and `project.clj` and nothing else.  A whole lot of "is
the server Clojure, or is it the other thing?"  branches. The Port experience
reaffirmed that the right move for a genuinely different client is a *new
project*, not a thousand cuts to an existing one.

So `neat` was born. The name is short, says what it does (it's neat, both in
the small-and-tidy sense and in the "no deps, no special assumptions, just the
protocol" sense), and conveniently leaves room for puns I haven't fully
committed to yet. I might land on a backronym one day. For now it's just
"neat".

## What neat actually is

neat is a small Emacs nREPL client. The code is  split across four files:

- `neat-bencode.el`: bencode encode/decode.
- `neat-client.el`: TCP connections, request dispatch, the standard
  nREPL ops.
- `neat-repl.el`: a comint-derived REPL buffer.
- `neat.el`: the entry point, customization group, and `neat-mode`
  minor mode for source buffers.

It only uses Emacs builtins. There are no external runtime
dependencies, not even on `clojure-mode`, because neat doesn't assume
Clojure on the other end. If you write `clojure-mode`, `fennel-mode`,
`hy-mode`, or anything else that talks nREPL, you turn on `neat-mode`
in that buffer and it just works.

The connection routing is also intentionally library-friendly. There's a
buffer-local `neat-current-connection` override so downstream packages can
implement their own routing logic, plus a global default for the simple
"one server at a time" case that most people will want.

Capability discovery is done at connect time via the nREPL `describe` op.
neat doesn't hardcode "this server has completions, this one doesn't"
assumptions. If the server reports a `completions` op, the CAPF backend
lights up (with type annotations next to each candidate, when the server
provides them). If it reports `lookup`, eldoc starts working and `M-.`
jumps to definitions via an xref backend. If neither is there, you still
get a perfectly serviceable raw REPL.

## Basic usage

Start an nREPL server. Anything that speaks the protocol will do. For a
Clojure server:

```
clj -M:nrepl
# or
bb nrepl-server :port 7888
# or
lein repl :headless :port 7888
```

Then in Emacs:

```
M-x neat RET localhost RET 7888 RET
```

A REPL buffer pops up, the prompt follows the server's reported
namespace, and you can type expressions at it. Multi-line input works
because `RET` only submits when the form parses as balanced under
`neat-repl-input-syntax-table` (Emacs Lisp syntax by default, which is
close enough for any Lisp). Input history is persisted across sessions.

If there's a `.nrepl-port` file in the project, the prompt defaults to
its contents, so `M-x neat RET RET` is enough to connect.

To evaluate from a source buffer, turn on the minor mode:

```
M-x neat-mode
```

The familiar bindings are there, intentionally compatible with what CIDER
users expect:

| Key           | Command                   |
|---------------|---------------------------|
| `C-c C-e`     | `neat-eval-last-sexp`     |
| `C-c C-c`     | `neat-eval-defun`         |
| `C-c C-r`     | `neat-eval-region`        |
| `C-c C-b`     | `neat-eval-buffer`        |
| `C-c C-l`     | `neat-load-buffer-file`   |
| `C-c C-z`     | `neat-switch-to-repl`     |
| `C-c C-k`     | `neat-interrupt-eval`     |
| `C-c M-n`     | `neat-set-ns`             |
| `C-c C-d C-d` | `neat-show-doc-at-point`  |
| `M-.`         | `xref-find-definitions`   |
| `M-,`         | `xref-go-back`            |

`neat-eval-buffer` ships the buffer contents as an `eval` op;
`neat-load-buffer-file` uses the standard `load-file` op instead, so the
server can attribute file and line numbers to errors. Use the latter when
you're actually loading a file from disk and care about good diagnostics.

`neat-set-ns` sets the buffer-local `neat-ns`, which gets sent as the `ns`
field on every `eval` op from that buffer. For languages where the
namespace is declared in the source (Clojure's `(ns foo.bar)`, etc.), swap
in a parser via `neat-buffer-ns-function`.

For juggling multiple connections, `M-x neat-list-connections` opens a
tabulated-list buffer with one row per live connection, where you can
set the default or disconnect interactively.

That's roughly the whole user-facing surface today. There's no jack-in
command, no inspector, no debugger, no test runner. Likely there will never
be, but if you need those you should probably be using CIDER anyways...

## Should you use it?

If you write Clojure and CIDER works for you, keep using CIDER. It's
mature, full-featured, and supported, and I'm going to keep working on it
for as long as people use it. Nothing about neat changes that.

But if you find yourself in one of these situations:

- you write a non-Clojure language whose runtime ships an nREPL server,
  and you've been muddling through with a half-supported CIDER setup,
- you write Clojure but you value minimalism and don't need the full
  CIDER feature set,
- you're building an Emacs package that needs to talk nREPL and you
  want a small, dependency-free library to build on,[^2]

then neat might be a better fit. It's small enough that you can read the
whole thing in an afternoon, and the library/UI split (`neat-bencode` and
`neat-client` are perfectly usable from other packages) is genuinely
designed for downstream consumers.

## The bigger picture

neat is part of a broader push I've been chewing on for a while now: making
nREPL a healthy multi-language ecosystem rather than a Clojure-only protocol. That push has three legs:

1. **An actual nREPL specification.** The [spec.nrepl.org](https://github.com/nrepl/spec.nrepl.org)
   draft is (will be) the formal version of what today is "whatever nREPL the
   project does".
2. **Reference clients.** neat is one. The point of building a
   deliberately Clojure-free client is that it stress-tests the spec.
   Anywhere neat ends up needing to special-case the server, the spec
   has a gap.
3. **A compatibility test suite.** The parameterised integration suite
   in neat already runs the same assertions against multiple servers
   and surfaces real divergences (Clojure batching `(println "hi")`
   into a single `out` message where Basilisp emits two, for example).
   I'd like to grow this into a portable suite that any nREPL server
   can self-check against.

This is also why I keep teasing a "reference CLI client" in
conversations. An editor client is one thing, but a small command-line
nREPL client written in a non-Lisp language would be a much sharper test
of how language-agnostic the protocol really is. neat is plausibly a
precursor to that. Time will tell how far I push this; for now I just
wanted to get the Emacs side moving.

## Thanks

As always, big thanks to [Clojurists
Together](https://www.clojuriststogether.org/) and everyone supporting my open
source work. You make it possible for me to keep tweaking and improving CIDER,
nREPL, clj-refactor, and friends, and occasionally try something "neat" on the
side. `neat` isn't replacing any of the existing Clojure tooling for Emacs. It's
just another tool in the box for the people who want it.

Feedback, ideas, and contributions are most welcome over at the
[issue tracker](https://github.com/nrepl/neat/issues).

Keep hacking!

[^1]: <https://github.com/clojure-emacs/cider/issues/3905>
[^2]: For a long time I planned to extract CIDER's nREPL client code into a reusable package, but now that we have `neat` I probably will finally abandon this idea.
