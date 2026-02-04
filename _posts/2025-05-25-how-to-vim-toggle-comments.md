---
title: 'How to Vim: Toggle Comments'
date: 2025-05-25 10:13 +0300
tags:
- Vim
---

One of the things that had initially frustrated me about Vim is
that out-of-the-box there's no way to toggle comments on and off
in programming languages. This definitely struck me as something odd,
given that almost all of Vim's users are programmers and we have to
deal with comments quite often.

I quickly discovered a couple of things on the subject:

- Historically the way to address this shortcoming was to install the
  [vim-commentary](https://github.com/tpope/vim-commentary) plugin, which
  introduces a `gc` operator that does pretty much what you'd expect.
- Neovim 0.10 (released in 2024) introduced built-in commenting `gc` operator
  (likely modeled after the `vim-commentary` plugin).

As I'm mostly trying to use plain old Vim these days, I've been using
`vim-commentary` myself and it definitely gets the job done. Recently,
however, I realized I didn't actually need it as Vim 9.1 finally added
a built-in plugin that handles comments toggling.[^1] There's a small
caveat, though - it's not enabled by default. To make use of it
you can either:

- Run `:packadd comment` (useful to try it out and get a feel for it)
- Add `packadd comment` to your `.vimrc` (which I think is the way to go)

Now you'll have pretty much the same `gc` operator as the one provided by
Neovim and `vim-commentary`. I'm still a bit puzzled, as why this was made
something optional, but Vim is a weird editor and I guess that's aligned
with its culture of having very conservative defaults.

One other thing that puzzled me is that this didn't get any coverage in
Vim 9.1's release notes.[^2] On the bright side - the plugin is documented
pretty well. (see `:h comment.txt` for more details)

Before we wrap up:

- You can also trigger the plugin commands via `:Comment` and `:Uncomment`
- I find the most useful commenting commands to be `gcc` and combining `gc`
  with visual mode selection.  Neovim has richer text objects, so there it's
  also easy to operate on methods, classes, etc.
- Don't forget to remove whatever comments plugin you're currently using if you
  decide to switch to the built-in functionality

That's all I have for you today. Time to get wild with `gc`!

[^1]: The plugin is named `comment`. Yeah, yeah - naming is hard!
[^2]: <https://www.vim.org/vim-9.1-released.php>
