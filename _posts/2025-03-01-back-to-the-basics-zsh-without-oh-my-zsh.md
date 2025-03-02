---
title: 'Back to the Basics: Zsh without Oh My Zsh'
date: 2025-03-01 18:20 +0200
tags:
- Zsh
---

> `.zshrc` simplicity is the ultimate sophistication.
>
> -- Leonardo Da Zshinci

I've been using the Z Shell (a.k.a. Zsh) for a [very long time now]({% post_url
2008-07-27-zsh-prompt %}).  There was a time early on in my Zsh journey when I
read the entire documentation, plus a book on Zsh and I had a simple, yet pretty
decent custom configuration. At some point, however, I bought into the hype
of [Oh My Zsh](https://ohmyz.sh/) and I've been mostly using it ever since.

Oh My Zsh (OMZ) is a nice setup if you're on the lazy side when it comes to
customizing your shell, but I recently realized I'm barely using anything from
it. Fundamentally OMZ is mostly a collection of:

- Zsh prompt themes
- Aliases
- Some utility plugins (e.g. the `bundler` plugin allows you to run Ruby commands without
typing `bundle exec`)
- Some sane Zsh defaults

I got drawn to OMZ mostly because of the fancy prompt themes that had things
like VCS status, etc.  That was a long time ago, though, and the competition has
caught up.  These days there are a ton of portable prompt themes that one can
install on any shell.  I don't use almost any of the aliases coming with the
various OMZ plugins, so those are not really a big features for me.  Finally, I
used to modify only a handful of Zsh defaults in the past - mostly things to do
with history management and `autocd`. So when I asked myself recently why am I
using OMZ exactly I couldn't provide a meaningful answer. Just a classic case
of inertia. Oh, well...

It became clear to me I don't really need OMZ and I quickly replaced it with a
tiny custom `.zshrc`.  Before I show you its contents, I'll mention that I'm
using [Starship](https://starship.rs/) as my prompt, `zoxide` to move
efficiently between directories, and `fzf` to navigate the shell history, so if
you want to emulate my setup completely you'll have to install them first. On
macOS that's as easy as:

``` shell
brew install starship
brew install fzf
brew install zoxide
```

And here's my `.zshrc`:

``` shell
# Enable persistent history
HISTFILE=~/.zsh_history
HISTSIZE=100000
SAVEHIST=100000

setopt HIST_SAVE_NO_DUPS
setopt INC_APPEND_HISTORY

# Configure the push directory stack (most people don't need this)
setopt AUTO_PUSHD
setopt PUSHD_IGNORE_DUPS
setopt PUSHD_SILENT

# Emacs keybindings
bindkey -e
# Use the up and down keys to navigate the history
bindkey "\e[A" history-beginning-search-backward
bindkey "\e[B" history-beginning-search-forward

# Move to directories without cd
setopt autocd

# Initialize completion
autoload -U compinit; compinit

# The most important aliases ever (the only thing I borrowed from OMZ)
alias l='ls -lah'
alias la='ls -lAh'
alias ll='ls -lh'
alias ls='ls -G'
alias lsa='ls -lah'

# Set up fzf key bindings and fuzzy completion
source <(fzf --zsh)

# Set up zoxide to move between folders efficiently
eval "$(zoxide init zsh)"

# Set up the Starship prompt
eval "$(starship init zsh)"
```

I told you it was small and simple and I hope you'll agree I didn't lie.
Now I know everything in my Zsh setup and I frankly don't missing anything from OMZ.
Not to mention that Starship is a much better and faster theme than anything bundled in OMZ.

By the way, if you really miss some particular plugins from OMZ, you don't
really the whole OMZ to be able to use it. [Antidote](https://antidote.sh/) is a
Zsh plugin manager that allows to install OMZ plugins, if you really need
them. I don't care much about things like syntax highlighting (the most common
use case of a Zsh plugin IMO) or pressing `ESC` twice to prefix a command with
`sudo` (people should really learning what `C-a` does in the shell), so as you can
imagine it's unlikely I'm be installing any plugins soon. Still, it's good to know
you have options. The vast Zsh plugin ecosystem is probably it's main advantage over
its arch-nemesis Bash.

So, what are you waiting for? Are you going to go back to the basics as well or you're going to
stick with OMZ?

That's all I have for you today. Keep hacking!

**P.S.** Truth be told I probably don't really need to be using Zsh at all, given that I'm not
using many of its unique features. These days Bash has closed the gap with Zsh when it comes to
core functionality (e.g. Bash has its own equivalent of `autocd` and programmable completion, although
not as fancy as Zsh's) and it will probably be enough for me if I wanted to really go back to the basics.

On the other end of the spectrum - knowing I'm not particularly tied to Zsh means I can easily switch
to some fancier shell like Fish or Nushell. Decisions, decisions...
