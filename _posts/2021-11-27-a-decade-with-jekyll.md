---
title: A Decade with Jekyll
date: 2021-11-27 10:54 +0200
tags:
- Blogging
- Jekyll
---

2021 is the year of anniversaries for me:

- 10 years since the [creation of my first OSS project Projectile](https://metaredux.com/posts/2021/10/28/projectile-turns-10.html)
- 10 years since the creation of my second OSS project [Emacs Prelude](https://github.com/bbatsov/prelude)
- 10 years of using [Zenburn]({% post_url 2011-05-11-zenburn-emacs %}) as my Emacs color theme.
- 10 years since I've [stopped using Linux]({% post_url 2011-06-11-linux-desktop-experience-killing-linux-on-the-desktop %}) as my primary desktop operating system
- 10 years since I [bought my first Kindle]({% post_url 2011-04-26-thoughts-on-the-kindle %})
- 10 years since I've started [blogging like a hacker]({% post_url 2011-04-23-moving-to-jekyll %}) with Jekyll

Probably I'm forgetting some other important anniversaries, especially those that were not related to technology. 2011 was a really big year for me!

Today I'd like to look back on my experience with Jekyll, the static site
generator (SSG) that I'm using to publish this site and my other blogs [Meta
Redux](https://metaredux.com) and [Emacs Redux](https://emacsredux.com).  Back
in the day Jekyll was a trend-setter - it basically defined the SSG category and
every subsequent tool in it was compared to Jekyll. A lot has happened since
2011:

- Jekyll has a ton of great alternatives today (e.g. Hugo, Ghost, Cryogen, etc)
- For a while [Octopress]({% post_url 2011-11-11-blogging-like-a-hacker-evolution %}) was all the rage in the world of Jekyll (basically Octopress was a highly customized Jekyll setup that many people were using, including me)
- Jekyll's development lost momentum over time. For a while the project was supported by GitHub engineers directly, but I believe this is no longer the case.
- Ruby was all the rage back then, now that's no longer the case. Part of the allure of the alternatives is that they are not based on Ruby and supposedly are easier to
use as you don't have to deal with `gems`, `Gemfile`s, etc.

I definitely had a troubled relationship with Jekyll myself. Early on I was quite annoyed that you had to setup everything in Jekyll manually, even basic things like
an Atom feed or archive/tags pages. I was also frustrated that I had to fiddle with the themes a lot, as HTML and CSS were never my strong suites. If I recall correctly the early versions of Jekyll used some horrible Markdown parser that caused all sorts of problems for me. Still, for all its
shortcomings Jekyll was a huge win for me as it allowed me to do exactly what I wanted to do:

- Write my articles in Emacs using my beloved Markdown format.
- Store my blog under version control and treat it like any other software project.
- Host my blog for free on GitHub Pages.

I guess the first point was the only important point, though. When it comes to writing there's a single tool I want to use everywhere and that's Emacs.
In hindsight I realize that Jekyll and Emacs are quite similar in some ways - they are both building material and not some end user products. Jekyll is
a framework for building sites and this requires you to invest a lot in tweaking the setup to your liking. Emacs is a framework for building the ultimate
editor. We've already established numerous times that's a [life-long endeavor]({% post_url 2021-11-24-emacs-is-a-lifestyle %}).

Over the years I considered replacing Jekyll with something else a few times,
especially when Hugo was all the rage and I was still [stuck with a semi-broken
Octopress setup]({% post_url 2015-02-15-octopress-3-dot-0 %}). I remember
playing with Hugo and thinking that it's not really that different from
Jekyll. Sure, it generated sites faster, but I never cared much about
this. Frankly, I almost never run Jekyll locally - I simply write and push to GitHub when
I feel I've written something that's already in a readable form. Production is
my preview - unlike with software I think for blogs that's fine.  The fact that
Jekyll is written in Ruby is a problem for most people, but for me it's a
feature. After all, I was a Ruby programmer back in 2011 and I'm still involved with Ruby to this day.

Most importantly, however, Jekyll is as simple as it gets. I guess pretty much everyone can setup a Jekyll blog in 5 minutes if they don't need to do customize
some theme. When it comes to writing the thing that truly matters is the content and not its appearance. The more time you spend on customizing your blogging
tool, the less time you have for the actual process of writing.[^1] Jekyll gets the job done for me and I simply don't want to invest a lot of time to learn properly
another SSG (e.g. Hugo) when I can be writing new articles instead. Time is our most precious resource and the older I get the more I value it.

Don't get me wrong, though - Jekyll is not standing still and it's certainly a more
capable tool than it used to be in 2011:

- Jekyll plugins simplified a lot of common tasks (e.g. Atom feeds, pagination, redirects)
- Working with themes is much easier than it used to be. I think distributing themes
as gems (Ruby libraries) was a wonderful idea.
- [kramdown](https://kramdown.gettalong.org/) has been the default Markdown parser for a while now and it's great.

I think those advancements essentially made it pointless to copy someone else's Jekyll setup to jump-start yours. And tools like Octopress were pretty much this - a solid Jekyll setup, that was conveniently packaged for easy reuse. Now that Jekyll is more mature they are just a remnant of the past.

I've had a productive decade with Jekyll, sans the end of the Octopress era, and I think I won't be moving away from Jekyll any time soon.
Yeah, there's always some room for improvement and probably many of the newer tools do the job better in one area or another, but Jekyll covers well my
use-cases and that's what matters the most to me. I recall back in my Linux days I spent countless hours doing distro-hopping, with the misguided notion that
some Linux distro will be massively better than all the others. A classic example of missing the forest for the trees. At this point I'm fairly certain that
no SSG is significantly better than the alternatives and the only practical differences for me are their communities and how much do I have to learn to use one
of them.

And that's a wrap. This happens to be the 132th article I publish here since adopting Jekyll. I've also published exactly 280 articles on "Emacs Redux" and "Meta Redux". Here's to the next 10 years of blogging with Jekyll and Emacs! I hope I'll do better this time around.[^2]

[^1]: I guess that's why I always opted to use relatively simple Jekyll themes. Often I think that `minima` is the greatest theme ever invented.
[^2]: In terms of quality, not quantity.
