---
title: 'Updating my toolbox: Ghostty and Fish'
date: 2025-03-14 16:53 +0200
tags:
- Tools
- Ghostty
- Fish
---

Often when I'm setting up new computers I take a bit of time to evaluate my
programming toolbox and make some (usually small) adjustments to it. In January
I got myself a new mac mini M4 and for whatever reasons I got inspired to dive
much deeper than usual. This time around I spent a lot of time playing with
different terminals, editors (e.g. neovim and helix), shells, configuration managers (mostly `chezmoi`), etc.

The two biggest toolbox updates I ended up doing were:

- Switching from iTerm2 to Ghostty
- Switching from Zsh to Fish

I didn't really have any particular issues with iTerm2 and Zsh - I've been using them for ages
and they were both staples in my toolbox. Still, one can get bored even with good tools and I've
always liked to tinker with new tools and look for ways to optimize my workflows.
That's my kryptonite!

## Ghostty

While I was happy with iTerm2 overall, I was somewhat frustrated with iTerm2's
ever growing list of features, that I never really cared much about. I probably
never really knew or needed more than 10% of what it has to offer, so when it
comes to the terminal I was looking for something that's just simpler.

I researched the landscape of alternatives (e.g. WezTerm, Kitty, Alacrity) and Ghostty
caught my eye because of its focus on simplicity and performance. There was some weird
hype around the project, that I didn't quite understand (it's a terminal emulator after all),
but this didn't deter me from trying it out.

I liked the simple setup (it's quite useful with the default settings) and the old-school configuration
via a config file. My Ghostty config is super simple:

``` toml
keybind = global:ctrl+grave_accent=toggle_quick_terminal

command = /opt/homebrew/bin/fish

background-opacity = 0.9

macos-option-as-alt = true

font-family = Fira Code Mono
```

I'm guessing it doesn't really need much explanation - a custom keybinding for the dropdown terminal window
and some really simple settings.

**Note:** I've been using `Control + ~` as my dropdown terminal hotkey for about
20 years and I can't recommend this keybinding highly enough. It's also part of
the reason why I really dislike keyboards that move `~` to a different
location.[^1] On macOS I also use `Command + ~` global keybinding (via Alfred)
to bring to focus my main terminal window.

One other thing I like about the Ghostty is the CLI. I find really handy the following commands:

``` shell
ghostty +list-fonts
ghostty +list-keybinds
ghostty +list-themes
ghostty +list-colors
ghostty +list-actions
ghostty +show-config
```

The `+command-name` admittedly looks a bit weird, but I didn't bother to investigate why they decided to
go down this route.

I've noticed a lot of people are complaining about the lack of a GUI to configure Ghostty, but as noted
above - the config file is more than enough for me. I'm guessing some GUI will be introduced down the road,
but I doubt I'll ever use it.

**Tip:** You can always reload the config file without restarting Ghostty - just do `Shift + Command + ,` (on macOS).

I did encounter a few weird bugs here and there (e.g. the dropdown terminal not
appearing unless I focus one of the regular Ghostty windows), but overall I
didn't have any serious issues with Ghostty and I'm quite happy with it for now.

At some point I also entertained the idea of using Warp, but I don't like much the idea of a terminal that
requires a paid subscription to be fully useful. There are some other less invasive ways to get
good AI tooling in your terminal (e.g. Aider, Claude Code, etc).

## Fish

Recently I wrote about my move away from "Oh My Zsh".[^2] After this was done I
realized I'm using so little of Zsh's functionality that switching to Fish would
be trivial. There's also the big [Fish 4.0](https://fishshell.com/blog/rustport/) release, where Fish is rewritten in Rust!

As you can imagine, if you're reading this, I did make the switch and I'm super
happy with Fish. Lots of small quality of life improvements and very good
out-of-the-box experience. Below is my entire `config.fish`:

``` shell
/opt/homebrew/bin/brew shellenv | source

if status is-interactive
    # zoxide
    zoxide init fish | source

    # Commands to run in interactive sessions can go here
    starship init fish | source

    # rbenv
    rbenv init - fish | source

    # opam
    eval (opam env)
end

abbr -a -- be 'bundle exec'
abbr -a -- gst 'git status'
abbr -a -- lg lazygit
abbr -a --position anywhere --command git -- co checkout
abbr -a --position anywhere --command git -- st status
abbr -a --position anywhere --command hx --command nvim --command emacs -- fish ~/.config/fish/config.fish
```

It's more or less the same as my Zsh setup from the article I've mentioned above, sans the `fzf` integration.
I'm still wondering whether I need it in Fish, as the default history search is not bad at all.

I love the following Fish features:

- Syntax highlighting out-of-the-box
- Completion and history search
- The abbreviations are just awesome!

I still need to go over the complete manual and try out some more advanced features, but I'm really
happy with my ultra basic Fish setup. Fish has been on my radar for at least 10 years, so I definitely
regret a little bit not trying it out earlier.

Better late than never, right?

## Epilogue

Lately I've been drawn by tools that embrace the philosophy "no configuration needed" and provide great
experience out-of-the-box. Ghostty and Fish certainly fit the bill and they delivered small productivity
improvements for me without any efforts on my end. When I was playing with neovim and helix I really realized
how powerful this approach can be.

Sure, I'm the guy who spent 20 years writing Emacs plugins and tinkering with his Emacs config, but I have to
admit that the older I get the more appreciative I become of simpler solutions that just get the job done
and that don't require a life (or two) to truly master.

Expect to read more about my Ghostty and Fish experience going forward. In the meantime - always be improving your own toolboxes! Keep hacking!

[^1]: Recently I got a HHKB and was quickly forced to remap `Esc` there to `~`.
[^2]: See {% post_url 2025-03-01-back-to-the-basics-zsh-without-oh-my-zsh %}
