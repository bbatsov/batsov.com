---
title: Configure Quick Terminal Size in Ghostty
date: 2025-11-12 10:36 +0200
tags:
- Ghostty
---

I'm a very heavy quick (dropdown) terminal user, so after adopting
Ghostty one of the points of frustration for me was that I could not
specify a default size for my quick terminal. Instead I had to
adjust the size every time I started Ghostty.

Fortunately, the problem recently got solved with the introduction of
the `quick-terminal-size` config option in Ghostty 1.2. Its behavior
is linked to that of `quick-terminal-position` (`top` by default).
You can configure the default size for both the primary axis (e.g. height)
and the secondary axis (e.g. width) in either a percentage or pixels.
I like my dropdown terminal to be relatively big, so I just do the following:

```
quick-terminal-size = 60%
```

But, you can do a lot more, as indicated by the docs. If you specify two
values, then the second value is the secondary axis. The examples below
illustrate a few things you can do:

```
# Percentage, primary axis only
quick-terminal-size = 25%

# Pixels work too, primary axis only
quick-terminal-size = 600px

# Two values specify primary and secondary axis
quick-terminal-size = 25%,75%

# You can also mix units
quick-terminal-size = 300px,80%
```

As mentioned earlier, the primary axis is defined by the `quick-terminal-position` configuration. For
the `top` and `bottom` values, the primary axis is the height. For the `left` and
`right` values, the primary axis is the width. For `center`, it depends on your
monitor orientation: it is height for landscape and width for portrait.

You've got many options, but I doubt many people will need those. That's all I have for you today.
Keep hacking!
