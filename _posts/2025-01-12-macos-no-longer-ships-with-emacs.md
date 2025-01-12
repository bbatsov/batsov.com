---
title: macOS No Longer Ships with Emacs
date: 2025-01-12 11:03 +0200
tags:
- macOS
- Emacs
---

While I was setting up my new mac mini yesterday I noticed something
interesting - Apple have stopped shipping the ancient Emacs 22.1 with macOS! As
I was mostly using Windows + WSL2 in the past 5 years I had missed the exact
moment when this happened, but after some digging I discovered that the change
was first made in macOS 10.15 ("Catalina"), which was released in October 2019.

For me that's a very welcome change, as the ancient version of Emacs that Apple
was shipping in the past was only causing issues for Emacs users. (occasionally
people would accidentally run it, instead of whatever Emacs they installed by
themselves and they'd wonder what went wrong)
Still, I got curious about would things:

1. Why didn't Apple just update Emacs?
2. What prompted them to finally drop the ancient Emacs?

The answer to the first question turned out to be Apple being opposed to using
anything licensed under GPL v3, and so it happens that Emacs 22.1 was the last
Emacs version licensed under GPL v2.[^1] A former Apple employee shared the
following in a comment on HackerNews:

> When I was at Apple, they discontinued bundling the command line version of
> emacs, and they sent a long internal email stating why. The stated reason was
> that any newer emacs would suffer from license incompatibility with macOS, and
> they didn't see the point of supporting a 10 year old version that the FSF
> doesn't even support when, if someone really wants emacs, it's just a `brew
> install emacs` away.

As for the timing - I guess this was just a long time coming.

One curious tidbit - Apple did provide a sort of out-of-the-box replacement for
Emacs.  That's the [mg](https://github.com/troglobit/mg) editor, which is
something like a "Micro Emacs" in a way.  I think it's a great alternative to
the likes of `vi` and `nano` when you need to do simple edits from the
command-line, so you might consider adding something like this to your shell setup:

```shell
export $EDITOR=mg
```

That's all I have for you today. Keep hacking!

[^1]: For the same reason macOS switched from Bash to Zsh in the very same macOS 10.15 ("Catalina"). And similarly to the situation with Emacs - for a very long time Apple were shipping an ancient version of Bash with macOS, the last one that was released under GPL v2. Obviously almost noone was willing to use it.
