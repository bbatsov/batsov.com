---
title: 'How to Vim: Format Lines'
date: 2025-05-23 23:00 +0300
tags:
- vim
---

When writing long code comments or prose (e.g. in Markdown) I like to
have lines neatly formatted to fit the `textwidth` setting.[^1]
There are two common operators we can use in Vim to achieve this -
`gq` and `gw`. Most of the time you'd use:

- `gqq` or `gww` to format the current line (there are also the longer versions `gqgq` and `gwgw`, which do the same, but are harder to type)
- `gqip` or `gwip` to format the current paragraph

You might be wondering what's the difference between `gq` and `gw` - I certainly
wondered about this when I first saw them. Basically `gq` moves the cursor to the
end of the formatted region of text and `gw` doesn't. On top of this `gq` also
respects the settings `formatprg` and `formatexpr` and `gw` doesn't.[^2]

I find myself using commands like `gww` and `gwip` a lot more often than their
`gq` counterparts.

From what I gathered in older versions of Vim, there was also a `Q` operator that was
a synonym for `gq`. People who got used to it tend to add it to their `.vimrc`s.

That's all I have for you today. As usual it's not a bad idea to do `:h gq` and `:h gw`
to learn more on the subject. 

[^1]: I normally aim for line-length limit of either 80 or 100 characters, depending on the context.
[^2]: Both of them are blank by default.
