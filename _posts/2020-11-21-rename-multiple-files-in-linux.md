---
layout: post
title: Rename Multiple Files in Linux
date: 2020-11-21 19:16 +0200
tags:
- Linux
- Utils
- Z Shell
---

From time to time we need to rename a bunch of files according to some
pattern.  One simple example that comes to mind is that recently I
noticed that some articles in my blog had a `.md` extension and some
had a `.markdown` extension. I don't like inconsistencies, so I wanted
them all to have a `.md` extension. Bellow I'll cover several ways to
do this on Linux[^1]. All the examples will assume you're renaming
files in the same folder, but it's typically trivial to extend them to
a directory tree by combining a command with `find -exec` or extended
globbing (e.g. `**/*.markdown`). So, here we go.

One simple option is to use the `rename` utility from the `util-linux` package:

``` shellsession
$ rename markdown md *.markdown
```

Basically you're doing a text substitution in the list of files passed
to the command. The problem with this is that it won't work properly
in cases like `markdown.markdown`. Fortunately, Debian and Debian-derived
distributions (e.g. Ubuntu) ship a more powerful version of this command, that's
written in Perl, and supports regular expressions.

``` shellsession
$ sudo apt install rename
$ rename 's/\.markdown$/.md/' *.markdown
```

The regular expression allows us to be specific about the match and prevents the problem listed above.
By the way, generally it's a good idea to first preview any changes that the command might perform with the `-n` option:

``` shellsession
$ rename -n 's/\.markdown$/.md/' *.markdown
rename(2008-05-05-first-post.markdown, 2008-05-05-first-post.md)
rename(2008-06-16-das-keyboard.markdown, 2008-06-16-das-keyboard.md)
rename(2008-06-19-emacs-rails.markdown, 2008-06-19-emacs-rails.md)
rename(2008-07-27-zsh-prompt.markdown, 2008-07-27-zsh-prompt.md)
rename(2008-09-29-singleton-java-ruby.markdown, 2008-09-29-singleton-java-ruby.md)
rename(2009-05-04-switch-string-idiom-java.markdown, 2009-05-04-switch-string-idiom-java.md)
```

Super handy!

If you don't want to use an external command you can leverage some shell features instead:

``` shellsession
$ for f in *.markdown; do
    mv -- "$f" "${f%.markdown}.md"
done
```

This relies on some relatively advanced substitution features of Bash
and Zsh, that are beyond the scope of today's article, but it gets the
job done. Interestingly enough, Zsh provides a much simpler and way more powerful way to tackle mass rename, via
the `zmv` utility it bundles:

``` shellsession
$ zmv '(*).markdown' '$1.md'
```

While `zmv` doesn't use regular expressions, it's matching and substitution functionality should cover pretty much
everything you decide to throw at it.
Note that `zmv` is usually not enabled by default and you might have to load it manually before using it:

``` shellsession
$ autoload -Uz zmv
```

Notice also that in the first argument of `zmv` you've specifying both the search pattern for files and substitution groups
you can use in the second argument. You can do way more complex renamings with `zmv`:

``` shellsession
# rename dir1/file.txt, dir2/file.txt, etc to file-1.txt, file-2.txt, etc
$ zmv zmv 'dir(*)/file.txt' 'file-${1}.txt'
```

Obviously sky is the limit here, although this applies to the Perl version of the `rename` command as well.
One cool thing about `zmv` is that you just like with `rename` you can preview the changes it's going to do with the `-n` option.

``` shellsession
$ zmv -n '(*).markdown' '$1.md'
```

This will help you to quickly find the right pattern for the mass rename you're trying to perform.

That's all I have for you today. I've barely scratched the surface of
what's possible, but I still hope you learned something new and
useful. Keep hacking!

[^1]: Most of those suggestions should also work on other Unix-like operating systems like macOS, *BSD, etc.
