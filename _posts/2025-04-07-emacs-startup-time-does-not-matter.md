---
title: Emacs Startup Time Doesn't Matter
date: 2025-04-07 10:24 +0300
tags:
- Emacs
---

Every now and then I see people discussion one of the following:

- How editor X has faster startup time than Emacs (in a classic apples to oranges comparison style) and Emacs sucks because it doesn't start "fast enough"
- How certain config changes or Elisp hacks optimized by 0.5 seconds someone's Emacs startup time

Here's one hot take from me - none of this really matters. Emacs startup time doesn't matter.[^1]
Why so? Because of how people normally use Emacs, compared to some other editors:

- If you're the type of person who uses Emacs in the terminal, you're likely using `emacs --daemon`
- If you're the type of person who uses Emacs's GUI - you don't restart it very often
- If you're the type of person who uses both - you're definitely using `emacs --daemon` (or you're missing out a lot if you're not)

In the end of the day Emacs sessions tend to be "long-lived". By this I mean that often I restart Emacs only
when I restart my computer. I recall `M-x uptime` often showing 3+ months of uptime. So, why then
would I care about micro-optimizations to my startup time?

I think those conversations are mostly driven by users coming from other editors (usually `vim`), where
people have pretty different usage patterns - e.g. they'd work on files in one project, exit their editor, start it again, ad infinitum. For them - startup time probably matters a lot...

But they also care a lot about their shell, terminal emulator, terminal multiplexer (e.g. `tmux`) and
in the world of Emacs none of those are really as important, as Emacs is the unifying interface of everything
that we need to use.[^2]

Emacs is different. Emacs is special. Emacs startup time doesn't matter most of the time. Remember this.
M-x forever!

**Update:** This short article sparkled a lively discussion on [Reddit](https://www.reddit.com/r/emacs/comments/1jtja9s/emacs_startup_time_doesnt_matter/).

[^1]: Unless it's insanely slow, that is.
[^2]: I've noticed most Emacs users really struggle to understand the value of something like `tmux`, as Emacs is the ultimate window multiplexer for pretty much anything.
