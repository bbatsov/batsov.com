---
title: 'How to Vim: Build your .vimrc from Scratch'
date: 2026-02-22 20:29 +0200
tags:
- Vim
description: You don't need a massive .vimrc to be productive in Vim. Here's how far you can get with defaults and a handful of settings.
---

People often think that getting started with Vim means spending hours crafting an
elaborate `.vimrc` with dozens of plugins. In reality, modern Vim (9+) and
Neovim ship with remarkably sane defaults, and you can get very far with a
configuration that's just a few lines long -- or even no configuration at all.

## Starting with No .vimrc

If you launch Vim 9 without a `.vimrc` file, it automatically loads
`defaults.vim` -- a built-in configuration that provides a solid foundation.
Here's what you get for free:

- `syntax on` -- syntax highlighting
- `filetype plugin indent on` -- filetype detection, language-specific plugins, and automatic indentation
- `set incsearch` -- incremental search (results appear as you type)
- `set scrolloff=5` -- keeps 5 lines of context around the cursor
- `set display=truncate` -- shows `@@@` instead of hiding truncated lines
- `set mouse=a` -- mouse support in all modes
- `Q` remapped to `gq` (text formatting) instead of the mostly useless Ex mode
- And several other quality-of-life improvements

That's actually a pretty reasonable editing experience out of the box! You can
read the full details with `:h defaults.vim`.

> Neovim goes even further with its defaults -- it enables `autoindent`
> (copies indentation from the previous line), `hlsearch` (highlights all
> search matches), `smarttab` (makes Tab smarter at the start of a line),
> `autoread` (reloads files changed outside the editor), always shows the
> statusline, and sets the command history to 10000 entries, among many other
> things. If you're on Neovim, the out-of-the-box experience is excellent.
> See `:h vim-differences` for the full list.
{: .prompt-info }

## The defaults.vim Gotcha

Here's something that trips up a lot of people: the moment you create a `.vimrc`
file -- **even an empty one** -- Vim stops loading `defaults.vim` entirely. That
means you lose all those nice defaults.

The fix is simple. Start your `.vimrc` with:

```vim
source $VIMRUNTIME/defaults.vim
```

This loads the defaults first, and then your own settings override or extend
them as needed.

> This gotcha only applies to Vim. Neovim's defaults are always active
> regardless of whether you have an `init.vim` or `init.lua`.
{: .prompt-tip }

## A Minimal .vimrc

Here's a minimal `.vimrc` that builds on the defaults and adds a few things most
people want:

```vim
source $VIMRUNTIME/defaults.vim

set number            " show line numbers
set ignorecase        " case-insensitive search...
set smartcase         " ...unless you type a capital
set expandtab         " use spaces instead of tabs
set shiftwidth=4      " indent by 4 spaces
```

That's five settings on top of the defaults. You might not even need all of
them -- `defaults.vim` already handles the fundamentals.

For Neovim, you don't need the `source` line -- all the `defaults.vim`
equivalents are already active. You also get `autoindent`, `hlsearch`, and
`smarttab` for free, so the only settings left to add are the ones that are
genuinely personal preference:

```vim
set number
set ignorecase
set smartcase
set expandtab
set shiftwidth=4
```

## Built-in Filetype Support

One of the most underappreciated aspects of Vim is how much built-in support it
ships for programming languages. When `filetype plugin indent on` is active
(which it is via `defaults.vim` or Neovim's defaults), you automatically get:

- **Syntax highlighting** for hundreds of languages -- Vim ships with around 770+ syntax definitions
- **Language-specific indentation** rules for over 420 file types
- **Filetype plugins** that set sensible options per language (e.g., `shiftwidth`, `commentstring`, `formatoptions`)

This means that when you open a Python file, Vim already knows to use 4-space
indentation. Open a Ruby file and it switches to 2 spaces. Open a Makefile and
it uses tabs. All without a single plugin or line of configuration.

You can check what's available with `:echo globpath(&rtp, 'syntax/*.vim')` for
syntax files or `:echo globpath(&rtp, 'ftplugin/*.vim')` for filetype plugins.
The list is impressively long.

## When to Add More

At some point you'll probably want more than the bare minimum. Here are a few
things worth considering as your next steps:

- **A colorscheme** -- Vim ships with several built-in options (try
  `:colorscheme` followed by Tab to see them). Recent Vim builds even bundle
  [Catppuccin](https://github.com/catppuccin/vim) -- a beautiful pastel theme
  that I'm quite fond of. Another favorite of mine is
  [Tokyo Night](https://github.com/folke/tokyonight.nvim), which you'll need to
  install as a plugin. Neovim's default colorscheme has also been quite good
  since 0.10.
- **Persistent undo** -- `set undofile` lets you undo changes even after closing
  and reopening a file. A game changer.
- **Clipboard integration** -- `set clipboard=unnamedplus` makes yank and paste
  use the system clipboard by default.
- **vim-unimpaired** -- if you're on classic Vim (not Neovim), I think Tim Pope's
  [vim-unimpaired](https://github.com/tpope/vim-unimpaired) is essential. It
  adds a consistent set of `]`/`[` mappings for navigating quickfix lists,
  buffers, adding blank lines, and much more. Neovim 0.11+ has adopted many of
  these as built-in defaults, but on Vim there's no substitute.

And when you eventually want more plugins, you probably won't need many. A fuzzy
finder, maybe a Git integration, and perhaps a completion engine will cover most
needs. But that's a topic for another day.

## Epilogue

The key takeaway is this: **don't overthink your `.vimrc`**. Start with the
defaults, add only what you actually need, and resist the urge to copy someone
else's 500-line configuration. A small, well-understood configuration beats a
large, cargo-culted one every time.

If you want to see just how far you can go without plugins, I highly recommend
the Thoughtbot talk [How to Do 90% of What Plugins Do (With Just
Vim)](https://www.youtube.com/watch?v=XA2WjJbmmoM). It's a great demonstration
of Vim's built-in capabilities for file finding, auto-completion, tag
navigation, and more.

That's all I have for you today. Keep hacking!
