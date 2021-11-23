---
title: Small Improvements to the Blog
date: 2021-11-23 10:40 +0200
tags:
- Meta
---

After [switching (think) to Minimal Mistakes]({% post_url 2021-11-01-switching-to-minimal-mistakes %}) I've been
doing some small improvements to the site's structure and content. I thought it might be a good idea to summarize them here:

* I've updated the [About page](/about/). It now includes a lot more information about me and (think)'s background and mission.
* I've moved the contact information from the About page to a [dedicated page](/contact/).
* I've added a simple `lunr`-powered search. I should have done this a while ago, but Minimal Mistakes made this so easy that I finally had no excuse not to do it.
It's not as fancy as Algolia-powered search, but it mostly gets the job done.
* I've replaced the old custom [Archive](/archive/) and [Tags](/tags/) pages, with the versions of those pages provided by Minimal Mistakes. Now they look much better as a result.
* I've added dedicated Atom feeds for the main topics I've historically written about:
  * [Ruby](/feeds/Ruby.xml)
  * [Clojure](/feeds/Clojure.xml)
  * [Emacs](/feeds/Emacs.xml)
  * [Meta](/feeds/Meta.xml) (I use this a replacement for "Misc", so expect all sorts of disconnected articles here)
* I've added a table of contents to many of my longer articles (here's an [example]({% post_url 2011-05-03-ruby-or-python %})
* I've been going over some of my old articles and I've fixed tags, broken links, typos, grammar and markup here and there.

I have to say that this has been a very fun process and Minimal Mistakes has been an absolute joy to use so far!
Next step - finally learn how to use [Jekyll's collections](https://jekyllrb.com/docs/collections/) and add something else besides articles to the site.
