---
layout: post
title: 'Ubuntu Tip: Removing All Packages Installed from a PPA'
date: 2020-09-15 16:14 +0300
tags:
- Ubuntu
- Linux
- Tips
---

PPAs (Personal Package Archives) are a popular way to install pre-built packages that
for some reason are not (yet) available in Ubuntu's standard package repositories.
Some common examples that come to my mind:

- installing the newest release of Emacs
- installing the newest GPU drivers

Eventually, if you're lucky[^1] you might not need the packages from the PPA
anymore, so this brings us to the question of today's article - how can you
quickly remove everything you installed from a PPA?  If you installed only a
single package that's not a big deal, but you might have installed dozens of
packages from one PPA. A good example would be
[oibaf/graphics-drivers](https://launchpad.net/~oibaf/+archive/ubuntu/graphics-drivers).
Once you no longer need the newest drivers (e.g. after a distro upgrade) you can
remove all of them like this:

``` shellsession
$ sudo apt install ppa-purge
$ sudo ppa-purge ppa:oibaf/graphics-drivers
```

This will remove all packages installed from the PPA. Depending on whether the
packages exist in other enabled repos or not that might result in downgrade or
complete removal of those packages. In the specific example I've given we'd just
downgrade all video driver packages to their versions available in Ubuntu's own
repos.

That's all I have for you today. Keep hacking!

[^1]: Or unlucky - imagine some snapshot versions crashing constantly.
