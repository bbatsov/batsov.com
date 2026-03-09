---
title: Emacs and Vim in the Age of AI
date: 2026-03-09 10:30 +0200
tags:
- Emacs
- Vim
- AI
---

> It's tough to make predictions, especially about the future.
>
> -- Yogi Berra

I've been an Emacs fanatic for over 20 years. I've built and maintained some of
the most popular Emacs packages, contributed to Emacs itself, and spent
countless hours tweaking my configuration. Emacs isn't just my editor -- it's
my passion, and my happy place.

Over the past year I've also been spending a lot of time with Vim and Neovim,
relearning them from scratch and having a blast contrasting how the two
communities approach similar problems. It's been a fun and refreshing
experience.[^1]

And lately, like everyone else in our industry, I've been playing with AI tools
-- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) in particular
-- watching the impact of AI on the broader programming landscape, and
pondering what it all means for the future of programming. Naturally, I keep
coming back to the same question: what happens to my beloved Emacs and its
"arch nemesis" Vim in this brave new world?

I think the answer is more nuanced than either "they're doomed" or "nothing
changes". Predicting the future is obviously hard work, but it's so fun
to speculate on it.

My reasoning is that every major industry shift presents plenty of risks
and opportunities for those involved in it, so I want to spend a bit of time
ruminating over the risks and opportunities for Emacs and Vim.

<!--more-->

## The Risks

### The IDE gravity well

VS Code is already the dominant editor by a wide margin, and it's going to get
first-class integrations with every major AI tool -- Copilot (obviously),
Codex, Claude, Gemini, you name it. Microsoft has every incentive to make VS
Code the best possible host for AI-assisted development, and the resources to
do it.

On top of that, purpose-built AI editors like [Cursor](https://cursor.com),
[Windsurf](https://windsurf.com), and others are attracting serious investment
and talent. These aren't adding AI to an existing editor as an afterthought --
they're building the entire experience around AI workflows. They offer
integrated context management, inline diffs, multi-file editing, and agent
loops that feel native rather than bolted on.

Every developer who switches to one of these tools is a developer who isn't
learning Emacs or Vim keybindings, isn't writing Elisp, and isn't contributing
to our ecosystems. The gravity well is real.

> I never tried Cursor and Windsurf simply because they are essentially
> forks of VS Code and I can't stand VS Code. I tried it several times over
> the years and I never felt productive in it for a variety of reasons.
{: .prompt-info }

### Do you even need a "power tool" anymore?

Part of the case for Emacs and Vim has always been that they make you faster at
writing and editing code. The keybindings, the macros, the extensibility -- all
of it is in service of making the human more efficient at the mechanical act of
coding.

But if AI is writing most of your code, how much does mechanical editing speed
matter? When you're reviewing and steering AI-generated diffs rather than
typing code character by character, the bottleneck shifts from "how fast can I
edit" to "how well can I specify intent and evaluate output." That's a
fundamentally different skill, and it's not clear that Emacs or Vim have an
inherent advantage there.

The learning curve argument gets harder to justify too. "Spend six months
learning Emacs and you'll be 10x faster" is a tough sell when a junior
developer with Cursor can scaffold an entire application in an afternoon.[^2]

### The corporate backing asymmetry

VS Code has Microsoft. Cursor has venture capital. Emacs has... a small group
of volunteers and the FSF. Vim had Bram, and now has a community of
maintainers. Neovim has a small but dedicated core team.

This has always been the case, of course, but AI amplifies the gap. Building
deep AI integrations requires keeping up with fast-moving APIs, models, and
paradigms. Well-funded teams can dedicate engineers to this full-time.
Volunteer-driven projects move at the pace of people's spare time and
enthusiasm.

### The doomsday scenario

Let's go all the way: what if programming as we know it is fully automated
within the next decade? If AI agents can take a specification and produce
working, tested, deployed software without human intervention, we won't need
coding editors at all. Not Emacs, not Vim, not VS Code, not Cursor. The entire
category becomes irrelevant.

I don't think this is likely in the near term, but it's worth acknowledging as
a possibility. The trajectory of AI capabilities has surprised even the
optimists (and I was initially an AI skeptic, but the rapid advancements last
year eventually changed my mind).

## The Opportunities

### AI makes configuration and extension trivial

Here's the thing almost nobody is talking about: Emacs and Vim have always
suffered from the obscurity of their extension languages. Emacs Lisp is a
1980s Lisp dialect that most programmers have never seen before. VimScript is...
VimScript. Even Lua, which Neovim adopted specifically because it's more
approachable, is niche enough that most developers haven't written a line of it.

This has been the single biggest bottleneck for both ecosystems. Not the
editors themselves -- they're incredibly powerful -- but the fact that
customizing them requires learning an unfamiliar language, and most people never
make it past copying snippets from blog posts and READMEs.

I felt incredibly overwhelmed by Elisp and VimScript when I was learning Emacs and
Vim for the first time, and I imagine I wasn't the only one. I started to feel
very productive in Emacs only after putting in quite a lot of time to actually
learn Elisp properly. (never bothered to do the same for VimScript, though,
and admittedly I'm not too eager to master Lua either)

AI changes this overnight. You can now describe what you want in plain English
and get working Elisp, VimScript, or Lua. "Write me an Emacs function that
reformats the current paragraph to 72 columns and adds a prefix" -- done.
"Configure lazy.nvim to set up LSP with these keybindings" -- done. The
extension language barrier, which has been the biggest obstacle to adoption for
decades, is suddenly much lower.

### The same goes for plugin development

After 20+ years in the Emacs community, I often have the feeling that a
relatively small group -- maybe 50 to 100 people -- is driving most of the
meaningful progress. The same names show up in MELPA, on the mailing lists, and
in bug reports. This isn't a criticism of those people (I'm proud to be among
them), but it's a structural weakness. A community that depends on so few
contributors is fragile.

And it's not just Elisp and VimScript. The C internals of both Emacs and Vim
(and Neovim's C core) are maintained by an even smaller group. Finding people
who are both willing and able to hack on decades-old C codebases is genuinely
hard, and it's only getting harder as fewer developers learn C at all.

AI tools can help here in two ways. First, they lower the barrier for new
contributors -- someone who understands the *concept* of what they want to
build can now get AI assistance with the *implementation* in an unfamiliar
language. Second, they help existing maintainers move faster. I've personally
found that AI is excellent at generating test scaffolding, writing
documentation, and handling the tedious parts of package maintenance that slow
everything down.

### AI integrations are already happening

The Emacs and Neovim communities aren't sitting idle. There are already
impressive AI integrations:

**Emacs:**

- [gptel](https://github.com/karthink/gptel) -- a versatile LLM client that
  supports multiple backends (Claude, GPT, Gemini, local models)
- [ellama](https://github.com/s-kostyaev/ellama) -- an Emacs interface for
  interacting with LLMs via llama.cpp and Ollama
- [aider.el](https://github.com/tninja/aider.el) -- Emacs integration for
  [Aider](https://aider.chat), the popular AI pair programming tool
- [copilot.el](https://github.com/copilot-emacs/copilot.el) -- GitHub Copilot
  integration (I happen to be the current maintainer of the project)
- [elysium](https://github.com/lanceberge/elysium) -- an AI-powered coding
  assistant with inline diff application
- [agent-shell](https://github.com/xenodium/agent-shell) -- a native Emacs
  buffer for interacting with LLM agents (Claude Code, Gemini CLI, etc.)
  via the [Agent Client Protocol](https://agentclientprotocol.org/)

**Neovim:**

- [avante.nvim](https://github.com/yetone/avante.nvim) -- a Cursor-like AI
  coding experience inside Neovim
- [codecompanion.nvim](https://github.com/olimorris/codecompanion.nvim) -- a
  Copilot Chat replacement supporting multiple LLM providers
- [copilot.lua](https://github.com/zbirenbaum/copilot.lua) -- native Copilot
  integration for Neovim
- [gp.nvim](https://github.com/Robitx/gp.nvim) -- ChatGPT-like sessions in
  Neovim with support for multiple providers

And this is just a sample. Building these integrations isn't as hard as it
might seem -- the APIs are straightforward, and the extensibility of both
editors means you can wire up AI tools in ways that feel native. With AI
assistance, creating new integrations becomes even easier. I wouldn't be
surprised if the pace of plugin development accelerates significantly.

### Terminal-native AI tools are a natural fit

Here's an irony that deserves more attention: many of the most powerful AI
coding tools are terminal-native. Claude Code, Aider, and various Copilot CLI
tools all run in the terminal. And what lives in the terminal? Emacs and Vim.[^3]

Running Claude Code in an Emacs `vterm` buffer or a Neovim terminal split is
a perfectly natural workflow. You get the AI agent in one pane and your editor
in another, with all your keybindings and tools intact. There's no context
switching to a different application -- it's all in the same environment.

This is actually an advantage over GUI-based AI editors, where the AI
integration is tightly coupled to the editor's own interface. With
terminal-native tools, you get to choose your own editor *and* your own AI
tool, and they compose naturally.

### Emacs as an AI integration platform

Emacs's "editor as operating system" philosophy is uniquely well-suited to AI
integration. It's not just a code editor -- it's a mail client (Gnus, mu4e),
a note-taking system (Org mode), a Git interface (Magit), a terminal emulator,
a file manager, an RSS reader, and much more.

AI can be integrated at every one of these layers. Imagine an AI assistant that
can read your org-mode agenda, draft email replies in mu4e, help you write
commit messages in Magit, and refactor code in your source buffers -- all
within the same environment, sharing context. No other editor architecture
makes this kind of deep, cross-domain integration as natural as Emacs does.

Admittedly, I've stopped using Emacs as my OS a long time ago, and these days
I use it mostly for programming and blogging. (I'm writing this article in Emacs with
the help of `markdown-mode`) Still, I'm only one Emacs user and many are probably
using it in a more holistic manner.

### AI helps you help yourself

One of the most underappreciated benefits of AI for Emacs and Vim users is
mundane: troubleshooting. Both editors have notoriously steep learning curves
and opaque error messages. "Wrong type argument: stringp, nil" has driven more
people away from Emacs than any competitor ever did.

AI tools are remarkably good at explaining cryptic error messages, diagnosing
configuration issues, and suggesting fixes. They can read your init file and
spot the problem. They can explain what a piece of Elisp does. They can help
you understand why your keybinding isn't working. This dramatically flattens
the learning curve -- not by making the editor simpler, but by giving every
user access to a patient, knowledgeable guide.

I don't really need any AI assistance to troubleshoot anything in my Emacs setup,
but it's been handy occasionally in Neovim-land, where my knowledge is
relatively modest by comparison.

### Even in the post-coding apocalypse, Emacs and Vim survive

Let's revisit the doomsday scenario. Say programming is fully automated and
nobody writes code anymore. Does Emacs die?

Not necessarily. Emacs is already used for far more than programming. People
use Org mode to manage their entire lives -- tasks, notes, calendars,
journals, time tracking, even academic papers. Emacs is a capable writing
environment for prose, with excellent support for LaTeX, Markdown, AsciiDoc,
and plain text. You can read email, browse the web, manage files, and yes,
play Tetris.

Vim, similarly, is a text editing paradigm as much as a program. Vim
keybindings have colonized every text input in the computing world -- VS Code,
IntelliJ, browsers, shells, even Emacs (via Evil mode). Even if the Vim
*program* fades, the Vim *idea* is immortal.[^4]

And who knows -- maybe there'll be a market for artisanal, hand-crafted
software one day. "Locally sourced, free-range code, written by a human in
Emacs." I'd buy that t-shirt. And I'm fairly certain those artisan programmers
won't be using VS Code.

So even in the most extreme scenario, both editors have a life beyond code.
A diminished one, perhaps, but a life nonetheless.

## The Bigger Picture

I think what's actually happening is more interesting than "editors die" or
"editors are fine." The role of the editor is shifting.

For decades, the editor was where you *wrote* code. Increasingly, it's
becoming where you *review, steer, and refine* code that AI writes. The
skills that matter are shifting from typing speed and editing gymnastics to
specification clarity, code reading, and architectural judgment.

In this world, the editor that wins isn't the one with the best code
completion -- it's the one that gives you the most control over your workflow.
And that has always been Emacs and Vim's core value proposition.

The question is whether the communities can adapt fast enough. The tools are
there. The architecture is there. The philosophy is right. What's needed is
people -- more contributors, more plugin authors, more documentation writers,
more voices in the conversation. AI can help bridge the gap, but it can't
replace genuine community engagement.

### The ethical elephant in the room

Not everyone in the Emacs and Vim communities is enthusiastic about AI, and
the objections go beyond mere technophobia. There are legitimate ethical
concerns that are going to be debated for a long time:

- **Energy consumption.** Training and running large language models requires
  enormous amounts of compute and electricity. For communities that have long
  valued efficiency and minimalism -- Emacs users who pride themselves on running
  a 40-year-old editor, Vim users who boast about their sub-second startup times
  -- the environmental cost of AI is hard to ignore.

- **Copyright and training data.** LLMs are trained on vast corpora of code and
  text, and the legality and ethics of that training remain contested. Some
  developers are uncomfortable using tools that may have learned from
  copyrighted code without explicit consent. This concern hits close to home
  for open-source communities that care deeply about licensing.

- **Job displacement.** If AI makes developers significantly more productive,
  fewer developers might be needed. This is an uncomfortable thought for any
  programming community, and it's especially pointed for editors whose identity
  is built around empowering *human* programmers.

These concerns are already producing concrete action. The Vim community
recently saw the creation of [EVi](https://codeberg.org/NerdNextDoor/evi), a
fork of Vim whose entire raison d'etre is to provide a text editor free from
AI integration. Whether you agree with the premise or not, the fact that
people are forking established editors over this tells you how strongly some
community members feel.

I don't think these concerns should stop anyone from exploring AI tools, but
they're real and worth taking seriously. I expect to see plenty of spirited
debate about this on emacs-devel and the Neovim issue tracker in the years
ahead.

## Closing Thoughts

> The future ain't what it used to be.
>
> -- Yogi Berra

I won't pretend I'm not worried. The AI wave is moving fast, the incumbents
have massive advantages in funding and mindshare, and the very nature of
programming is shifting under our feet. It's entirely possible that Emacs and
Vim will gradually fade into niche obscurity, used only by a handful of
diehards who refuse to move on.

But I've been hearing that Emacs is dying for 20 years, and it's still here.
The community is small but passionate, the editor is more capable than ever,
and the architecture is genuinely well-suited to the AI era. Vim's situation
is similar -- the core idea is so powerful that it keeps finding new expression
(Neovim being the latest and most vigorous incarnation).

The editors that survive won't be the ones with the flashiest AI features. They'll
be the ones whose users care enough to keep building, adapting, and sharing.
That's always been the real engine of open-source software, and no amount of
AI changes that.

So if you're an Emacs or Vim user: don't panic, but don't be complacent
either. Learn the new AI tools (if you're not fundamentally opposed to them,
that is). Pimp your setup and make it awesome. Write about your
workflows. Help newcomers. The best way to ensure your editor survives the AI
age is to make it thrive in it.

Maybe the future ain't what it used to be -- but that's not necessarily a bad
thing.

That's all I have for you today. Keep hacking!

[^1]: If you're curious about my Vim adventures, I wrote about them in [Learning Vim in 3 Steps]({% post_url 2026-02-24-learning-vim-in-3-steps %}).
[^2]: Not to mention you'll probably have to put in several years in Emacs before you're actually more productive than you were with your old editor/IDE of choice.
[^3]: At least some of the time. Admittedly I usually use Emacs in GUI mode, but I always use (Neo)vim in the terminal.
[^4]: Even Claude Code has vim mode.
