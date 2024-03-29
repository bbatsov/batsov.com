---
layout: single
title: "Moving from WordPress to Jekyll"
tags:
- Jekyll
---

This is my first post after a very long hiatus partially induced by a
migration of my blog from WordPress to
[Jekyll](https://github.com/mojombo/jekyll), which I started half an
year ago and never quite finished.[^1]

I wasn't particularly unhappy with WordPress, but some things there
irritated me - for instance it was hard to edit my posts within my
beloved Emacs.[^2] Accidentally I've stumbled upon the post
["Blogging like a hacker"](http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html)
and I instantly knew Jekyll was what I needed. It allows me to create
my posts as simple markdown templates, allows me to embed logic
written in Ruby in them and it has built-in support for code
highlighting (using the excellent Python library *pygments*). It also
allows me to store my blog under version control in GitHub which is
really nice.

Jekyll is not particularly well documented and I had a few problems
with it at first, but I managed to overcome them quickly. One thing that
will be problematic for some people is the need to add things like RSS feed,
commenting and analytics manually (they are bundled in a solution like
WordPress). On the other hand - you're not tied to anything in
particular and may choose the service you like the most. I chose
[Disqus](http://disqus.com) for the comments and Google Analytics for the site
analytics.[^3]

By the way - my old WordPress blog [Devcraft](http://devcraft.wordpress.com) is no more. My new Jekyll blog has a new
domain (batsov.com) and a new theme (based on my favorite coding theme
[Zenburn](https://github.com/bbatsov/zenburn-emacs)).  I know that the
blog is currently pig ugly and the content is not particularly great
either, but I'll try to improve this along the way and add a lot of
interesting and hopefully useful information.

[^1]: Until today, that is.
[^2]: And yes - I know all the options available and I hate them all.
[^3]: What a surprise, right?
