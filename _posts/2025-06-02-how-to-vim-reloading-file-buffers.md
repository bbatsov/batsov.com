---
title: 'How to Vim: Reloading File Buffers'
date: 2025-06-02 11:11 +0300
tags:
- Vim
---

In Vim (and many other editors) we interact with the contents of files via the "file buffer"
abstraction. Basically, that's the in-memory representation of a file within a text editor,
that occasionally gets synchronized with the disk one (the actual file).

From time to time a file might get changed outside Vim (e.g. you had it changed in another editor or
you pulled some updates from your VCS). In those cases we usually want to reload the file
contents into the file buffer. There are multiple ways to do this in Vim (shocker, right) -
a manual (with a couple of nuances) and an automated approach (with many nuances).

Let's start with the manual, as it's super simple. Just press `:e`(dit) followed by `<CR>`
to reload the current buffer. You can also use `:e!` to discard any changes you may have made.

I'm more partial to the automated way, which involves enabling `autoread`. Just
add it to your `.vimrc` like this:

```viml
set autoread
```

Unfortunately, for whatever reasons, this won't get triggered in all desirable
cases, so we'll need to help Vim a bit with some extra configuration that checks
the file modification time when the editor gains focus or we switch to a
buffer:

```viml
au FocusGained,BufEnter * :silent! checktime
```

One final thing to keep in mind - you can go back to your version of the file
with `u` (undo). So, you don't have to worry that any local edits will get
permanently lost of you enable `autoread`.

So, that's crux of reloading files in Vim - not exactly trivial, but perhaps a
bit more complicated than it needs to be.  Down the road I'll have to check if
that's made simpler in Neovim.

That's all I have for you today. Keep hacking!
