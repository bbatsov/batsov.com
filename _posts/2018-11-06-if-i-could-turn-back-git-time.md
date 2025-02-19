---
layout: single
title: If I Could Turn Back (Git) Time
date: 2018-11-06 18:54 +0100
tags:
- Git
- Tips
---

If only there was an easy way to find the first commit in a Git repository...
A bit of Googling unveils some pretty hard to remember incantations like:

```console
$ git rev-list --max-parents=0 HEAD
91938b105b3e4ed86ba96602785123ffb1c0d1eb
```

That returns the SHA-1 hash of the first commit in the repo and you can use
`git show` to actually see the commit details like this:

```console
$ git show 91938b105b3e4ed86ba96602785123ffb1c0d1eb
```

Or you can simply combine the commands:

```console
$ git show `git rev-list --max-parents=0 HEAD`
```

This gets the job done, but it's kind of ugly and in practice you need
to map it to some Git alias or a shell alias to be able to use it
effectively. Here's the shell alias approach:

```console
alias git-first="git show `git rev-list --max-parents=0 HEAD`"
```

At this point it's time for the twist it our story.
Turns out there's a much simpler way to get to the beginning of your history:

```console
$ git log --reverse
```

Simple and sweet!
