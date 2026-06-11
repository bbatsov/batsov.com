---
title: Emacs loves AsciiDoc
date: 2026-06-11 09:00 +0300
tags:
- Emacs
- AsciiDoc
---

Regular readers know I have a soft spot for
[AsciiDoc](https://asciidoc.org/) -- I've written about it more than once, and
it's the markup behind the documentation of most of my bigger OSS projects
(CIDER, nREPL, Projectile, RuboCop). It hits a sweet spot Markdown never quite
reaches: rich enough for proper technical writing (admonitions, includes, real
tables, cross-references), without dragging in the full ceremony of something
like DocBook.

There was just one problem, and it nagged at me for years: editing AsciiDoc in
Emacs was never much fun.

## Background

For years the only real option was `adoc-mode`, and it was showing its age. The
font-locking was uneven, it predated modern Asciidoctor syntax, and it lagged
far behind what `markdown-mode` (and `org-mode`) offered. So I'd catch myself
reaching for Markdown in situations where AsciiDoc was clearly the better
tool -- not because Markdown was better, but because `markdown-mode` was. That
always bugged me.

Eventually the itch got bad enough that I did something about it. Back in 2023 I
[took over maintenance of adoc-mode](https://metaredux.com/posts/2023/03/12/adoc-mode-reborn.html),
which had been dormant for years, and set myself a clear goal: get it to feature
parity with `markdown-mode`. More recently I also started a brand-new
[asciidoc-mode](https://github.com/bbatsov/asciidoc-mode) built on top of
tree-sitter. The target for both is the same -- AsciiDoc support in Emacs that's
on par with Markdown, and ideally not far off from Org.

## Less is more, unless it's modes

Why two modes? They scratch slightly different itches:

- `adoc-mode` is the classic, regexp-based mode. It works on any reasonably
  modern Emacs, has no external dependencies, and now ships a pile of
  interactive features (markup commands, list editing, image preview, tempo
  templates).
- `asciidoc-mode` is the newer, tree-sitter-based mode. It leans on a real
  grammar for robust, accurate highlighting and good performance in large
  documents. It's leaner by design, and it needs Emacs 30.1+ with tree-sitter.[^1]

If you want the kitchen sink on any Emacs, reach for `adoc-mode`. If you're on a
recent Emacs and want highlighting backed by an actual parser, try
`asciidoc-mode`. Pick whichever fits your setup -- both are actively maintained.

## Recent developments

I've spent a good chunk of the past month bringing both modes to a place I'm
genuinely happy with, and they're now mostly feature-complete as far as I'm
concerned. Some highlights:

- **Modern AsciiDoc, not legacy AsciiDoc.py.** Both modes now track the modern
  Asciidoctor spec -- something I'd
  [wanted to do for a while]({% post_url 2024-02-22-asciidoc-language-specification %}).
  `+text+` and `++text++` are treated as passthroughs rather
  than monospace, curved-quote syntax (`` "`text`" ``) is recognized, the block
  ID shorthand `[#id]` is supported, checklists are highlighted, and the menus
  and templates dropped a bunch of deprecated AsciiDoc.py leftovers.
- **More uniform font-locking.** I went through the faces in both modes and
  aligned them with `markdown-mode` and `org-mode`: bold renders as plain bold,
  emphasis as plain italic, structural markup (delimiters, list markers) fades
  into the background, and dedicated semantic faces cover metadata, URLs,
  footnotes, highlights, and roles. Switching between Markdown and AsciiDoc no
  longer feels jarring.
- **Navigation that actually works.** Clickable cross-references and links, an
  `xref` backend over anchors, context-aware completion, and -- for `adoc-mode`
  -- cross-file Antora cross-reference resolution.
- **Tooling integration.** Asciidoctor-backed preview/export and a Flymake
  checker that reports parser errors inline.
- **Native code-block fontification.** `[source,LANG]` blocks in `asciidoc-mode`
  are highlighted using the language's own major mode, just like in
  `markdown-mode` and `org-mode`. `adoc-mode` had added support for this a while
  back.

Getting `asciidoc-mode` where I wanted it also meant sending a few fixes
upstream to the
[tree-sitter-asciidoc](https://github.com/cathaysia/tree-sitter-asciidoc)
grammar it builds on. A grammar-backed mode is only as good as its grammar, so
some of the work happened a layer below Emacs. If you're curious about that side
of things, I wrote up the broader experience in
[building Emacs major modes with Tree-sitter]({% post_url 2026-02-27-building-emacs-major-modes-with-treesitter-lessons-learned %}).

## Give it a try

If you've been avoiding AsciiDoc in Emacs because the tooling wasn't there -- I
get it, that was me too. But the situation is genuinely different now. Grab
[adoc-mode](https://github.com/bbatsov/adoc-mode) or
[asciidoc-mode](https://github.com/bbatsov/asciidoc-mode), point it at one of
your `.adoc` files, and see how it feels.

And please share your feedback. Both projects are at the stage where real-world
usage surfaces the rough edges I can't see myself. Bug reports, ideas, and PRs
are all very welcome on the respective issue trackers.

Together we can make AsciiDoc a first-class citizen of the Emacs ecosystem![^2]

That's all from me, folks! Keep hacking!

[^1]: Also, I really wanted to name something `asciidoc-mode`.
[^2]: Afterwards we'll aim for world domination, of course.
