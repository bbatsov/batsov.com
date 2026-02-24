---
title: Learning Vim in 3 Steps
date: 2026-02-24 11:00 +0200
tags:
- Vim
description: A simple 3-step plan for learning Vim -- read, configure, practice. And maybe a fallback plan involving Emacs.
---

Every now and then someone asks me how to learn Vim.[^1] My answer is always the
same: it's simpler than you think, but it takes longer than you'd like. Here's
my bulletproof 3-step plan.

## Step 1: Learn the Basics

Start with `vimtutor` -- it ships with Vim and takes about 30 minutes. It'll
teach you enough to survive: moving around, editing text, saving, quitting. The
essentials.

Once you're past that, I strongly recommend [Practical
Vim](https://pragprog.com/titles/dnvim2/practical-vim-second-edition/) by Drew
Neil. This book changed the way I think about Vim. I had known the basics of
Vim for over 20 years, but the Vim editing model never really *clicked* for me
until I read it. The key insight is that Vim has a **grammar** -- operators
(verbs) combine with motions (nouns) to form commands. `d` (delete) + `w`
(word) = `dw`. `c` (change) + `i"` (inside quotes) = `ci"`. Once you
internalize this composable language, you stop memorizing individual commands
and start *thinking in Vim*. The book is structured as 121 self-contained tips
rather than a linear tutorial, which makes it great for dipping in and out.

> You could also just read `:help` cover to cover -- Vim's built-in
> documentation is excellent. But let's be honest, few people have that kind of
> patience.
{: .prompt-info }

Other resources worth checking out:

- [Advent of Vim](https://www.youtube.com/playlist?list=PLAgc_JOvkdotxLmxRmcck2AhAF6GlbhlH)
  -- a playlist of short video tutorials covering basic Vim topics. Great for
  visual learners who prefer bite-sized lessons.
- [ThePrimeagen's Vim Fundamentals](https://theprimeagen.github.io/vim-fundamentals/)
  -- if you prefer video content and a more energetic teaching style.
- [vim-be-good](https://github.com/ThePrimeagen/vim-be-good) -- a Neovim
  plugin that gamifies Vim practice. Good for building muscle memory.

## Step 2: Start Small

Resist the temptation to grab a massive Neovim distribution like
[LazyVim](https://www.lazyvim.org/) on day one. You'll find it overwhelming if
you don't understand the basics and don't know how the Vim/Neovim plugin
ecosystem works. It's like trying to drive a race car before you've learned how
a clutch works.

Instead, start with a minimal configuration and grow it gradually. I wrote
about this in detail in [Build your .vimrc from
Scratch]({% post_url 2026-02-22-how-to-vim-build-your-vimrc-from-scratch %})
-- the short version is that modern Vim and Neovim ship with excellent defaults
and you can get surprisingly far with a handful of settings.

I'm a tinkerer by nature. I like to understand how my tools operate at their
fundamental level, and I always take that approach when learning something new.
Building your config piece by piece means you understand every line in it, and
when something breaks you know exactly where to look.

## Step 3: Practice for 10 Years

I'm only half joking. Peter Norvig's famous essay [Teach Yourself Programming
in Ten Years](https://norvig.com/21-days.html) makes the case that mastering
any complex skill requires sustained, deliberate practice over a long period --
not a weekend crash course. The same applies to Vim.

Grow your configuration one setting at a time. Learn Vimscript (or Lua if
you're on Neovim). Read other people's configs. Maybe write a small plugin.
Every month you'll discover some built-in feature or clever trick that makes
you wonder how you ever lived without it.

> One of the reasons I chose Emacs over Vim back in the day was that I really
> hated Vimscript -- it was a terrible language to write anything in. These days
> the situation is much better: Vim9 Script is a significant improvement, and
> Neovim's switch to Lua makes building configs and plugins genuinely enjoyable.
{: .prompt-info }

Mastering an editor like Vim is a lifelong journey. Then again, the way things
are going with LLM-assisted coding, maybe you should think long and hard about
whether you want to commit your life to learning an editor when half the
industry is "programming" without one. But that's a rant for another day.

## Plan B

If this bulletproof plan doesn't work out for you, there's always Emacs. Over
20 years in and I'm still learning new things -- these days mostly how to make
the best of [evil-mode](https://github.com/emacs-evil/evil) so I can have the
best of both worlds. As I like to say:

> The road to Emacs mastery is paved with a lifetime of `M-x` invocations.

That's all I have for you today. Keep hacking!

[^1]: Just kidding -- everyone asks me about learning Emacs. But here we are.
