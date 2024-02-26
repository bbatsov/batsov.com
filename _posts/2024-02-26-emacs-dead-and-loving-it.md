---
title: 'Emacs: Dead and Loving It'
date: 2024-02-26 10:47 +0100
tags:
- Emacs
---

> What is dead cannot die.
>
> -- A Song of Ice and Fire (a.k.a. Game of Thrones)

Emacs was originally created in 1976. I was born in 1984. I've been using Emacs
as my primary editor since 2005. Back then GNU Emacs was still reeling from the
schism with XEmacs[^1] and a lot of people felt that the project might be near
the end of its long and storied history. I recall the Emacs development wasn't
moving particularly fast at the time, modern text editors and IDEs
(e.g. TextMate and Eclipse) were on the rise, and there were quite a few articles
proclaiming the death of Emacs. Yet Emacs is still here 19 years later and some
of the editors and IDEs that were popular in 2005 are not. This year Emacs will turn 48 years,
which is an amazing achievement for any piece of software!

Last week another [Reddit thread on Emacs's
death](https://www.reddit.com/r/emacs/comments/1avn7ox/is_emacs_dying/) created
some ripples in the Emacs community. I didn't even read it, because it's always
more or less the same:

- Emacs is too complicated (different) for people used to "modern" editors
- Emacs is not as popular as X, Y and Z
- Emacs is not as fast as X, Y and Z in some context
- Emacs is old, so it's probably (technologically) outdated
- Lisp sucks, let's just use JavaScript everywhere

Highly subjective personal preferences aside, people often conflate how popular
something is with how good or lively it actually is. If you look at the market
share of Emacs, things definitely don't look great:[^2]

![editor_usage.jpg](/assets/images/editor_usage.jpg)

On the other hand, both the [upstream Emacs development](https://git.savannah.gnu.org/cgit/emacs.git) and [the ecosystem of
third-party packages](https://melpa.org) feel to me more active than ever. At a time
when some people are ready to write off Emacs as dead yet again, I'd be more inclined to
pronounce a "Golden Age of Emacs".[^3]

Why so you might ask. Here are a couple of reasons that immediately come to mind:

- There are around 6,000 Emacs packages available (on the MELPA repository alone)
- Emacs 29 has built-in support for modern technologies like [LSP](https://www.gnu.org/software/emacs/manual/html_node/eglot/index.html) and [TreeSitter](https://www.masteringemacs.org/article/how-to-get-started-tree-sitter)
- Tools like [magit](https://magit.vc/) and [org-mode](https://orgmode.org/) are widely admired even outside Emacs, but haven't been completely emulated by any other editor
- The core Emacs Lisp APIs have been extended and refined a lot in recent years (think `seq.el`, `map.el`, `subr-x.el`, `project.el`, `xref.el`, etc)
- Emacs distros like Spacemacs and Doom Emacs have made it easier for newcomers to experience the full power of Emacs (and have enticed quite a few people from the world of `vim`)
- The popularity of Clojure drove a lot of curious developers to Emacs, as it was the first editor to provide solid support for Clojure
- Emacs 28 brought massive performance gains with [native compilation](https://akrl.sdf.org/gccemacs.html).
- Emacs 29 features a pure GTK front-end (a.k.a `pgtk`). This means that Emacs 29 works natively on Wayland (and Windows 11 + WSL by association).
- Last, but not least - Emacs 29 has [proper support for emojis](https://lars.ingebrigtsen.no/2021/10/28/emacs-emojis-a-%e2%9d%a4%ef%b8%8f-story/)!

Much of this happened in the last 5 years, which is pretty amazing for a dying editors that has been rendered irrelevant but its vastly superior competitors.

It amuses me how many people keep chasing after shiny and new things, even if they don't really need them.
Emacs has covered all of my use-cases for so long that I don't even bother to upgrade to the new releases
right way. I'm writing this article in Emacs 28, as I didn't really have much of an incentive to upgrade to Emacs 29,
despite it packing a lot of improvements. Even if Emacs didn't add any few features for the next decade I'd be
a pretty happy camper.[^4] Perhaps that's a reflection of my appreciation for minimalism, perhaps I've grown wiser with age,
but I'm no longer equating "progress" with "more".

I've written [at length about the merits of Emacs]({% post_url 2021-11-16-why-emacs-redux %}) in the past, so I'm not going to repeat myself here.
I'll just remind you about the old saying "Don't judge a book by its cover". Or by it's popularity or marketing
campaigns for that matter. Essence is what matters, not superficial appearances.

> Whatever doesn't kill you simply makes you stranger.
>
> -- The Joker (The Dark Knight)

Emacs is certainly a very strange editor by modern standards. I'm guessing
that's the main reason why so many people quickly dismiss it. But its remarkable
resilience is an indication that it's probably made of stronger stuff than
most editors. If you want to invest into a tool that has stood the test of time
and will probably continue to be useful 50 years down the road - your options besides
Emacs are quite limited...

It's true that Emacs was at one point one of the two most popular text editors in the world,
but those days are long gone and there's little point in dwelling on them.
Still, today it probably has a lot more users in absolute terms than in the 80s and early 90s.
World domination is not a big priority for Emacs, but it may still be in the cards, given
the average lifespan of many of its competitors.

Emacs is no more dead than usual and it likes it that way. M-x forever!

[^1]: See <https://www.emacswiki.org/emacs/EmacsSchism>
[^2]: See <https://survey.stackoverflow.co/2023/#section-most-popular-technologies-integrated-development-environment>
[^3]: And I'm not the only one who [thinks this way](https://www.reddit.com/r/emacs/comments/ucldkz/are_we_living_in_the_golden_age_of_emacs/).
[^4]: I also need some time to catch up with everything new from the past few years. Emacs is so dead that its users can't catch up with all the progress it makes from the grave!
