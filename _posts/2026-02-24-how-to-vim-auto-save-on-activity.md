---
title: 'How to Vim: Auto-save on Activity'
date: 2026-02-24 10:30 +0200
tags:
- Vim
description: How to set up auto-saving in Vim, from simple autocommands to plugins, and why you might not need any of it.
---

Coming from Emacs, one of the things I missed most in Vim was auto-saving. I've
been using my own [super-save](https://github.com/bbatsov/super-save) Emacs
package for ages -- it saves your buffers automatically when you switch between
them, when Emacs loses focus, and on a handful of other common actions. After
years of using it I've completely forgotten that `C-x C-s` exists. Naturally, I
wanted something similar in Vim.

## The Autocommand Approach

Vim's autocommands make it straightforward to set up basic auto-saving. Here's
what I ended up with:

```vim
autocmd FocusLost,InsertLeave * silent! update
```

This saves the current buffer when Vim loses focus (you switch to another
window) and when you leave Insert mode. A few things to note:

- `update` instead of `w` -- it only writes when the buffer has actually changed,
  avoiding unnecessary disk writes.
- `silent!` -- suppresses errors for unnamed buffers and read-only files that
  can't be saved.

You can extend this with more events if you like:

```vim
autocmd FocusLost,InsertLeave,TextChanged * silent! update
```

Adding `TextChanged` catches edits made in Normal mode (like `dd`, `x`, or
paste commands), so you're covered even when you never enter Insert mode.

> `FocusLost` works reliably in GUI Vim and most modern terminal emulators, but
> it may not fire in all terminal setups (especially inside tmux without
> additional configuration). One more point in favor of using Ghostty and not
> bothering with terminal multiplexers.
{: .prompt-warning }

## Neovim

The same autocommands work in Neovim. You can put the equivalent in your
`init.lua`:

```lua
vim.api.nvim_create_autocmd({ "FocusLost", "InsertLeave", "TextChanged" }, {
  command = "silent! update",
})
```

Neovim also has `autowriteall` (`:set autowriteall`) which automatically saves
before certain commands like `:next`, `:make`, and `Ctrl-Z`. It's not a full
auto-save solution, but it's worth knowing about.

## Auto-save Plugins

There are several plugins that take auto-saving further, notably
[vim-auto-save](https://github.com/907th/vim-auto-save) for Vim and
[auto-save.nvim](https://github.com/okuuva/auto-save.nvim) for Neovim.

Most of these plugins rely on `CursorHold` -- an event that fires after the
cursor has been idle for `updatetime` milliseconds. The problem is that
`updatetime` is a global setting that also controls how often swap files are
written, and other plugins depend on it too. Setting it to a very low value
(say, 200ms) for snappy auto-saves can cause side effects -- swap file churn,
plugin conflicts, and in Neovim specifically, `CursorHold` can behave
inconsistently when timers are running.

For what it's worth, I think idle-timer-based auto-saving is overkill in
Vim's context. The simple autocommand approach covers the important cases, and
anything more aggressive starts fighting against Vim's grain.

I've never been fond of the idle-timer approach to begin with, and that's
part of the reason why I created `super-save` for Emacs. I like the predictability
of triggering save by doing some action.

## Or Just... Save Manually

> Simplicity is the ultimate sophistication.
>
> -- Leonardo da Vinci

Here's the thing I've come to appreciate about Vim: saving manually isn't
nearly as painful as it is in Emacs. In Emacs, `C-x C-s` is a two-chord
sequence that you type thousands of times a day -- annoying enough that
auto-save felt like a necessity. In Vim, you're already in Normal mode most of
the time, so a quick mapping like:

```vim
nnoremap <leader>w :update<CR>
```

gives you a fast, single-keystroke save (assuming your leader is Space, which
it should be). It's explicit, predictable, and takes almost no effort.

As always, I've learned quite a bit about Vim by looking into this simple topic.
That's probably the main reason I still bother to write such tutorial articles -- 
they make me reinforce the knowledge I've just obtained and make ponder more than
usual about the trade-offs between different ways to approach certain problems.

I still use the autocommand approach myself -- old habits die hard -- but I
have to admit that `<Space>w` gets the job done just fine. Sometimes the
simplest solution really is the best one.

That's all I have for you today. Keep hacking!
