---
title: Who Needs Modern Emacs?
date: 2022-06-01 08:59 +0300
tags:
- Emacs
---

> The tools we use have a profound (and devious) influence on our thinking habits, and, therefore, on our thinking abilities.
>
> — Edsger W. Dijkstra

Every now and again I come across some discussion on making Emacs
"modern".[^1] The argument always go more or less like this - Emacs
doesn't look and behave like <insert here some trendy editor of the day> and
the world will end if we don't copy something "crucial" from it.

Looking at what people are typically complaining about it seems to make Emacs modern we need to:

- ship by default "cool" themes like Solarized (it seems it's every important to have some dark mode these days)
- show line numbers in the gutter (or its general vicinity) by default
- use the keybindings that are common in most "modern" editors (think `cua-mode`)
- make Emacs more mouse-friendly (e.g. right clicking would open some context menus, there would be more tooltips on mouse hover and so on)
- replace Emacs Lisp with Scheme/Common Lisp/JavaScript
- have a fancy configuration wizard to help with the initial setup of Emacs
- bundle some popular third-party packages by default (e.g. `company-mode`, `lsp-mode`, etc) to fix some perceived deficiency of vanilla Emacs
- rewrite the entire UI layer to make Emacs look more "native" and "sexy"

Given that most of those proposals are somewhat superficial I'm skeptical that
any of them would move the needle for Emacs's adoption. If you ask me - there's
pretty much nothing we can do that would suddenly make Emacs as popular as VS Code.
But you know what - that's perfectly fine. After all there are plenty of "modern" editors that are even less popular than Emacs, so clearly being "modern" doesn't make you popular. And there's also our "arch-nemesis" vim, that's supposedly as "dated" as Emacs, but is extremely popular.

I grow tired of repeating myself, but I'll say this once again:

> Emacs isn't an editor, it's a building material.

A building material for you to create the best editor for _yourselves_. By definition the number of people who want/need to build their own editors is not that big, so I think we'd be on a wild goose chase trying to change this.[^2] Not to mention that Emacs being different from the pack is probably its biggest competitive advantage. Do we need one more editor that's essentially the same as every other editor?

Don't get me wrong - there are always things that we can improve in Emacs. No editor is truly perfect, not even the One True Editor. There are many things I'd like to see improved myself, but none of them is something I can't live without. And none of them would make Emacs fit this mystical "modern" moniker.

So, I'm asking you this simple question - who needs "modern" Emacs?

**Note:** As always I'd recommend sharing your feedback with me [over email](https://batsov.com/contact/#email).

[^1]: E.g. <https://lwn.net/ml/emacs-devel/20200906133719.cu6yaldvenxubcqq@Ergus/> and the response to the mail thread here <https://lwn.net/Articles/832311/>.
[^2]: I wrote more on the topic here <https://batsov.com/articles/2021/11/16/why-emacs-redux/>.
