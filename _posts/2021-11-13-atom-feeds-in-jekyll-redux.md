---
title: 'Atom Feeds in Jekyll: Redux'
date: 2021-11-13 20:04 +0200
tags:
- Jekyll
- Atom
- Tutorials
---

It's been over a decade since I've started using Jekyll and I'm still
struggling with setting up Atom feeds there. Perhaps this happens mostly,
because I rarely do any feed-related changes, so I tend to forget how they work
exactly. Recently I [switched to using Minimal Mistakes]({% post_url 2021-11-01-switching-to-minimal-mistakes %}) and I had to refresh my knowledge of the subject.

Probably few people remember this, but in the early days of Jekyll you had to
manually generate the Atom feeds you needed. [^1] Back then I'd create a primary
`atom.xml` feed including all articles, and occasionally some category feeds that included only a particular
subset of articles (e.g. some of my readers cared only about the stuff I wrote on Emacs or Ruby). I'd copy the code from `atom.xml`, add a bit of filtering and end up with feeds named `emacs.xml` and `ruby.xml`. Life was simple back then, even if it required a bit of extra work.

Today, however, we have Jekyll plugins and most people use the [jekyll-feed](https://github.com/jekyll/jekyll-feed) plugin, which eliminates the need for all the boilerplate code. Out of the box, the plugin will generate a feed named `feed.xml`. That's fine for most people I guess, but it means I have to (remember to) change the default configuration if I want to preserve my historical `atom.xml` name. That's as simple as adding this snippet to your `_config.yml`:

``` yaml
feed:
  path: atom.xml
```

More interestingly, the plugin can generate additional feeds from your article `Category` and `Tags` metadata. Here's how you can create feeds for a specific category:

``` yaml
feed:
  path: atom.xml
  categories:
    - emacs
```

This will result in the creation of an additional Atom feed named `/feed/emacs.xml`.
Note that for some reason Jekyll doesn't lowercase category names, so if your category is named Emacs the generated feed will become `/feed/Emacs.xml`. I've filed a bug about this behavior, as I wasn't sure if it's the intended one.

You can also generate feeds for all tags like this:

``` yaml
feed:
  tags: true
```

Now you'll end up with a bunch of feeds like `/feed/by_tag/emacs.xml`, `/feed/by_tag/clojure.xml`, etc. Note that as with categories `jekyll-feed` won't lowercase tag names, so "ruby" and "Ruby" will be considered different and will result in the creation of two separate tag feeds.

Generating feeds for all tags is probably an overkill for most people, so you can narrow this down to the tags you care about:

``` yaml
feed:
  tags:
    only:
      - emacs
      - clojure
```

You can also adjust the path for tag feeds, if you don't like the default:

``` yaml
feed:
  tags:
    path: "feed/topics/"
```

You can even shorten it to `feed/` if won't be using category feeds.

`jekyll-feed` has plenty of additional settings, but those are the only ones that I ever needed. Still, as mentioned earlier, I keep forgetting some details about them, which results in silly mistakes from time to time.

After migrating to Minimal Mistakes I managed to mess up the following:

- I forgot to include the `jekyll-feed` settings in my `_config.yml`, as I started with a fresh config for the new theme.
- I couldn't remember how the category feeds were named. As I couldn't find the info in the project's README I had to consult the specs to sort this out. I'll propose some small documentation update to the project maintainers.
- I kept wondering why <https://batsov.com/feed/emacs.xml> results in a 404 error. Eventually I figured out the names were case-sensitive.

At least I didn't forget to update the `atom_feed` setting for Minimal Mistakes. It's good that this is a theme where the feed's path is not hardcoded in the theme's layouts.[^2]

Anyways, I hope that writing this down will help me remember how to do things properly in the future, and I also hope that I'll spare some of you from doing the same mistakes.

That's all I have for you today. Keep writing!

[^1]: See my [old article]({% post_url 2011-04-24-add-atom-feed-to-jekyll %}) on the subject.
[^2]: Some themes simply hardcode `feed.xml` and you have to copy and adjust the layout files if you want to use a different name.
