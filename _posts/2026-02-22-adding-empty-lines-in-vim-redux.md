---
title: 'Adding Empty Lines in Vim: Redux'
date: 2026-02-22 18:30 +0200
tags:
- Vim
description: All the ways to add blank lines above or below the cursor in Vim
---

A long time ago I wrote about [adding empty
lines](https://emacsredux.com/blog/2013/03/26/smarter-open-line/) in Emacs on
my other blog, Emacs Redux. Now it's time for the Vim version of this. Adding a
blank line above or below the cursor is one of those tiny operations you do
constantly, and Vim gives you several ways to do it -- each with different
trade-offs.

## Normal Mode

### The Obvious: `o` and `O`

Most Vim users reach for `o` (open line below) or `O` (open line above). These
work, but they drop you into Insert mode. If you just want a blank line and want
to stay in Normal mode, you need `o<Esc>` or `O<Esc>`.

This gets the job done, but has a couple of annoyances:

- The cursor moves to the new blank line.
- Auto-indent may leave trailing whitespace on the "blank" line.
- It pollutes the `.` repeat register (it records an insert operation).

On the bright side, `o`/`O` accept a count -- `5o<Esc>` inserts 5 blank lines below.

### The Clean Way: `:put`

A lesser-known approach that avoids the issues above:

- `:put _` -- adds a blank line **below** the current line
- `:put! _` -- adds a blank line **above** the current line

The `_` here is Vim's black hole register, which is always empty. Since `:put`
inserts register contents linewise, putting "nothing" results in a clean blank
line. No trailing whitespace, no register pollution, and you stay in Normal mode.

> `:put =''` (using the expression register) works the same way, but `:put _` is
> shorter and easier to remember.
{: .prompt-tip }

The downside? It's verbose to type interactively, and the cursor still moves to
the new line.

### The Elegant: `]<Space>` and `[<Space>`

This is the gold standard:

- `]<Space>` -- adds a blank line **below**
- `[<Space>` -- adds a blank line **above**

They also accept a count -- `3]<Space>` adds 3 blank lines below.

> In **Neovim 0.11+**, these are built-in default mappings. In regular Vim,
> you need Tim Pope's [vim-unimpaired](https://github.com/tpope/vim-unimpaired)
> plugin. Note that the two implementations differ slightly -- Neovim uses
> `nvim_buf_set_lines` which preserves the cursor position exactly, while
> vim-unimpaired uses `:put` with mark jumps, which keeps you on the same line
> but resets the column to 0.
{: .prompt-info }

If you're on plain Vim without plugins, you can add equivalent mappings to your
`.vimrc`:

```vim
nnoremap ]<Space> :call append(line('.'), '')<CR>
nnoremap [<Space> :call append(line('.') - 1, '')<CR>
```

The `append()` approach is the cleanest -- no side effects on registers, marks,
or undo granularity.

## Insert Mode

In Insert mode your options are simpler:

- `Enter` -- creates a new line below (or splits the line if the cursor is in
  the middle of text).
- `Ctrl-O o` -- executes `o` as a one-shot Normal mode command, opening a line
  below. You end up in Insert mode on the new line.
- `Ctrl-O O` -- same thing, but opens a line above.

`Ctrl-O` is handy when you want to add a line without breaking your Insert mode
flow.

## Comparison

| Method | Stays in Normal? | Cursor Stays? | Count? | Notes |
|--------|-----------------|---------------|--------|-------|
| `o<Esc>` / `O<Esc>` | Yes | No | Yes | May leave trailing whitespace |
| `:put _` / `:put! _` | Yes | No | No | Clean, no side effects |
| `]<Space>` / `[<Space>` | Yes | Neovim: yes; unimpaired: col 0 | Yes | Neovim 0.11+ or vim-unimpaired |
| `Enter` | N/A (Insert) | No | No | Splits line if cursor is mid-text |
| `Ctrl-O o` / `Ctrl-O O` | N/A (Insert) | No | No | One-shot Normal mode |

My recommendation? Use `]<Space>` and `[<Space>`. Like most Vim users, I relied
on `o<Esc>` and `O<Esc>` for years, but once I discovered vim-unimpaired (and
later the equivalent built-in mappings in Neovim 0.11) I never looked back.

More broadly, I'm a huge fan of the uniform `]`/`[` convention that
vim-unimpaired pioneered -- `]q`/`[q` for quickfix, `]b`/`[b` for buffers,
`]<Space>`/`[<Space>` for blank lines, and so on. It's a consistent, mnemonic
system that's easy to internalize, and I'm glad Neovim has been adopting it as
built-in defaults. If you're on Vim, do yourself a favor and install
[vim-unimpaired](https://github.com/tpope/vim-unimpaired).

That's all I have for you today. Keep hacking!
