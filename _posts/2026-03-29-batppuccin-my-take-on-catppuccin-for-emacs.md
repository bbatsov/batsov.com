---
layout: post
title: 'Batppuccin: My Take on Catppuccin for Emacs'
date: 2026-03-29 10:00 +0300
tags:
- Emacs
- Themes
---

I promised I'd take a break from building Tree-sitter major modes, and I meant
it. So what better way to relax than to build... color themes? Yeah, I know. My
idea of chilling is weird, but I genuinely enjoy working on random Emacs packages.
Most of the time at least...

## Some Background

For a very long time my go-to Emacs themes were
[Zenburn](https://github.com/bbatsov/zenburn-emacs) and
[Solarized](https://github.com/bbatsov/solarized-emacs) -- both of which I
maintain popular Emacs ports for. Zenburn was actually one of my very first open
source projects (created way back in 2010, when Emacs 24 was brand new). It served
me well for years.

But at some point I got bored. You know the feeling -- you've been staring at the
same color palette for so long that you stop seeing it. My experiments with other
editors (Helix, Zed, VS Code) introduced me to
[Tokyo Night](https://github.com/enkia/tokyo-night-vscode-theme) and
[Catppuccin](https://github.com/catppuccin/catppuccin), and they've been my daily
drivers since then.

Eventually, I ended up creating my own Emacs ports of both. I've already published
[emacs-tokyo-themes](https://github.com/bbatsov/emacs-tokyo-themes),
and I'll write more about that one down the road. Today is all about Catppuccin.
(and by this I totally mean Batppuccin!)

## Why Another Catppuccin Port?

There's already an [official Catppuccin theme for
Emacs](https://github.com/catppuccin/emacs), and it works. So why build another
one? A few reasons.

The official port registers a single `catppuccin` theme and switches between
flavors (Mocha, Macchiato, Frappe, Latte) via a global variable and a reload
function. This is unusual by Emacs standards and breaks the normal `load-theme`
workflow -- theme-switching packages like `circadian.el` need custom glue code to
work with it. It also loads color definitions from an external file in a way that
fails when Emacs hasn't marked the theme as safe yet, which means some users
can't load the theme at all.

Beyond the architecture, there are style guide issues.
`font-lock-variable-name-face` is set to the default text color, making variables
invisible. All `outline-*` levels use the same blue, so org-mode headings are
flat. `org-block` forces green on all unstyled code. Several faces still ship
with `#ff00ff` magenta placeholder colors. And there's no support for popular
packages like vertico, marginalia, transient, flycheck, or cider.

I think some of this comes from the official port trying to match the structure of
the Neovim version, which makes sense for their cross-editor tooling but doesn't
sit well with how Emacs does things.[^1]

## Meet Batppuccin

[Batppuccin](https://github.com/bbatsov/batppuccin-emacs) is my opinionated take
on Catppuccin for Emacs. The name is a play on my last name (Batsov) + Catppuccin.[^2]
I guess you can think of this as `@bbatsov`'s Catppuccin... or perhaps Batman's
Catppuccin?

The key differences from the official port:

**Four proper themes.** `batppuccin-mocha`, `batppuccin-macchiato`,
`batppuccin-frappe`, and `batppuccin-latte` are all separate themes that work
with `load-theme` out of the box. No special reload dance needed.

**Faithful to the style guide.** Mauve for keywords, green for strings, blue for
functions, peach for constants, sky for operators, yellow for types, overlay2 for
comments, rosewater for the cursor. The rainbow heading cycle (red, peach, yellow,
green, sapphire, lavender) makes org-mode and outline headings actually
distinguishable.

**Broad face coverage.** Built-in Emacs faces plus magit, vertico, corfu,
marginalia, embark, orderless, consult, transient, flycheck, cider, company,
doom-modeline, treemacs, web-mode, and more. No placeholder colors.

**Clean architecture.** Shared infrastructure in `batppuccin-themes.el`, thin
wrapper files for each flavor, color override mechanism, configurable heading
scaling. The same pattern I use in
[zenburn-emacs](https://github.com/bbatsov/zenburn-emacs) and
[emacs-tokyo-night-theme](https://github.com/bbatsov/emacs-tokyo-night-theme).

I didn't really re-invent anything here - I just created a theme in a way I'm
comfortable with.

> I'm not going to bother with screenshots here -- it looks like Catppuccin,
> because it *is* Catppuccin. There are small visual differences if you know where
> to look (headings, variables, a few face tweaks), but most people wouldn't
> notice them side by side. If you've seen Catppuccin, you know what to expect.
{: .prompt-info }

## Installation

The easiest way to install it right now:

```emacs-lisp
(use-package batppuccin-mocha-theme
  :vc (:url "https://github.com/bbatsov/batppuccin-emacs" :rev :newest)
  :config
  (load-theme 'batppuccin-mocha t))
```

Replace `mocha` with `macchiato`, `frappe`, or `latte` for the other flavors. You
can also switch interactively with `M-x batppuccin-select`.

## There Can Never Be Enough Theme Ports

I remember when Solarized was the hot new thing and there were something like five
competing Emacs ports of it. People had strong opinions about which one got the
colors right, which one had better org-mode support, which one worked with their
favorite completion framework. And that was fine! Different ports serve different
needs and different tastes.

The same applies here. The official Catppuccin port is perfectly usable for a lot
of people. Batppuccin is for people who want something more idiomatic to
Emacs, with broader face coverage and stricter adherence to the upstream style
guide. Both can coexist happily.

I've said many times that for me the best aspect of Emacs is that you can tweak
it infinitely to make it your own, so as far as I'm concerned having a theme
that you're the only user of is perfectly fine. That being said, I hope
a few of you will appreciate my take on Catppuccin as well.

## Wrapping Up

This is an early release and there's plenty of room for improvement. I'm sure
there are faces I've missed, colors that could be tweaked, and packages that
deserve better support. If you try it out and something looks off, please [open
an issue](https://github.com/bbatsov/batppuccin-emacs/issues) or send a PR.

I'm also curious -- what are your favorite Emacs themes these days? Still rocking
Zenburn? Converted to modus-themes? Something else entirely? I'd love to hear
about it.

That's all from me, folks! Keep hacking!

[^1]: The official port uses Catppuccin's [Whiskers](https://github.com/catppuccin/toolbox/tree/main/whiskers) template tool to generate the Elisp from a `.tera` template, which is cool for keeping ports in sync across editors but means the generated code doesn't follow Emacs conventions.
[^2]: Naming is hard, but it should also be fun! Also -- I'm a huge fan of Batman.
