---
title: Rediscovering Vim
date: 2025-05-20 18:09 +0300
tags:
- vim
- Emacs
---

Most people who know me and follow my open-source work are likely going to be surprised
that I'm writing an article about Vim. After all, I've been a devout member of the Church
of Emacs for 20 years now, and I've spent a lot of time preaching the gospel of Emacs and
building extensions for Emacs.

I think a lot fewer people know that I was using vim briefly, before switching to Emacs in 2005.
In the beginning of this year I felt it was a good time to revisit Vim and (neovim) and see
how they've evolved compared to my beloved Emacs. After all, it's never a bad idea to keep an eye on
the competition - they are always a great source of "inspiration".

The main reason why I abandoned vim back in the day is quite simple - I really dislike vimscript.
It was a horrible language back then and I think it's still a horrible language today.[^1]
I'll admit that the rise of neovim (and it's switch to Lua) was probably the main reason
why the thought of revisiting Vim after so many years even crossed my mind.
There's also the fact that it remains the only other editor (besides Emacs),
that's essentially building material for those of us crazy enough to want to build their own editor.

Starting with Vim is always intimidating, even if you kind of know the basics. The configuration options
are countless and so is the Vim plugin ecosystem. `:help` is your friend!

Initially, I focused on neovim for a couple of reasons:

- Lua for the configuration and the plugins
- Built-in support for things like LSP and Tree-sitter
- Many distros and a ton of "modern" plugins

I spent a lot of time with LazyVim (and a bit with Kickstart.nvim) and while I was mostly OK with them,
I didn't like the process of starting with a huge configuration and working from there to make it my own.
So, after a bit of soul-searching I've decided to start at the very beginning with Vim 9 and a blank `.vimrc`
and work my way up from there, same as I did with Emacs 20 years ago.

For a while I didn't use any plugins at all, apart from a handful of plugins bundled with Vim. Eventually
I had to install a few plugins as Vim is so spartan out-of-the-box, compared to most other editors. I was reminded
that you need plugins for basic things like:

- VCS
- commenting out stuff
- surrounding stuff in paired delimiters
- working with paired delimiters in general (e.g. auto-insert/skip closing quotation marks or brackets)

Frankly, I'm a bit shocked how those things never made their way to Vim proper, but I guess there's some
reason for this. Emacs is often considered to be a super-conservative and slow-moving editor, but it
almost feels progressive compared to Vim.[^2]

I was also a bit confused by the native Vim 8 package system, as it's little
more then some predefined locations and structure for the Vim packages.  I had
expected something closer to Emacs's `package.el`. Anyways `vim-plug` is pretty
good and there are a ton of other package managers out there.

So, what am I doing with Vim these days? I'm working my way through the classic book
"Practical Vim" and gradually building up my modest `.vimrc`. I'm trying to use Vim
for as many tasks as I can (e.g. writing this blog article), but admittedly every time
I need to do something more complex I switch back to Emacs. Knowing myself and my
habit of always diving deeper than it usually makes sense I'll likely play a bit
with the dreaded Vim script and Vim9 script before revisiting neovim eventually.

The "real" Vim seems to be in a pretty weird place right now:

- Vim9 is here, but few people are interested in using it, as Vim9 won't be supported on neovim for obvious reasons
- As neovim has much better LSP support, and Tree-sitter support the majority of the developers using Vim have switched to neovim already
- Vim's future seems quite uncertain after the passing of its author and primary maintainer

Still, for me it's always fun to learn something new and to challenge myself to work in a different manner.
I've never really bought the claims that Vim's modal editing model is better than the competition, but I can't
deny it has a certain charm to it and it often forces you to think about achieving the desired end result
with as little keystrokes as possible. 

I don't see myself switching to Vim (or neovim) as my primary editor, but I can imagine adopting `evil-mode`
in Emacs if I start seeing some tangible benefits from Vim's way. Even before I began my experiments with Vim,
I'd occasionally use `evil-mode` in Emacs when I primarily had to read something as opposed to edit it - pressing
fewer modifier keys is always nice. The other potential benefit from my foray in the Vim that I envision is that
editors like VS Code and Zed typically have a lot better Vim emulation than Emacs, as there are so much more
Vim users out there. Recently my F# exploits forced me to spend a bit of time in VS Code and dealing with its
native keybindings was a lot of pain for me.

Down the road I plan to write a couple of follow-up articles on things like:

- my Vim configuration
- comparisons between Vim and Emacs today
- some tips and tricks

Perhaps I'll even write a guide to Vim for Emacs users, as I really needed one when I was starting out.

That's all I have for you today. Now I have to figure out how to commit this article with Fugitive
and exit Vim. Wish me luck!

[^1]: I have to admit that I can tolerate vim9 script.
[^2]: There was a push in Emacs in recent years to modernize the out-of-the-box experience.
