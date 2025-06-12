---
title: "zoxide: tips and tricks"
date: 2025-06-12 08:18 +0300
tags:
  - zoxide
  - tips-and-tricks
  - shell
  - terminal
  - productivity
  - linux
  - macos
  - command-line
---

[zoxide](https://github.com/ajeetdsouza/zoxide) is a smart and fast alternative
to `cd` that learns your directory usage patterns and allows you to jump to
directories quickly. It's one of my favorite command-line tools and it's an
essential part of my workflow.

Here are some tips and tricks to get the most out of zoxide:

- Normally you'd use `z` to trigger `zoxide`, but pressing `z` is not super
  convenient. That's why I like to use `j` instead. You can set this up by
adding the following to your shell configuration file (e.g., `.bashrc`,
`.zshrc`):

  ```sh
  alias j='z'
  ```

- If you have `fzf` installed, the `zi` command can be used to interactively
  select a directory to jump to. You can also use `jj` with `fzf` by adding the
following alias:

  ```sh
  alias jj='zi'
  ```

- `zoxide` was inspired by `z` and `autojump`, but unlike them it's a complete
  replacement for `cd`. That's why it's not a bad idea to simply alias `cd` to
`z`:

  ```sh
  alias cd='z'
  ```

- If you have multiple directories with similar names you can use SPACE to
  trigger smart completion for your options:

  ```sh
  z mydir<SPACE>
  ```

This will present the matching candidates (using `fzf` if present), and you can
select one to jump to it.

- If you're a Fish user (like me), you can get nice TAB completions by
  installing the following Fish plugin:

  ```sh
  fisher install icezyclon/zoxide.fish
  ```

Now try the following:

  ```sh
  z mydir<TAB>
  ```

You can read more about the plugin [here](https://github.com/icezyclon/zoxide.fish).

And that's a wrap! Even though I do all of the above, I think the first two
items are the most important. `j` and `jj` are very easy to type and cover all
of my use-cases beautifully. If you have any other tips or tricks for zoxide,
feel free to share them in the comments below!

That's all I have for you today! Keep hacking!
