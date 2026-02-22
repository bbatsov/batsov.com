---
title: 'How to Vim: To the Terminal and Back'
date: 2026-02-22 20:50 +0200
tags:
- Vim
description: Comparing Vim's two main approaches to shell access -- suspending with Ctrl-Z and the built-in terminal emulator.
---

Sooner or later every Vim user needs to drop to a shell -- to run tests, check
git status, or just poke around. Vim gives you two very different ways to do
this: the old-school `Ctrl-Z` suspend and the newer `:terminal` command. Let's
look at both.

## The Old-School Way: Ctrl-Z

Pressing `Ctrl-Z` in Vim sends a `SIGTSTP` signal that suspends the entire Vim
process and drops you back to your shell. When you're done, type `fg` to bring
Vim back exactly where you left it.

```
$ vim myfile.rb
# (press Ctrl-Z)
[1]+  Stopped     vim myfile.rb
$ git status
$ fg
# back in Vim, right where you left off
```

You can also use `:suspend` or `:stop` from command mode if you prefer.

**Pros:**

- Dead simple -- no configuration, works everywhere.
- You get your *real* shell with your full environment, aliases, and all.
- Zero overhead -- Vim stays in memory, ready to resume instantly.

**Cons:**

- You can't see Vim and the shell at the same time.
- Easy to forget you have a suspended Vim session (check with `jobs`).
- Doesn't work in GUIs like gVim or in terminals that don't support job control.

## The Modern Way: :terminal

Vim 8.1 (released in May 2018) introduced `:terminal` -- a built-in terminal
emulator that runs inside a Vim window. This was a pretty big deal at the time,
as I'll explain in a moment.

The basics are simple:

- `:term` -- opens a terminal in a horizontal split
- `:vert term` -- opens it in a vertical split
- `Ctrl-\ Ctrl-N` -- switches from Terminal mode back to Normal mode (so you
  can scroll, yank text, etc.)

> In Neovim the key mapping to exit terminal mode is the same (`Ctrl-\ Ctrl-N`),
> but you can also set up a more ergonomic alternative like `<Esc>` by adding
> `tnoremap <Esc> <C-\><C-N>` to your config.
{: .prompt-tip }

### Running Commands Directly

One of the most useful aspects of `:terminal` is running a specific command:

```vim
:term ruby %        " run the current file with Ruby
:term python3 %     " run it with Python
:term make          " run make
:term pytest        " run your test suite
```

The `%` expands to the current filename, which makes this a quick way to test
whatever you're working on without leaving Vim. The output stays in a buffer you
can scroll through and even yank from -- handy when you need to copy an error
message.

### :terminal vs :! (Bang Commands)

You might be wondering how `:terminal` compares to the classic `:!` command.
The main difference is that `:!` blocks Vim until the command finishes and then
shows the output in a temporary screen -- you have to press Enter to get back.
`:terminal` runs the command in a split window, so you can keep editing while it
runs and the output stays around for you to review.

For quick one-off commands like `:!ls` or `:!git diff`, bang commands are fine.
For anything with longer-running output -- tests, build commands, interactive
REPLs -- `:terminal` is the better choice.

## A Bit of History

The story of `:terminal` is intertwined with the story of Neovim. When Neovim
was forked from Vim in early 2014, one of its key goals was to add features that
Vim had resisted for years -- async job control and a built-in terminal emulator
among them. Neovim shipped its terminal emulator (via libvterm) in 2015, a full
three years before Vim followed suit.

It's fair to say that Neovim's existence put pressure on Vim to modernize. Bram
Moolenaar himself [acknowledged](https://lwn.net/Articles/713114/) that "Neovim
did create some pressure to add a way to handle asynchronous jobs." Vim 8.0
(2016) added async job support, and Vim 8.1 (2018) brought the terminal
emulator. Competition is a wonderful thing.

## Do You Even Need a Terminal in Your Editor?

Here's the honest truth: I rarely use `:terminal`. Not in Vim, and not the
equivalent in Emacs either (`M-x shell`, `M-x vterm`, etc.).

I much prefer switching to a proper terminal emulator -- these days that's
[Ghostty](https://ghostty.org/) for me -- where I get my full shell experience
with all the niceties of a dedicated terminal (proper scrollback, tabs, splits,
ligatures, the works). I typically have Vim in one tab/split and a shell in
another, and I switch between them with a keystroke.

I get that I might be in the minority here. Many people love having everything
inside their editor, and I understand the appeal -- fewer context switches,
everything in one place. If that's your style, `:terminal` is a perfectly solid
option. But if you're already comfortable with a good terminal emulator, don't
feel pressured to move your shell workflow into Vim just because you can.

That's all I have for you today. Keep hacking!
