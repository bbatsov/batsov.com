---
title: AsciiDoc Language Specification
date: 2024-02-22 14:14 +0100
tags:
- AsciiDoc
---

I'm an avid fan of AsciiDoc and I've been using it for years to write
documentation for some of my OSS projects (e.g. CIDER and Projectile). I try to
keep track of developments around AsciiDoc, but it seems I've missed a few
quite notable ones:

1. In 2019 work started towards an official specification for the AsciiDoc
   language.[^1] It's nice to see that this happened with the backing of Stuart
   Rackham, the original author of AsciiDoc, and under the leadership of the co-leads of AsciiDoctor Dan Allen and Sarah White.
2. AsciiDoc is now an Eclipse Foundation project.
3. The original AsciiDoc(.py) site <https://asciidoc.org> has been revamped to serve as the site for the language itself (instead of the original Python implementation)
4. The original [GitHub org](https://github.com/asciidoc) has been abandoned in
favour of the [new Eclipse Foundation project](https://gitlab.eclipse.org/eclipse/asciidoc)

Why are those developments notable? AsciiDoc started life in 2002 as
AsciiDoc.py, but after the original project stagnated, the Ruby implementation
[AsciiDoctor](https://asciidoctor.org) became the most popular processor and
eventually it kind of became the AsciiDoc standard.[^2] For many years in my
mind AsciiDoc and AsciiDoctor were essentially the same thing. Still,
AsciiDoctor was never "The Standard", at least not officially. As noted in the specification proposal:

> The specification for the AsciiDoc language will include an open source
> specification document, which defines required and optional API definitions,
> semantic behaviors, data formats, and protocols, as well as an open source
> Technology Compatibility Kit (TCK) that developers can use to develop and test
> compatible implementations. (Those of you who know Dan and I from before
> Asciidoctor know that an open source TCK is a hard requirement for us). A
> compatible implementation, as defined by the EFSP, must fully implement all
> non-optional elements of a specification version, must fulfill all the
> requirements of the corresponding TCK, and must not alter the specified API.
>
> For users and developers alike, the AsciiDoc specification will mean a clear,
> working definition of what AsciiDoc is and how it should be
> interpreted. Developers will be able to build implementations, tools, and
> services around AsciiDoc without risk of diluting its meaning or splintering
> it. In turn, users will have more options, greater document portability, and
> the assurance that compatible implementations and tools will handle their
> AsciiDoc documents according to a versioned specification.

That's pretty important, and it's something that Markdown has not achieved yet.
The lack of a common Markdown language standard has resulted in the creation
of many slightly incompatible implementations (e.g. GitHub-flavoured Markdown)
and some semi-successful standardization efforts like CommonMark.

You can find the AsciiDoc language specification
[here](https://gitlab.eclipse.org/eclipse/asciidoc-lang/asciidoc-lang). One
interesting thing that I noticed is that final spec will remove some features that
AsciiDoctor had:

* Setext-style headings (section titles and discrete headings)
* Trailing markers on Atx-style headings
* Roman numeral list markers (can still be enabled in output using list style)
* Markdown compatibility syntax with the exception of email-style quote blocks and thematic break variants
* ;; as dlist marker (infinite depth of colons is permitted instead)
* Half open callout list marker (e.g., `1>`)
* $$ for inline passtrhough (will likely be repurposed as an inline stem shorthand instead)
* boxed attrlist on inline passthrough spans
* indexterm2 inline macro (functionality will be folded into indexterm macro)
* dsv table format
* target on endif preprocessor directive
* float style on discrete headings (renamed to discrete, possibly with support for heading as alternative)

Probably the Markdown compatibility syntax is the most impactful item on the list. You
can find more information about the removals
[here](https://gitlab.eclipse.org/eclipse/asciidoc-lang/asciidoc-lang/-/blob/main/spec/outline.adoc?ref_type=heads#user-content-removed-syntax).

From what I've gathered the language spec is still a work in progress, but it seems to be shaping up nicely. I'm really glad
I finally caught wind of it, so I can also align Emacs's [adoc-mode](https://github.com/bbatsov/adoc-mode) with it.

That's all I have for you today. Keep hacking!

[^1]: See <https://asciidoctor.org/news/2019/01/07/asciidoc-spec-proposal/>
[^2]: See <https://docs.asciidoctor.org/about/history/>
