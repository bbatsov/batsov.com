---
title: Inspecting the Contents of a Ruby Gem
date: 2020-12-16 10:17 +0200
tags:
- Tips
- Ruby
---

From time to time you'll need to inspect the contents of a locally
installed Ruby gem. For instance - I needed to check the contents
of my Jekyll theme (`minima`) earlier today, so I could override something
that was hardcoded there.

Each installed gem corresponds to a directory
in your local file system, so all you need to do is find out where
a particular gem resides. There are several ways to do this.
The first option is to use Ruby's `gem` command directly:

```console
$ gem info minima

*** LOCAL GEMS ***

minima (2.5.1)
    Author: Joel Glovier
    Homepage: https://github.com/jekyll/minima
    License: MIT
    Installed at: /home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0

    A beautiful, minimal theme for Jekyll.

$ gem contents minima
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/LICENSE.txt
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/README.md
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_includes/disqus_comments.html
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_includes/footer.html
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_includes/google-analytics.html
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_includes/head.html
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1/_includes/header.html
...
```

The `gem contents` command is extra useful as it effectively combines something like this in a single step:

```console
$ gem info gem-name
$ cd gem-dir
$ ls -l
```

You don't really have to visit the folder in which the installed gem
resides to play with its contents. With `gem unpack` you can dump the
contents of any gem in the current folder:

```console
$ gem unpack minima
Unpacked gem: '/home/bozhidar/minima-2.5.1'
# You can also specify the folder in which to unpack the gem like this
$ gem unpack minima --target ~/unpacked-gems
```

The main benefit of using `gem unpack` is that you can't modify some gem by accident, with the added bonus that you'll
get an easier to remember directory path.

An alternative option is to use `bundler` to procure the gem installation directory information (assuming you're using it):

```console
$ bundle info minima

 * minima (2.5.1)
        Summary: A beautiful, minimal theme for Jekyll.
        Homepage: https://github.com/jekyll/minima
        Path: /home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1

# This is a variant of the previous command that returns only the gem path
$ bundle info --path minima
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1
# Alternatively you can use Bundler's show command
$ bundle show minima
/home/bozhidar/.rbenv/versions/2.7.1/lib/ruby/gems/2.7.0/gems/minima-2.5.1
```

Unfortunately, there's no `bundler` equivalent of the `gem contents` command, but that's not
that big of deal.

Bundler and `gem` actually have one more
extremely useful command that will directly open the gem's folder in
your default editor:

```console
$ gem open minima
# or alternatively
$ bundle open minima
```

Probably that's my favorite way to navigate to an installed gem's contents.

As you can imagine it's pretty straight-forward to change the behavior of a gem - just go to its directory and
edit some of its contents. That's an useful debugging technique, but it opens up one question - how to restore
a gem to its original pristine state? Well, turns out there's a command for this as well:

```console
$ gem pristine gem-name
# or alternatively
$ bundle pristine
```

The `bundler` command will restore all installed gems for a particular bundle to their original state.

That's all I have for you today. I hope you learned something useful! Keep hacking!
