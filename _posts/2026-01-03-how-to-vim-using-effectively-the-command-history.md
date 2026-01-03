---
title: 'How to Vim: Using Effectively the Command History'
date: 2026-01-03 18:48 +0200
tags:
- Vim
---

One of the frustrating aspects of Vim for me is that in insert mode you're quite
limited in what you can do. That's fine most of the time, except when you're in
command-line mode (meaning you're typing something like `:s`). In command-line
mode there's no way to switch to normal mode, so if you make a typo or want to
edit a command you've previous invoked that's a bit painful.

Recently I've discovered a way to mitigate the problem - namely the command history
window. (see `:h cmdwin` for details) You can trigger in a couple of ways:

- Press `:` and afterwards do a `Ctrl-F`
- Press `q:`

When the command history window opens you can edit its contents like any other buffer and
normally you'd find some command that's of interest to you, perhaps edit it, and
afterwards press RET to run it. You can also close the window with either `:q` or `Ctrl-C`.

Note, that the command history window is special and while you're in it you can't really
move around (e.g. switch to another window with `Ctrl-W W`)

For me the main use of the command history window is to reuse and tweak longer `:s` and
`:g` commands, but I can imagine it having other uses as well. It's certainly a good
addition to any Vimmer's tool belt.

Going to back to the original problem I posted - how do you fix a typo while entering
some command? Imagine you wrote the following:

```
:s%/foo/bar/gc
```

Just press `Ctrl-F` and you can instantly fix the command in the command window.
Now you can just do a `^xp` (remember you're in normal mode) and press RET to
fire the fixed command.

```
:%s/foo/bar/gc
```

That's all I have for you today. Keep hacking!

**P.S.** If only Vim's insert mode supported `readline`'s keybindings or
something similar...  You can, however, get something similar using plugins
like [rsi.vim](https://github.com/tpope/vim-rsi) and
[readline.vim](https://github.com/ryvnf/readline.vim).
