---
title: 'Git Tip: Find the Top Contributors'
date: 2022-12-11 19:38 +0200
tags:
- Git
- Tips
---

From time to time it's useful to know who are main authors of some piece of a
project.  Admittedly most of the time I want to check who are the top
contributors to some Git repository I'd use a web interface for this
(e.g. GitHub).  Probably because I never bothered to remember the magic
incantations to do this with the `git` command-line interface and probably
because statistics often look better when you have a have richer UI toolkit to
render them. That being said, today I was reminded how easy it is to cover the basics
with the command-line. If we want a list of the top 10 contributors (in terms of
commits) we can get it like this:[^1]

```console
$ cd cider
$ git shortlog -s -n | head -10
  3358  Bozhidar Batsov
   436  Artur Malabarba
   308  Tim King
   213  Vitalie Spinu
   139  Michael Griffiths
    81  yuhan0
    60  Tianxiang Xiong
    48  Jeff Valk
    47  Lars Andersen
    43  Hugo Duncan
```

By default the output is based on the commit author, but you can switch to using the committer identity:

```console
$ git shortlog -s -n -c | head -10
  4031  Bozhidar Batsov
   434  Artur Malabarba
   307  Tim King
   129  Michael Griffiths
    94  GitHub
    82  Vitalie Spinu
    48  Jeff Valk
    43  Hugo Duncan
    42  Lars Andersen
    23  Jon Pither
```

The output here is quite different for me, as I've squashed and rebased many commits.

We can also include the emails of the authors, which would result is some fun output for this particular project due to my [love for email addresses]({% post_url 2022-05-27-email-mania %}):

```console
$ git shortlog -s -n -e | head -10
  2210  Bozhidar Batsov <bozhidar@domain1.com>
   712  Bozhidar Batsov <bozhidar@domain2.com>
   418  Artur Malabarba <am@example.com>
   308  Tim King <kingtim@gmail.com>
   269  Bozhidar Batsov <bozhidar@domain3.com>
   213  Vitalie Spinu <vs@example.com>
   159  Bozhidar Batsov <bozhidar@domain4.dev>
   139  Michael Griffiths <mg@example.com>
    81  yuhan0 <y0@example.com>
    47  Jeff Valk <jv@example.com>
```

Seems I really went overboard here, as I've committed code with at least 4 different email addresses!
I'd recommend spending some quality time with `man git-shortlog` if I'd like to know what all the flags mean exactly.

That's all I have for you today. Keep hacking!

[^1]: All examples use [CIDER's repository](https://github.com/clojure-emacs/cider).
