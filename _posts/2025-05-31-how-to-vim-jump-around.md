---
title: 'How to Vim: Jump Around'
date: 2025-05-31 10:09 +0300
tags:
- Vim
---

One of the most important aspects of effective editing is to be able to quickly
move where you want to go in a buffer - e.g. to a specific line, paragraph,
character, word, etc.

When it comes to navigating within the current line in Vim, motion commands like
`f`/`F` and `t`/`T` are very widely used. They basically allow you to navigate
the occurances of a character backwards/forward. (you can cycle through multiple
matches with `;` and `,`)

Like many things in Vim, those are composable with operators like `d`, `c`, etc, which makes
them quite powerful. Consider the example below (where `|` denotes the curson position):

```
|this text is completely useless; but this text isn't
```

If you'd like to delete the useless text you can do this quite fast by pressing `df;`.
Good stuff!

Sadly, however, this wouldn't work in the situation below:

```
|this text is completely useless
this text is completely useless
this text is completely useless; but this text isn't
```

How to delete all the text from our cursor position on the first line to the `;` on the third?
`/` (search) to the rescue! I think many people newer to Vim don't realize that search works
like a motion and can be combined with editing operations. Try typing `d/; <RET>` and see what
happens!

It gets even better! Imagine a slightly more complicated situation:

```
|this text is completely useless;
this text is completely useless;
this text is completely useless; but this text isn't
```

Here we can either repeat the previous action `d/;` 3 times (`.` is our friend)
or use `C-g` (next match) and `C-t` (previous match) to rotate the possible
matches before pressing `<RET>` to confirm our target. Try typing
`d/;<C-t><C-t><C-t><RET>`.

**Note:** The above will work only if you've enabled `incsearch`, which pretty much everyone should
do anyways.

Of course, here I'm just scratching the surface of the many ways to jump around a buffer in Vim.
I think those built-ins can get you pretty far if you master them, but on tops of this there are
plenty of plugins that:

- Extend commands like `f`/`t` to work across multiple lines
- Allow you to jump to any place in the window by pressing a couple of characters (e.g. EasyMotion and its million clones)[^1]

What are your favorite ways to quickly jump around in Vim? Please share those in the comments!

That's all I have for you today! Vim long and prosper!

[^1]: I'm partial for [flash](https://github.com/folke/flash.nvim) in Neovim and [vim-sneak](https://github.com/justinmk/vim-sneak) in "classic" Vim.
