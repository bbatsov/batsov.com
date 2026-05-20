---
title: nREPL Forever
date: 2026-05-20 09:00 +0300
tags:
- Clojure
- nREPL
- prepl
- Meta
---

Last week I [announced Port](/articles/2026/05/12/port-a-minimalist-prepl-client-for-emacs/),
a small prepl client for Emacs. That post focused on Port itself, but writing it left
me with the itch to do a follow-up on the bigger picture, because the
socket REPL / prepl story is one I've been meaning to write up for years.

If you've been around Clojure long enough, you remember the chatter. Socket
REPL landed in Clojure 1.8 (January 2016), prepl in Clojure 1.10 (December
2018), and for a couple of years there was a steady stream of posts, tweets,
and Slack threads to the effect of "this is what we should be building
tools on. nREPL is on the way out." Some serious people put their weight
behind that idea, and some of them went and built tools to prove it.

Now it's 2026 and we can take stock.

## What the "hopeful" era looked like

The pitch was good. Socket REPL is just *the* Clojure REPL exposed on a TCP
port. prepl wraps it with a structured printer so the bytes coming back are
EDN-tagged maps (`:ret`, `:out`, `:err`, `:tap`) instead of a human-readable
prompt. Both ship with Clojure itself. No external server library, no
middleware, no third-party namespaces. You start a JVM, you bind a port,
you're done.

The intellectual case for moving off nREPL had been made by Rich Hickey
himself, most clearly in a [March 2015 clojure-dev
post](https://groups.google.com/g/clojure-dev/c/Dl3Stw5iRVA/m/IHoVWiJz5UIJ)
that's worth reading in full. Rich didn't actually attack nREPL by name in
that message. What he did was argue carefully for what a REPL *is*: a thing
that reads characters, evaluates forms, prints results, and loops, with
those streams available to user code so that things like nested REPLs and
debuggers compose naturally. The money line:

> While framing and RPC orientation might make things easier for someone
> who just wants to implement an eval window, it makes the resulting
> service strictly less powerful than a REPL.

His proposal, in the same post, was that tools should open *multiple*
connections to the running program: one for the human-facing stream, and
dedicated channels for IDE operations. The socket REPL (which landed in
1.8 the following January) and prepl (which arrived in 1.10) were the
official implementation of that worldview.

A handful of editor projects took the cue and built clients:

- [Tutkain](https://tutkain.flowthing.me/) for Sublime Text
- [Chlorine](https://github.com/mauricioszabo/atom-chlorine) for Atom
- [Conjure](https://github.com/Olical/conjure) for Neovim (in its early Rust incarnation)
- [Clojure-Sublimed](https://github.com/tonsky/Clojure-Sublimed) by Nikita Tonsky
- a steady drip of smaller experiments around [`unrepl`](https://github.com/Unrepl/unrepl), [`propel`](https://github.com/Olical/propel), and friends

It was real momentum. If you were following Clojure tooling in 2018-2020, it
genuinely felt like nREPL might be the past, and the future would be some
combination of socket REPL plus a thin self-installing protocol on top of it.
You can find a fair number of "RIP nREPL" hot takes from that period if you go
looking.

## What actually happened

I went and surveyed each of those projects recently while working on Port.
The pattern is depressingly consistent:

**Tutkain** started on prepl. In November 2021, its v0.11 release explicitly
stopped using prepl message framing and switched to a hand-rolled EDN-RPC
protocol that Tutkain boots onto the raw socket REPL by sending it a
base64-encoded blob. The new protocol has request ids, op dispatch
(`:eval`, `:lookup`, `:completions`, `:test`, `:apropos`, `:interrupt`,
...), and server-managed thread bindings. In other words: Tutkain grew
into nREPL, just spelled differently.

**Chlorine** never used prepl directly. It used socket REPL plus an
`unrepl`-style upgrade blob. Its author's successor project,
[Lazuli](https://github.com/mauricioszabo/lazuli), abandoned the whole
approach in favor of nREPL. The
[post-mortem](https://mauricio.szabo.link/blog/2024/11/09/goodbye-chlorine-and-welcome-lazuli/)
is worth reading and is fairly blunt: tools that attempted prepl went back
to nREPL because, honestly, it's simply better.

**Conjure** had a prepl client in its early Rust days. The current
Lua/Fennel rewrite ships only an nREPL client. The author's reasoning in
the release notes was that nREPL "has complete ecosystem adoption and
brilliant ClojureScript support."

**Clojure-Sublimed** technically still talks to a raw socket REPL, but only
after sending it an EDN-printing prelude that upgrades the REPL to a
structured protocol of tonsky's own design. His
[post on the topic](https://tonsky.me/blog/clojure-sublimed-3/) is one of
the most thoughtful pieces I've read on Clojure REPL design, and his
conclusion is roughly: the bare socket REPL is *more* useful than prepl
because you can install your own protocol on top of it. Which is true. But
notice that everyone who reached that conclusion ended up reinventing the
same wheel: ids, ops, request/response correlation, completion support,
lookup, interrupts. You know, the things nREPL has had since 2010.

So the trajectory looks roughly like this:

1. Editor decides nREPL is too heavy or an undesirable external dependency and starts on prepl.
2. Editor discovers prepl has no ids, no ops, no interrupts, no server-side
   completion, no namespace tracking, no test runner integration, etc.
3. Editor rolls a custom protocol on top of socket REPL, or...
4. Editor gives up and goes to nREPL.

Pure prepl clients are nearly extinct in the wild. The one I found that
qualifies is [propel](https://github.com/Olical/propel) by Oliver Caldwell
(of Conjure fame), which is delightful, about 70 lines of Clojure, and
explicitly synchronous (one outstanding eval at a time). That works! But
it's not a foundation for the kind of feature set people expect from an
editor.

## "Real" REPL vs. tool-friendly REPL

Here's where I land. Rich isn't wrong that prepl is closer to a "real" REPL
in the strict sense. prepl genuinely is a more faithful encoding of
read-eval-print: each form goes in, each result comes out, and the semantics
match what you'd get at the standard REPL prompt.

The thing is, "real REPL" is not the property you optimize for when you're
building editor tooling. The properties editor tooling actually needs are:

- A way to **correlate** a request with its response when output and results
  are interleaved.
- A way to **multiplex** -- one connection, several logical conversations.
- **Server-side hooks** for the operations every IDE expects: completion,
  lookup, go-to-definition, find-references, test running, stacktrace
  structuring, interrupt.
- A **protocol stable enough** that ten different editors can target it
  without each one inventing its own dialect.

nREPL was *explicitly designed* for those properties. The
[ops, middleware, and transport](https://nrepl.org/nrepl/design/overview.html)
abstractions exist precisely because the people building it knew the consumers
are not humans typing at a prompt, they're programs negotiating a session.

Calling nREPL "not a real REPL" is technically defensible and practically
beside the point. Nobody on the consuming end is confused about what nREPL
is *for*.

## Taking stock, eight years later

I [wrote about nREPL's revival in 2018](https://metaredux.com/posts/2018/10/29/nrepl-redux.html).
At that point I had just finished migrating the project out of Clojure Contrib,
and the goal was to give it a real home and a working development process. It
was a lot of work, but in hindsight things played out pretty well.

Looking at where things ended up:

- **nREPL itself** is healthier than it has ever been. Active maintainers, a
  proper [manual](https://nrepl.org), a steady release cadence, an actual
  ecosystem organization on GitHub.
- **Most popular Clojure editors** support it. [CIDER](https://github.com/clojure-emacs/cider),
  [Calva](https://calva.io), [Cursive](https://cursive-ide.com) (via its
  own client), Conjure, [vim-iced](https://github.com/liquidz/vim-iced),
  you name it.
- **[babashka](https://babashka.org)** ships with nREPL built in. You boot a
  `bb` and you get an nREPL server, no extra dependencies. That's how a lot
  of people use nREPL in scripting contexts today, and it's been a hit.
- **[basilisp](https://github.com/basilisp-lang/basilisp)** (the Clojure
  dialect on Python) has [nREPL support](https://github.com/basilisp-lang/basilisp-nrepl-async).
  nREPL running on Python, talking to Emacs, evaluating Clojure. Nice.
- **[ClojureCLR](https://github.com/clojure/clojure-clr)** has a working nREPL
  story now, and **[jank](https://jank-lang.org)** (the C++ Clojure) has nREPL
  on its roadmap too.
- The middleware ecosystem ([`cider-nrepl`](https://github.com/clojure-emacs/cider-nrepl),
  [`refactor-nrepl`](https://github.com/clojure-emacs/refactor-nrepl),
  [`piggieback`](https://github.com/nrepl/piggieback),
  [`sayid`](https://github.com/clojure-emacs/sayid),
  [`drawbridge`](https://github.com/nrepl/drawbridge), ...) is alive, well,
  and continues to add features.

Meanwhile prepl is, as best as I can tell, mostly a curiosity. It got me a
side project I had fun with. It did not displace nREPL.

## What I take from this

The history of tooling protocols is full of cases where "purer", "simpler",
or "more elegant" lost to "shipped, documented, and battle-tested." LSP
beat fifteen ad-hoc language protocols. DAP beat the same fifteen
debuggers. nREPL beat prepl in the (Clojure) editor space.

It's not that the simpler thing is bad. prepl is a fine, elegant little
protocol, and there's a real case for embedding it in CI scripts, ops
automation, deployment pipelines, or anywhere you want to drive a Clojure
VM programmatically without pulling in a server library. Use it there.

But for editor tooling? The Clojure community made an enormous, multi-year,
multi-tool investment in nREPL. We have the protocol, the middleware, the
manual, the books, the conference talks. nREPL works, it's actively
maintained, it's increasingly portable across Clojure dialects, and the
design decisions that Rich called out as un-REPL-like are the exact ones
that make it a good substrate for editors.

So I'll say what I felt awkward saying back in 2018: nREPL forever. It's
the right abstraction for the job, and it's not going anywhere.

One more thing. After finishing Port I got curious what a minimal nREPL
client would look like by comparison, so I went and built one. As you can
imagine, it turned out to be significantly simpler. If that sounds
interesting, take a look at
[neat]({% post_url 2026-05-20-neat-a-language-agnostic-nrepl-client-for-emacs %}),
a small, language-agnostic nREPL client for Emacs.

Keep hacking!
