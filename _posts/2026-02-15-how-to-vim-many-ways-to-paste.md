---
title: 'How to Vim: Many Ways to Paste'
date: 2026-02-15 11:03 +0200
tags:
- Vim
---

Most Vim users know `p` and `P` -- paste after and before the cursor. Simple
enough. But did you know that Vim actually has around a dozen paste commands,
each with subtly different behavior? I certainly didn't when I started
using Vim, and I was surprised when I discovered the full picture.

Let's take a tour of all the ways to paste in Vim, starting with Normal mode
and then moving to Insert mode.

## Normal Mode

One important thing to understand first -- it's all about the **register type**.
Vim registers don't just store text, they also track how that text was
yanked or deleted. There are three register types (see `:help registers`):

- **Characterwise** (e.g., `yw`): `p` inserts text to the right of the cursor, `P` to the left.
- **Linewise** (e.g., `yy`, `dd`): `p` inserts text on a new line below, `P` on a new line above. The cursor position within the line doesn't matter.
- **Blockwise** (e.g., `Ctrl-V` selection): text is inserted as a rectangular block starting at the cursor column.

This is something that trips up many Vim newcomers -- the same `p` command
can behave quite differently depending on the register type!

With that in mind, here's the complete family of paste commands in Normal mode:

| Command | Direction | Cursor Position | Indent | Notes |
|---------|-----------|-----------------|--------|-------|
| `p` | After cursor / below line | On last pasted char | None | The classic paste |
| `P` | Before cursor / above line | On last pasted char | None | The other classic paste |
| `gp` | After cursor / below line | After pasted text | None | Great for chaining pastes |
| `gP` | Before cursor / above line | After pasted text | None | Closest to "normal editor" paste |
| `]p` | After cursor / below line | On last pasted char | Adjusts to current line | My favorite for code |
| `[p` | Before cursor / above line | On last pasted char | Adjusts to current line | Like `]p`, but before |
| `zp` | After cursor / below line | On last pasted char | None | No trailing spaces (blockwise) |
| `zP` | Before cursor / above line | On last pasted char | None | No trailing spaces (blockwise) |

The "Direction" column above reflects both cases -- for characterwise text it's
"after/before the cursor", for linewise text it's "below/above the current line".

How to pick the right paste command? Here are a few things to keep in mind:

- The difference between `p`/`P` and `gp`/`gP` is all about where your cursor
  ends up. With `p` the cursor lands on the last character of the pasted text,
  while with `gp` it moves just past the pasted text. This makes `gp` handy
  when you want to paste something and continue editing right after it.
- `]p` and `[p` are incredibly useful when pasting code -- they automatically
  adjust the indentation of the pasted text to match the current line. No more
  pasting followed by `=` to fix indentation![^1]
- `zp` and `zP` are the most niche -- they only matter when pasting blockwise
  selections, where they avoid adding trailing whitespace to pad shorter lines.

All Normal mode paste commands accept a count (e.g., `3p` pastes three times)
and a register prefix (e.g., `"ap` pastes from register `a`).

## Insert Mode

In Insert mode things get interesting. All paste commands start with `Ctrl-R`,
but the follow-up keystrokes determine how the text gets inserted:

| Command | Chars Interpreted? | Respects `textwidth`? | Auto-indent? | Fixes Indent? |
|---------|--------------------|-----------------------|--------------|---------------|
| `Ctrl-R {reg}` | Yes (as-if-typed) | Yes | Yes | No |
| `Ctrl-R Ctrl-R {reg}` | No (literal) | Yes | Yes | No |
| `Ctrl-R Ctrl-O {reg}` | No (literal) | No | No | No |
| `Ctrl-R Ctrl-P {reg}` | No (literal) | No | No | Yes |

Let me unpack this a bit:

- `Ctrl-R {reg}` is the most common one -- you press `Ctrl-R` and then a register
  name (e.g., `"`, `a`, `+` for the system clipboard). The text is inserted
  as if you typed it, which means `textwidth` and auto-indentation apply. This
  can be surprising if your pasted code gets reformatted unexpectedly.
- `Ctrl-R Ctrl-R {reg}` inserts the text literally -- special characters like
  backspace won't be interpreted. However, `textwidth` and auto-indent still
  apply.
- `Ctrl-R Ctrl-O {reg}` is the "raw paste" -- no interpretation, no formatting,
  no auto-indent. What you see in the register is what you get. This is
  the one I'd recommend for pasting code in Insert mode.
- `Ctrl-R Ctrl-P {reg}` is like `Ctrl-R Ctrl-O`, but it adjusts the indentation
  to match the current context. Think of it as the Insert mode equivalent of `]p`.

**Note:** Plain `Ctrl-R {reg}` can be a minor security concern when pasting from
the system clipboard (`+` or `*` registers), since control characters in the
clipboard will be interpreted. When in doubt, use `Ctrl-R Ctrl-O +` instead.

## Epilogue

And that's a wrap! Admittedly, even I didn't know some of those ways to paste
before doing the research for this article. I've been using Vim quite a bit
in the past year and I'm still amazed how many ways to paste are there!

If you want to learn more, check out `:help p`, `:help gp`, `:help ]p`, and
`:help i_CTRL-R` in Vim's built-in help. There's always more to discover!

That's all I have for you today. Keep hacking!

[^1]: You can also use `]p` and `[p` with a register, e.g. `"]p` to paste from register `"` with adjusted indentation.
