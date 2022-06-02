---
title: Commercial Emacs
date: 2022-06-02 11:02 +0300
tags:
- Emacs
---

> Talking never moves anything in Emacs, never did, never will.
>
> 2021 Maintainer of GNU Emacs, who then proceeded to keep talking.

My previous article [Who Needs Modern Emacs?]({% post_url 2022-06-01-who-needs-modern-emacs %}) generated a lot of feedback, that I'll definitely address in a follow-up article. One comment caught my eye, though - a mention of a recent fork of Emacs that's amusing named [Commercial Emacs](https://github.com/commercial-emacs/commercial-emacs).

While the project doesn't have a manifesto, it feels to me that it's mission is pretty clear - try to address some long-standing issues in GNU Emacs, in the way Lucid Emacs/XEmacs did this in the past.
I haven't played with Commercial Emacs yet, but it seems it has already incorporated some improvements that feel major to me:

- Performant long lines.
- [Tree-sitter font highlighting.](https://github.com/commercial-emacs/commercial-emacs#tree-sitter)
- Gnus is rewritten to be non-blocking.
- Process management is rewritten.
- Tree-sitter replacement of ersatz PPSS syntactic parser.
- [Moving garbage collector rudiments.](https://github.com/commercial-emacs/commercial-emacs#moving-collector)

While I couldn't care less about Gnus, everything else seems very interesting to me. Yesterday someone asked me what would I like to see improved in Emacs and the performance issues with long lines and the lack of proper parser for font-locking purposes[^1] were definitely at the top of my mind. Improvements to the garbage collector would be welcomed by me as well (and any Elisp-level performance improvements in general).

I'll write more on Commercial Emacs once I get to play with it. Right now I just wanted to get the word out - perhaps more people would be interested in helping out with the maintenance of this promising project. And perhaps down the road some of those improvements will even make their way into GNU Emacs itself! Fingers crossed!

**P.S.** I totally loved the project's README. It's quite concise and very witty!

[^1]: Most Emacs major modes implement font-locking in terms of regular expressions. That's both slow and brittle.
