---
title: Switching from Zsh to Fish
date: 2025-05-20 9:55 +0300
tags:
- Zsh
- Z Shell
- Fish
---

Recently I've switched from Z Shell (aka Zsh) to Fish, after being a Zsh
user since 2008. The experience has been pretty good overall and here
I'll share a few tips for everyone looking to make the change.

I'll cover both the general setup and some of the tools I'm using on a
day to day basis (e.g. `rbenv`, `nvm`, etc).

**Note:** You can read more about my Zsh setup [here]({% post_url 2025-03-01-back-to-the-basics-zsh-without-oh-my-zsh %}).

Let's go!

## fisher (plugin manager)

As you'll see below, occasionally you'll need to install
Fish plugins. I recommend the [fisher](https://github.com/jorgebucaran/fisher) plugin manager to help
with that. Installing it is as easy as running:

```shell
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
```

In the following sections we'll see examples of installing plugins with fisher.

## homebrew

**Note:** You can skip this section, if you're not a macOS user.

If you install Fish from Homebrew, it won't work properly without a bit
of additional setup. Basically, you need to add the following to your
`config.fish` (at the very beginning):

```shell
/opt/homebrew/bin/brew shellenv | source
```

Without this bit of code you'll get some weird errors when you run Fish.

## rbenv

I do a lot of programming in Ruby, and I'm using `rbenv` to manage my
Ruby installations. rbenv provides built-in integration with Fish,
so things are easy here. Just add the following snippet to your `config.fish`:

```shell
rbenv init - fish | source
```

## NVM

These days so many tools are written in JavaScript and TypeScript that's
almost impossible to avoid having to install Node.js. NVM (Node Version Manager)
is the most common way to manage multiple Node.js installations.

NVM doesn't play well with Fish, but there's an excellent replacement for it
called [nvm.fish](https://github.com/jorgebucaran/nvm.fish). Just install it
with Fisher like this:

```shell
fisher install jorgebucaran/nvm.fish
```

Now you can easily install and enable various Node.js versions:

```shell
nvm install lts
nvm use lts
```

I think `nvm.fish` doesn't support all the features of NVM, but it certainly
supports the features I care about.

## SDKMAN!

I'm quite fond of Clojure and I'm working on a few OSS Clojure tools
in my spare time. As I need to test stuff on multiple JDK versions
I'm using SDKMAN! to manage them. (it's very similar to rbenv and NVM)

The situation here is quite similar with the one for NVM - you need to
use a version of SDKMAN! designed for Fish:

```shell
fisher install reitzig/sdkman-for-fish@v2.1.0
```

## mise

These days you can replace `rbenv`, NVM and SDKMAN! with [mise](https://github.com/jdx/mise).
I haven't made the switch yet, but I'm glad to report that mise plays well with Fish.
All you need to do is:

```shell
echo '~/.local/bin/mise activate fish | source' >> ~/.config/fish/config.fish
```

## OPAM

OPAM is a package manager and SDK manager for OCaml. Fortunately, it plays
with Fish pretty well out-of-the-box:

```shell
eval (opam env)
```

## Docker

Docker provides completions for the Fish's native completion system, that you'll have
to setup like this:

```shell
mkdir -p ~/.config/fish/completions
docker completion fish > ~/.config/fish/completions/docker.fish
```

## abbreviations

One of the Fish features that I really like are the abbreviations - snippets
of code that can be expanded in various places when typing a shell command.
Below are a few examples from my personal configuration:

```shell
abbr -a -- be 'bundle exec'
abbr -a -- gst 'git status'
abbr -a -- lg lazygit
abbr -a --position anywhere --command git -- co checkout
abbr -a --position anywhere --command git -- st status
abbr -a --position anywhere --command hx --command nvim --command emacs -- fish ~/.config/fish/config.fish
abbr -a -- jpost 'bundle exec jekyll post'
abbr -a -- jserve 'bundle exec jekyll serve'
```

Notice that some abbreviations are "global" and are specific to a particular
command (e.g. `co` and `st` will work only in the context of `git`). Notice also
that the same alias can be triggered in the context of multiple commands -
e.g. my `fish` abbreviation will work with `hx` (Helix), `nvim` and `emacs`.

Now if I type `be` followed by `TAB` it will be expanded to `bundle exec` and
`git co` will be expanded to `git checkout`.  Cool stuff!

## ENV variables

For various reasons working with environment variables in Fish is quite
different from POSIX shells like Bash and Zsh.  I'm not going to go into much
details here and I'll just mention a couple of basics:

- Instead of doing `export $SOME_VAR` you should do `set -gx SOME_VAR=VALUE`
- Lists in Fish (e.g. a list of paths) are space-separated
- Variables can have different scopes (e.g. local, global, universal)
- You can unset (erase) a variable with `set -e`

All of this seemed pretty confusing to me at first, as I'm so used to the POSIX way of doing things,
but after a while it started to feel like a solid improvement over Bash/Zsh and their extended family.

**Tip:** I highly recommend reading [Fish for Bash users](https://fishshell.com/docs/current/fish_for_bash_users.html) at this
point.

### Tweaking the PATH

If you want to tweak `PATH` I'd suggest using `fish_add_path`
(e.g. `fish_add_path $HOME/bin`).  This works both one-off interactively, and in
`config.fish` — it won't produce duplicates in the `config.fish` case.

There are other ways to do this, though. E.g.:

```shell
set -gx PATH "$HOME/bin" $PATH
set -gx PATH /path/to/dir1 /path/to/dir2 $PATH
```

You can also append entries with `set -a`.

### Universal variables

Those get persisted to a file and are available
between restarts. You can create/update universal variables like this:

```shell
set -ux SOME_VAR=value
```

The notion of persistent shell variables was definitely novel and confusing
for me at first, but now I find it pretty cool. In practice it means you can
tweak your shell setup directly from the shell, without having to update the
config and reload it.

**Tip:** You can read about the different types of shell variables in Fish [here](https://fishshell.com/docs/current/cmds/set.html).

## Reloading the configuration

If you want to reload your Fish configuration you can do so like this:

```shell
exec fish
```

That comes in handy after changes to `config.fish` or when you want to reset some
local changes you've made.

## My complete configuration

I'm still using Starship and zoxide with Fish, as I was doing with Zsh.
My entire `config.fish` is listed below and I hope you'll agree it's
pretty simple:

```shell
/opt/homebrew/bin/brew shellenv | source

fish_add_path $HOME/bin

if status is-interactive
    # zoxide
    zoxide init fish | source

    # Commands to run in interactive sessions can go here
    starship init fish | source

    # rbenv
    rbenv init - fish | source

    # nvm.fish
    set --universal nvm_default_version lts
    set --universal nvm_default_packages yarn np

    # opam
    eval (opam env)
end

abbr -a -- be 'bundle exec'
abbr -a -- gst 'git status'
abbr -a -- lg lazygit
abbr -a --position anywhere --command git -- co checkout
abbr -a --position anywhere --command git -- st status
abbr -a --position anywhere --command hx --command nvim --command emacs -- fish ~/.config/fish/config.fish
abbr -a -- jpost 'bundle exec jekyll post'
abbr -a -- jserve 'bundle exec jekyll serve'
```

One tool I use less with Fish is `fzf`, as the default history search is much better than
the one in Zsh.

## Make Fish your default shell

One thing that wasn't immediately clear to me when I switched to Fish was
how to make it my default shell. In the past I'd simply make Zsh my login
shell and be done with it, but that's not a good idea with Fish, as it's not
POSIX compatible and this might cause some issues.

After some deliberation I've decided that it's probably best to just make it
the default shell for my terminal emulator and this has worked pretty well in practice.
I'm using Ghostty and I had to add the following to you config:

```ini
command = /opt/homebrew/bin/fish
```

Alternatively you can just trigger Fish from your `.bashrc` and `.zshrc`, and this
might save you a bit of hassle as that way Fish will inherit the environment from your
existing shell setup.

**Note:** You can find more on the subject [here](https://wiki.archlinux.org/title/Fish#System_integration).

## Additional plugins

I didn't feel the need to install many Fish plugins, but there are a few that quite cool:

- [autopair.fish](https://github.com/jorgebucaran/autopair.fish) - it does what the name implies
- [hydro](https://github.com/jorgebucaran/hydro) - a fast native Fish prompt

Both are created by the author of Fisher Jorge Bucaran.

## Fish resources

There's no shortage of great resources for Fish, but I'd like to highlight a few:

- [Awsm Fish](https://github.com/jorgebucaran/awsm.fish)
- [Fish Cookbook](https://github.com/jorgebucaran/cookbook.fish)

In some areas the cookbook was more helpful to me than the official docs (e.g. it had better explanations
of the types of variables in Fish).

## Why Fish?

You might be wondering what prompted me to move to Fish, so let me address this here real quick:

- I didn't have any particular issues with Zsh, but I did notice that out-of-the-box Fish behaves more
or less exactly as I want my shell to behave. In practice this means less configuration, which is not a
bad thing.
- I like playing with new tools and Fish has been on my radar for a while now.
- Recently Fish 4.0 was released - a full rewrite of the project in Rust, and that caught my attention.

## Epilogue

And that's a wrap. The switch to Fish was extremly fast and easy
and I really regret not doing this earlier. Better late than never!

This post barely scratches the surface of what you can do with Fish, but I hope
it gives you an idea how to approach the process of moving over from another shell.

If you're curious about Fish I cannot recommend you highly enough to
try it out!
