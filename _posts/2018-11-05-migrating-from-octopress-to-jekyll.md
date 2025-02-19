---
layout: single
title: Migrating from Octopress to Jekyll
date: 2018-11-05 22:46 +0100
tags:
- Jekyll
- Octopress
- Tutorials
---

After dreading the migration of this site from Octopress 2 to Jekyll
for years, I finally found the will to do it today. The process was
actually very straight-forward and took me just a couple of hours
(most of which I spent trying to find new a theme for the site and
tweaking it afterwards).

<!--more-->

## Create a new Jekyll Blog

I think it's best to just create a blank Jekyll blog and move there
everything you need from your old blog.

```console
$ jekyll new jekyll-blog
```

## Copy posts and assets from the Octopress blog

```console
$ cd octopress-blog
$ cp -r source/_posts path-to-jekyll-blog
```

You should also copy whatever assets (e.g. images) you had in your
old blog to the new one. I like to keep the images under `assets/images`.

## Remove Octopress Plugins

Octopress shipped many [plugins](http://octopress.org/docs/plugins/) that are not
available with Jekyll.

I was using in my posts a lot <http://octopress.org/docs/plugins/blockquote/> and
<http://octopress.org/docs/plugins/image-tag/>, but replacing those was a trivial
mechanical process.

## Preserve the old links

There was a small difference in the permalinks format of my old
Octopress blog and the new Jekyll blog. The old links looked like
`http://batsov.com/articles/2018/11/05/back-in-black/` and the new
links were like
`http://batsov.com/articles/2018/11/05/back-in-black.html`.

To preserve the old links I had to add this to `_config.yml`:

``` yaml
permalink: ":categories/:year/:month/:day/:title/"
```

## Update posts's front-matter

Seems through the years something has changed in the semantics of
categories.  Back in the day I think this was just a synonym for tags,
but now categories are made part of the URLs (at least by
default). For some reason with Octopress I had used only categories in
my posts's front matter, so I had to rename those keys to tags.  I
also had to add `categories: articles` to all my posts to preserve their
old URL. Basically this:

``` yaml
---
layout: post
title: Migrating from Octopress to Jekyll
categories:
- Jekyll
- Octopress
- Tutorial
---
```

became this:

``` yaml
---
layout: post
title: Migrating from Octopress to Jekyll
categories: articles
tags:
- Jekyll
- Octopress
- Tutorial
---
```

Not sure how good my approach was, but it got the job done.

As an alternative `articles` can simply be made part of the default `permalink`:

``` yaml
permalink: "articles/:year/:month/:day/:title/"
```

## Add jekyll-compose

Octopress had some nice rake tasks like `rake new_post` that I was
found of. I noticed that out of the box Jekyll didn't have anything
like this, which was a bit frustrating. I quickly discovered the plugin [jekyll-compose](https://github.com/jekyll/jekyll-compose)
and it's even better than the old rake tasks. Here's how you can create a new post with it:

```console
$ bundle exec jekyll post "New Post"
```

You can also use the plugin to manage your drafts, which I find extremely handy.

## Party Time

That's all there is to the migration! Now enjoy your wonderful new
blog and write some amazing articles for it! I'm looking forward to
reading them!
