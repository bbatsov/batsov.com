---
layout: post
title: Basic Git Setup
date: 2020-11-22 11:32 +0200
tags:
- Git
---

Every time I change my computer or my operating system[^1], one of the first
things I have to do is to configure Git. This article simply covers the
basic Git settings that I always adjust.

So, here's what I'd typically do:

``` shellsession
# user identity
$ git config --global user.name "Bozhidar Batsov"
$ git config --global user.email bozhidar@example.com
# editor to use by default for things like commit messages
$ git config --global core.editor emacs
# auto-rebase when pulling
$ git config --global pull.rebase true
# the name of the primary branch (formerly known as master)
# that's a pretty recent setting, but it's useful for new projects
$ git config --global init.defaultBranch main
```

Those global user settings are simply stored under `~/.gitconfig`, so
you can easily review and update them there as well. You check your
current configuration running this command:

``` shellsession
$ git config --list
```

Note that this effective configuration would be a combination of OS-wide config (e.g. `/etc/gitconfig`), your
user-wide config (e.g. `~/.gitconfig`) and the config of the Git repo that you're currently in (e.g. `repo/.git/config).

When working on company projects, I would change for each company repository my email to
whatever my work email is:

``` shellsession
$ cd company-project
$ git config user.email bozhidar@company.com
```

And that's it. Turns out my basic Git setup is pretty basic. Check out [this section of the official docs](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
for an expanded coverage of the topic.

That's all I have for you today. I'd appreciate it if you shared in the comments some snippets of Git configuration that
you consider essential.

[^1]: This has been happening quite often recently and I'll cover it in a separate article or two.
