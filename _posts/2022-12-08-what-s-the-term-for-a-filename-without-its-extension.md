---
title: What's the Term for a Filename Without Its Extension?
date: 2022-12-08 16:40 +0200
tags:
- Programming
---

Today someone asked in OCaml's Discord "How do you call a variable that refers
to a filename without its extension?". I always thought there was no specific
term for this and I always named such variables `filename-sans-extension` (or
similar), but it turns out I was wrong. It's never too late to learning
something new! But first a bit of (subjective) terminology:

- `/path/to/some_file.foo` - filepath
- `/path/to` - directory
- `some_file.foo` - filename/basename
- `foo` - extension
- `some_file` - ???

I hope that makes things clear. Now we can proceed!

So what's the term we're looking for? Turns out it's `stem` and it's present in a few popular programming languages:

- [C++ (std::filesystem lib)](https://en.cppreference.com/w/cpp/filesystem/path/stem)
- [Python](https://docs.python.org/3/library/pathlib.html#pathlib.PurePath.stem)
- [Rust](https://docs.rs/pathmut/latest/pathmut/get/fn.stem.html)

Why `stem`? While I can't be sure I'd say it's either a nod to the `tree` terminology
that's pretty pervasive when we're talking about directories, or a nod to [linguistics](https://en.wikipedia.org/wiki/Word_stem):

> In linguistics, a word stem is a part of a word responsible for its lexical
> meaning. The term is used with slightly different meanings depending on the
> morphology of the language in question.

If I had to guess - probably the term originated with C++, given it's the oldest
language I could find that's using it. If someone knows the origin of the `stem`
terminology, please do share! At any rate - I kind of like it and I'll probably
use `stem` or the more descriptive `file_stem` going forward.

That's all I have for you today. Keep hacking!
