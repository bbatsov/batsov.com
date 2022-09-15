---
title: Reload Zsh Configuration
date: 2022-09-15 10:46 +0300
tags:
- Tips
- Zsh
---

I've been using Zsh on-and-off for a very long time (15+ years), but I still
occasionally learn something new about it.[^1] Yesterday I was setting up
[oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh) on a new computer and I've noticed they had added a
command for reloading the Zsh configuration:

``` shellsession
$ omz reload
```

I assumed this was just an alias for `. .zshrc`, which I had been doing for
ages, but it turns out it's actually an alias for:

``` shellsession
$ exec zsh
```

Instead of reloading a particular configuration file this reloads your Zsh process completely. Why would you want to do this instead of using `source/.`? The main advantage of `exec zsh` is that no rogue state is left after such a reload - e.g. env variables you've previously set that are no longer in your config. By the way, `exec` (like `source`) is just a Zsh built-in. Here is what the documentation has to say about it:

> Replace the current shell with command rather than forking. If command is a shell builtin command or a shell function, the shell executes it, and exits when the command is complete.

Going forward I'll definitely be using this approach, as I love to start my shell with a clean slate (state?). Keep hacking!

[^1]: Not to mention I constantly re-learn a lot of things I knew and forgot.
