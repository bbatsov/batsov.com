---
title: 'How to Fish: Moving Between Recent Directories'
date: 2025-06-09 12:01 +0300
tags:
- Fish
---

I love Fish, because it makes a lot of common everyday tasks easier and more
convenient. One such task is moving (switching) between folders you've visited
recently. In my case I often jump between project directories, configuration
directories, etc. Historically I've used things like `cd -`, `pushd`/`popd` and
`autojump`/`zoxide`, but ever since I moved to Fish I realized all I needed
was covered by the following built-in commands:

- `prevd` (`Alt+left arrow`) - takes you to the previous directory
- `nextd` (`Alt+right arrow`) - takes you to the next directory
- `dirh` (`Ald+d`) - shows a list of the recently visited directories
- `cdh` - allows you to quickly jump to any recently visited directly using letter or number shortcuts

Basically, things that require some manual setup/operations in
other shells are handled automatically in Fish, as it maintains
a list of the last 25 visited folders out-of-the-box.[^1]

Here's how using `cdh` looks like:

```shell
cdh
 c  3)  ~/projects/batsov.com
 b  2)  ~/projects/rubocop-ast
 a  1)  ~/projects/rubocop
Select directory by letter or number:
```

Now you can press `b` or `2` to switch to `rubocop-ast`.  I mostly use `prevd`
and `nextd` (via their keybindings), but `cdh` is quite handy as well.
Admittedly keybindings that use arrow keys are not ideal on every keyboard (as
some compact keyboards don't have arrow keys), but those can be easily remapped
to whatever keys you fancy.

At this point I see no need for external tools to help with the
directory history management, although you have to keep in mind that the
directory history in Fish is not persistent, so you might still
find some value in `autojump`/`zoxide` and friends.

That's all I have for you today! Happy Fishing!

[^1]: You can read more on the subject [here](https://fishshell.com/docs/current/interactive.html#directory-history).
