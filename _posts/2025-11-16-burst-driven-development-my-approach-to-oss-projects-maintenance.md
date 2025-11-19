---
title: 'Burst-driven Development: My Approach to OSS Projects Maintenance'
date: 2025-11-16 14:16 +0200
tags:
- OSS
- Maintenance
- Emacs
---

I've been working on OSS projects for almost 15 years now. Things are simple
in the beginning - you've got a single project, no users to worry about and
all the time and the focus in world. Things changed quite a bit for me over
the years and today I'm the maintainer of a [couple of dozen OSS projects](https://batsov.com/projects)
in the realms of Emacs, Clojure and Ruby mostly.

People often ask me how I manage to work on so many projects, besides having
a day job, that obviously takes up most of my time. My recipe is quite simple
and I refer to it as "burst-driven development". Long ago I've realized that
it's totally unsustainable for me to work effectively in parallel on several
quite different projects. That's why I normally keep a closer eye on my bigger
projects (e.g. RuboCop, CIDER, Projectile and nREPL), where I try to respond
quickly to tickets and PRs, while I typically do (focused) development only
on 1-2 projects at a time.

There are often (long) periods when I barely check a project, only to suddenly decide to
revisit it and hack vigorously on it for several days or weeks. I guess that's
not ideal for the end users, as some of them might feel that I "undermaintain"
some (smaller) projects much of the time, but this approach has worked for me very well
for quite a while.

The time I've spent develop OSS projects has taught me that:

- few problems require some immediate action
- you can't always have good ideas for how to improve a project
- sometimes a project is simply mostly done and that's OK
- less is more
- "hammock time" is important

To illustrate all of the above with some example, let me tell you a bit about
[copilot.el 0.3](https://github.com/copilot-emacs/copilot.el/releases/tag/v0.3.0).
I became the primary maintainer of `copilot.el` about 9 months ago.
Initially there were many things about the project that were frustrating to me
that I wanted to fix and improve. After a month of relatively focused work I had
mostly achieved my initial goals and I've put the project on the backburner for
a while, although I kept reviewing PRs and thinking about it in the background.
Today I remembered I hadn't done a release there in quite a while and `copilot.el` 0.3
was born. Tomorrow I might remember about some features in Projectile that
have been in the back of my mind for ages and finally implement them. Or not.
I don't have any planned order in which I revisit my projects - I just go
wherever my inspiration (or current problems related the projects) take me.

Back in the day (before the pandemic) I was also practicing a variant of burst-driven
development that I was calling "conference-driven development". I was speaking
often at conferences back then and I usually do a lot of focused development
on some project leading to a conference where I'd present something about it. E.g.:

- Before Clojure conferences I'd usually focus on improvements in CIDER and nREPL
- Before Ruby conferences I'd focus on RuboCop

I hope you get the idea. Even if my topics were not directly related to my OSS
projects I liked to announce new releases and exciting new developments at
conference talks. Sadly, I've barely spoken at any conferences since 2020, so
conference-driven development doesn't happen these days. Perhaps it will make a
comeback in the future, but I have my doubts about this...

And that's a wrap. Nothing novel here, but I hope some of you will find it useful
to know how do I approach the topic of multi-project maintenance overall.
The "job" of the maintainers is sometimes fun, sometimes tiresome and boring,
and occasionally it's quite frustrating. That's why it's essential to have
a game plan for dealing with it that doesn't take a heavy toll on you
and make you eventually hate the projects that you lovingly developed in
the past.
Keep hacking!
