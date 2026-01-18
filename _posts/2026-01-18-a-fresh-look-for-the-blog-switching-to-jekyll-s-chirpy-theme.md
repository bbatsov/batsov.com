---
layout: post
title: 'A Fresh Look for the Blog: Switching to Jekyll''s Chirpy Theme'
date: 2026-01-18 16:49 +0200
tags:
- Jekyll
- Chirpy
---

It's been over 4 years since I last updated the look of my blog - in
late 2021 I [switched to the popular at the time "Minimal Mistakes" theme]({% post_url 2021-11-01-switching-to-minimal-mistakes %}).
It served we well, but like so many [Jekyll](https://jekyllrb.com/) themes it got abandoned in recent
years, so I've been shopping recently for a good actively-maintained alternative.

After some research I've decided on [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) as the best option for my
use-case, but I've been putting off the actual switch for quite a while now.
Today it hit me that this was the perfect task for an AI agent like
[Claude Code](https://github.com/anthropics/claude-code), so I've decided to just offload the migration to it.

The whole process was really smooth - I started with the very simple
prompt "Switch my blog's theme to Chirpy" and pointed Claude to Chirpy's
docs. After less than 1 hour and fairly few follow-up requests to address
some small issues (most related to deploying on [GitHub Pages](https://pages.github.com/)), my updated
blog was up and running. As a bonus Claude also:

- Updated inconsistent tags in my articles (e.g. `vim` and `Vim`)
- Updated a reference to the blog's theme in the colophon
- Provided a [GitHub Action](https://github.com/features/actions) for the deployment of the blog that allows me to run Jekyll 4 directly

Good stuff!

Obviously I could have the done the migration manually, as I've always done in the past,
but these days I'm quite bored doing mechanical work that doesn't require much thinking.
This was the main reason why I procrastinated on this subject for so long and I think
that such boring tasks for me are the most appealing use-cases for AI agents like Claude.
If we spent less time on the things that we don't enjoying doing, this obviously
leaves us with more time to spend on the things that we're actually passionate about.

I'll never use AI to write blog posts, but I'm perfectly fine with delegating the maintenance
tasks around my various blogs to tools like Claude. There are many AI uses that I detest
(news, blog posts, videos, etc), but I think in programming there are plenty of good
use-cases for AI. Perhaps this modest article will inspire you to finish some programming-related
task that you've been putting off for a very long time?

That's all I have for you today. I hope you'll enjoy the new look of my blog. Keep hacking!
