---
title: Display Git Configuration
date: 2021-11-14 11:58 +0200
tags:
- Git
- Tips
---

From time to time I tend to forget what's my effective Git configuration, so I have to
check it somehow. Most of the time I'd simply do the following:[^1]

```console
$ git config --list
user.name=Bozhidar Batsov
user.email=bozhidar@example.org
pull.rebase=true
credential.username=bbatsov
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
remote.origin.url=git@github.com:bbatsov/batsov.com.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
```

This works great, but there's one small problem with the output - it's hard to figure out if something is a global setting
or a repo-specific setting. If only there was a way to show where each config value is coming from... Enter `--show-origin`:

```console
$ git config --list --show-origin
file:/home/bozhidar/.gitconfig  user.name=Bozhidar Batsov
file:/home/bozhidar/.gitconfig  user.email=bozhidar@example.org
file:/home/bozhidar/.gitconfig  pull.rebase=true
file:/home/bozhidar/.gitconfig  credential.username=bbatsov
file:.git/config        core.repositoryformatversion=0
file:.git/config        core.filemode=true
file:.git/config        core.bare=false
file:.git/config        core.logallrefupdates=true
file:.git/config        remote.origin.url=git@github.com:bbatsov/batsov.com.git
file:.git/config        remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
file:.git/config        branch.master.remote=origin
file:.git/config        branch.master.merge=refs/heads/master
```

Now, that's more like it! But wait, there's more!

Since Git 2.26.0, you can use the `--show-scope` option to achieve a similar result:

```console
$ git config --list --show-scope

system  rebase.autosquash=true
system  credential.helper=helper-selector
global  core.editor=emacs
local   core.symlinks=false
local   core.ignorecase=true
```

It can be combined with `--show-origin` or the following flags:

* `--local` for repository config
* `--global` for user config
* `--system` for all users' config

Pretty much everything related to `git config` can be combined with those flags.
Here's how to list only the settings defined within the current repository:

```console
$ git config --list --show-origin --local
file:.git/config        core.repositoryformatversion=0
file:.git/config        core.filemode=true
file:.git/config        core.bare=false
file:.git/config        core.logallrefupdates=true
file:.git/config        remote.origin.url=git@github.com:bbatsov/batsov.com.git
file:.git/config        remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
file:.git/config        branch.master.remote=origin
file:.git/config        branch.master.merge=refs/heads/master
```

Of course, you can also consult specific settings if you remember their names:

```console
$ git config user.email
bozhidar@example.org
$ git config --show-origin user.email
file:.git/config        bozhidar@example.org
```

I rarely do this, as I'm struggling to remember even some of the basic config options, but your memory might be better than mine.

That's all I've got for you today. I hope you learned something useful! I also hope I'll finally remember `--show-scope`!

[^1]: Admittedly, first I'd often try `git config` and then start to wonder what was the proper command.
