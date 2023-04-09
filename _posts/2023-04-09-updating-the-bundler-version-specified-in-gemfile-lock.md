---
title: Updating the Bundler Version Specified in Gemfile.lock
date: 2023-04-09 19:29 +0300
tags:
- Ruby
- Bundler
---

You might have noticed one change introduced with Bundler 2.3 - it now requires you to run the version of Bundler that's specified in your `Gemfile.lock`.[^1] This means that occasionally you might see something like this:

``` shellsession
$ bundle
Bundler 2.4.6 is running, but your lockfile was generated with 1.17.2. Installing Bundler 1.17.2 and restarting using that version.
Fetching gem metadata from https://rubygems.org/.
Fetching bundler 1.17.2
Installing bundler 1.17.2
```

Bundler does the safe thing automatically, but you can easily update the required Bundler version like this:

``` shellsession
$ bundle update --bundler
```

Pretty meta, right?

I needed to do this out earlier today, when I noticed that Bundler 1.x is not
compatible with Ruby 3. It took me a few minutes to recall the right way to update
the required version of Bundler, so I thought to write this short article for
posterity's sake.

That's all I have for you today. Keep hacking!

[^1]: <https://bundler.io/blog/2022/01/23/bundler-v2-3.html>
