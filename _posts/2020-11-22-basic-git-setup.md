---
layout: single
title: Basic Git Setup
date: 2020-11-22 11:32 +0200
tags:
- Git
- Tutorials
---

Every time I change my computer or my operating system[^1], one of the first
things I have to do is to configure Git. This article simply covers the
basic Git settings that I always adjust.

So, here's what I'd typically do:

``` shell
# user identity
$ git config --global user.name "Bozhidar Batsov"
$ git config --global user.email bozhidar@example.com
# editor to use by default for things like commit messages
$ git config --global core.editor emacs
# auto-rebase when pulling
$ git config --global pull.rebase true
# auto-convert CRLF to LF
# useful if you're working on Windows and there are people on your team who are working on Unix
$ git config --global core.autocrlf true
# the name of the primary branch (formerly known as master)
# that's a pretty recent setting, but it's useful for new projects
$ git config --global init.defaultBranch main
```

I guess for many people it'd also be useful to specify their preferred merge tool:

``` shell
$ git config --global merge.tool some-tool
```

To me, however, that's irrelevant as almost all of the time I'm interacting with Git via
[Magit](https://magit.vc/). If you're looking for an excuse to try out Emacs, you'll be hard
pressed to find a better excuse than Magit.

The global Git user settings are simply stored under `~/.gitconfig`, so
you can easily review and update them there as well. You can check your
current configuration by running this command:

``` shell
$ git config --list
```

Note that this effective configuration would be a combination of OS-wide config (e.g. `/etc/gitconfig`), your
user-wide config (e.g. `~/.gitconfig`) and the config of the Git repo that you're currently in (e.g. `repo/.git/config).

When working on company projects, I would change for each company repository my email to
whatever my work email is:

``` shell
$ cd company-project
$ git config user.email bozhidar@company.com
```

If you're working on multiple company repositories the above solution will quickly become annoying. In such
cases you may want to use [Git conditional includes](https://git-scm.com/docs/git-config#_conditional_includes),
which basically allow you to include a different configuration file in your main Git config, based so on some
rules.[^2] In our case we can have a different configuration for the e-mail based on the repository directory path. Here's an example
`.gitconfig` to illustrate this:

    [includeIf "gitdir:personal/"]
      path = .gitconfig-personal
    [includeIf "gitdir:work/"]
      path = .gitconfig-work

Now, any Git repository under a folder called `personal` (anywhere on your file
system) will use the personal email address, and any repository under a
folder called `work` will use your work email address.
This matches my preference to keep my personal projects under
`~/projects/personal` and the work under `~/projects/work`.

The contents of `.gitconfig-personal` can be something like:

    [user]
      email = bozhidar@home.com

And the contents of `.gitconfig-work` can be something like:

    [user]
      email = bozhidar@work.com

And that's it. Turns out my basic Git setup is pretty basic. Check out [this section of the official docs](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
for an expanded coverage of the topic. You can find way more configuration options [here](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

That's all I have for you today. I'd appreciate it if you shared in the comments some snippets of Git configuration that
you consider essential.

[^1]: This has been happening quite often recently and I'll cover it in a separate article or two.
[^2]: Special thanks to my readers who suggested this setup to me.
