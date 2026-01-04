---
title: 'How to Vim: Alternative Approach to Find and Replace'
date: 2026-01-04 12:33 +0200
tags:
- Vim
---

The classic way to do "find and replace" in Vim is pretty well known:

```
:%s/target/replacement/gc
```

This will replace all instances of `target` in the current buffer (that's what the `%` is about)
with `target`. The `c` flag means you'll get prompted for confirmation for every replacement.
Not bad, right?

Still, often you need to replace just a few instances of something, so the above might be a bit too much
typing. Imagine you're dealing with the following text:

```
foo bar
foo baz
foo bar
```

If you want to replace the `bar` instances with `baz` the fastest way to do this would be something like:

- `/bar` - this will take you to the beginning of `bar`
- `cwbaz` - this will replace `bar` with `baz`
- `n.` - this will take you to the next match and repeat the last edit you did

Pretty sweet and quite interactive in my opinion. It also allows you easily
skip matches you don't want to replace. And there are a few other tricks you can keep in mind:

- You can use `*` to select the word under the cursor and start a search with it
- If you're searching for something more complex (e.g. it has multiple words) you can use
`cgn` instead of `cw`. `gn` means the next search match.

So, there you have it - another way to do "find and replace" in Vim! Keep hacking!
