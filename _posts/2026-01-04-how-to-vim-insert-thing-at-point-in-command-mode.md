---
title: 'How to Vim: Insert Thing at Point in Command Mode'
date: 2026-01-04 11:11 +0200
tags:
- Vim
---

Most Vim users probably know that they can use `Ctrl-R` to
insert the contents of registers, while typing some command.
For instance - you can insert the clipboard contents with
`Ctrl-R +`.

Fewer people probably know that while typing a command you can
use `Ctrl-R` to insert the object under the cursor. There
are several objects supported by this:

* `CTRL-F` - the Filename under the cursor
* `CTRL-P` - the Filename under the cursor, expanded with `path` as in `gf`
* `CTRL-W` - the Word under the cursor
* `CTRL-A` - the WORD under the cursor
* `CTRL-L` - the line under the cursor

When `incsearch` is set the cursor position at the end of the
currently displayed match is used.  With `CTRL-W` the part of
the word that was already typed is not inserted again.

I find those keybindings handy when doing commands like:

- `:%s/current/substitute/g`
- `/` (search)
- `:vimgrep`

For me `Ctrl-R Ctrl-W` is the most commonly used keybinding, but
all of them have their uses from time to time.

As usual you can find much more on the topic in Vim's user manual.
Try `:help c_CTRL-R` and see where this takes you.

That's all I have for you today. I hope some of you will find some of those commands useful.
