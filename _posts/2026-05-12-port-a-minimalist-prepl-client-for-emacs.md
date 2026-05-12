---
title: 'Port: a minimalist prepl client for Emacs'
date: 2026-05-12 10:00 +0300
tags:
- Clojure
- Emacs
- Port
- prepl
---

For ages I've had "add prepl support to CIDER" sitting somewhere in the back of
my head. CIDER is built firmly around nREPL, but
[prepl](https://clojure.org/reference/repl_and_main#prepl) ships with Clojure
itself, and the appeal of dropping the external REPL server requirement is
obvious.  Recently, as part of a broader internals cleanup "mini-project" in
CIDER, I finally sat down and put a prototype together:
[cider#3899](https://github.com/clojure-emacs/cider/pull/3899).

The good news is that the prototype sort of worked. The bad news is that the
more I poked at it, the more I kept running into the same pattern. CIDER
assumes ops, sessions, request ids, and a whole structured protocol that
prepl simply doesn't have. The amount of CIDER code that would need to grow
"is this nREPL or prepl?" branches added up quickly, and I'd be papering
over prepl's limitations in dozens of subtle places. The exercise was fun,
but it ended up reaffirming my long-standing belief that nREPL is a much
better fit for editor tooling than prepl is.

The exercise did leave me thinking though. What if, instead of bolting prepl
onto CIDER, I built a small standalone client in the spirit of
[inf-clojure](https://github.com/clojure-emacs/inf-clojure) and
[monroe](https://github.com/sanel/monroe)? Something tiny and focused that
doesn't have to pretend to be CIDER, and where prepl's quirks would be the
design rather than something to work around.

Conveniently, I was on vacation in Portugal at the time, where I spent a few
days in Porto, and the name pretty much picked itself.
[Port](https://github.com/clojure-emacs/port) was born. It kept us firmly in the
land of fun, drink-inspired Clojure-on-editor names: CIDER,
[Calva](https://calva.io) (after Calvados, the apple brandy), and now Port (the
famous fortified wine). The protocol Port talks to is `prepl`, over a TCP
**port**, so the pun was hard to pass up.

This time around I didn't manage to land on a backronym I love (at least not
yet). The contenders so far:

- prepl omnipotent repl toolkit (my favorite so far)
- prepl-operated repl toolkit
- peak optimized repl toolkit

Naming is hard. I remain open to better suggestions. :D

## Scope, or what Port isn't

Port is a side project. I don't plan to invest serious time in it past the
point I consider it feature-complete, which won't be far beyond what's
already there. The deliberate goal is to keep it simple and focused, and its
feature set will stay close to inf-clojure and monroe. **Port is not
competing with CIDER.** If you want the full feature set (debugger,
inspector, test runner, profiler, structured stacktraces, refactor support),
CIDER + `cider-nrepl` is, and will remain, the way.

What Port gives you today is a small, dependable Clojure REPL that you can
hook into Emacs without any external dependencies, just a stock Clojure JVM
with a prepl listening on a port.

## A few architectural notes

If you're up for the long version, [doc/design.md](https://github.com/clojure-emacs/port/blob/main/doc/design.md)
goes deep. Here's the short version of what prepl gives you compared to nREPL:

- **No bencode**. prepl emits EDN-tagged maps, one per line. This might be a feature or a problem, depending on your perspective.
- **No middleware**. Whatever the server prints is what you get. No
  interception, no extension surface.
- **No sessions**. There's one thread per TCP connection.
- **No ops**. You send a Clojure form, the server evaluates it, and prints
  back tagged messages: `:ret`, `:out`, `:err`, `:tap`, plus an `:exception
  true` flag on errors.
- **No request id**. This is the main issue. Tags identify the *kind* of
  message, not which request produced it.

That last point is the central design constraint. If you want to issue a
request and reliably read back its result without accidentally consuming
output from an unrelated background `future` or `tap>`, you need to layer
correlation on top of the protocol. Port does this with two tricks:

1. **Two sockets per session.** A user socket drives the REPL buffer with
   raw streaming output, and a separate tool socket carries helper-command
   requests. Background prints from `future`/`agent` on the user thread
   don't bleed into the tool channel.
2. **A bootstrap form.** On connect, the tool socket evaluates a one-shot
   form that defines a `port.tooling/-eval` wrapper. Every subsequent helper
   call goes through it, which captures `*out*` / `*err*` and returns a
   tagged map containing the request id. The client matches the id against
   a pending-callback registry.

This is what nREPL provides via sessions and ops, just reinvented at the TCP
layer. It's a fair amount of work for something nREPL gives you for free,
which only strengthens my view that nREPL is the better protocol for editor
tooling. Still, it was an interesting and educational exercise.

## Zero dependencies

One thing I'm fairly proud of: Port has no hard dependencies. You'll want either
`clojure-mode` or `clojure-ts-mode` installed for the source-buffer side of
things, but Port itself only soft-depends on them via runtime `fboundp`
checks. Hook it onto whichever one(s) you actually use. I intend to keep it that
way. Dependency creep is a real problem in the Emacs (every?) ecosystem, and a small
package should stay small.

## What's there in 0.1

I tagged [v0.1.0](https://github.com/clojure-emacs/port/releases/tag/v0.1.0)
yesterday. It's small but already perfectly usable:

- `M-x port` "jacks in" (bootstraps) (auto-detects `deps.edn` / `project.clj` /
  `bb.edn`, starts a server and connects to it)
- single-buffer REPL with persistent input history, completion, and eldoc
  at the prompt
- interactive evaluation from source buffers with pretty-printed results
- structured stacktrace buffer with cause chain and navigable frames
- `M-.` find-definition that follows into jar sources
- doc/source/apropos/macroexpand helpers

MELPA submission is queued up next. After that, expect Port to be in
[burst-driven](/articles/2025/11/16/burst-driven-development-my-approach-to-oss-projects-maintenance/)
maintenance mode like most of my smaller projects.

Feedback, ideas, and contributions are most welcome. The
[issue tracker](https://github.com/clojure-emacs/port/issues) is the right
place.

## Closing thoughts

Funny thing, I'd never actually written any code against prepl until I
started this project. It was fun to spend some quality time with the
"competition" of my beloved nREPL. Working with a different protocol always
teaches you something about the one you're used to, and I came away from
this with a renewed appreciation for both: prepl is genuinely elegant for
what it is, and nREPL is genuinely well-designed for what we use it for.

Big thanks to [Clojurists Together](https://www.clojuriststogether.org/) and
everyone else who supports my OSS Clojure work. You rock! Now if you'll
excuse me, I have new releases of CIDER, clj-refactor, and refactor-nrepl
to get back to.

Keep hacking!
