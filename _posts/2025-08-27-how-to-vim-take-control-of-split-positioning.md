---
title: 'How to Vim: Take Control of Split Positioning'
date: 2025-08-27 10:56 +0300
tags:
- Vim
---

One of my pet peeves with Vim is that by default the buffer splitting
behaves a bit weird:

- horizontal splits (`:split`) appear on top
- vertical splits (`:vsplit`) appear on the left

That's exactly the opposite of how things happen by default in Emacs (and a few other editors),
so it really didn't sit well with me. Obviously you can use dedicated commands
to control the split behavior (e.g. `:rightbelow split` and `:botright vsplit`[^1]), but if you want to permanently
"improve" the defaults just add the following your `.vimrc`:

```vim
set splitbelow splitright
```

In case it's not clear:

- `splitbelow` alters the behavior of `:split` (and commands like `:help` that use `:split` internally)
- `splitright` alters the behavior of `:vsplit`

And that's it! Now I find using commands like `:h` a lot more enjoyable, as the help splits
appear exactly where I expect them to.

[^1]: Those commands are pretty crazy in the typical spirit of Vim and you can read more about them [here](https://technotales.wordpress.com/2010/04/29/vim-splits-a-guide-to-doing-exactly-what-you-want/).
