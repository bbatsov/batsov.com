---
title: 'How to Vim: Fixing Typos'
date: 2025-08-27 13:38 +0300
tags:
- vim
---

Here's another small Vim tip - how to deal with typos quickly.
Generally, most people do something along those lines:

- enable `:spell` (the built-in spell-checking)
- use `[s` and `]s` to move between the previous/next misspelled word
- correct the typo with `z=` or `1z=`

It's a pretty sound approach overall and there's nothing wrong with it.
Still, as I mostly see the typos I make as I'm typing, I think there are
two other reasonable ways to approach the problem at hand:

1. If you're a fast touch typist you can just `C-w` to delete the preceding word
and retype it from scratch. Using `C-h` for single-letter corrections is fine as well,
of course.
2. Use `C-x C-s` to immediately trigger smart completion for the last misspelled word,
using suggestions from your spelling dictionary. 

I use both of those approaches as type, but most often I lean towards 2), as for longer
words it's a bit more efficient.

By the way, here's my spell-checking configuration:

```vim
autocmd FileType asciidoc,markdown,text setlocal spell spelllang=en_us
```

Sadly, unlike some other editors you can't use Vim to spellcheck only code comments (in Emacs
that's easily achieved with `flyspell-prog-mode`), so I limit the use of `:spell` to
text types I'm working often with (e.g. Markdown).

Funny enough, I had to use the advice I shared in this post several times while writing it.
I hope that not typos made it in the final version!

That's all I have for you today! Keep hacking!
