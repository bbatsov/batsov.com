---
title: Replace Text in Multiple Files
date: 2025-02-19 13:31 +0200
tags:
- Tips
- macOS
- CLI
---

Today I wanted to update a bit markup in my blog and it took me some time
to get it right. Basically I wanted to replace the language in some fenced
code blocks and my instinct was to go for a combination of `find` and `sed`.
I hadn't used in a while, so after a bit of searching around I came up with
the following:

```console
$ find . -name "*.md" -type f -exec sed -i 's/` bash/`shell/g' {} +
sed: 1: "./_posts/2022-01-20-bad ...": invalid command code .
```

This didn't work at first, because `sed -i` expects a backup file extension (e.g. `bak`),
for the files it'd be changing. I didn't want this, as I didn't want to deal with deleting those files afterwards,
so I tweaked the command like this:

```console
$ find . -name "*.md" -type f -exec sed -i '' 's/` bash/`shell/g' {} +
```

At this point I remembered that I can make use of the shell's extended globbing and just pass multiple arguments to
`sed`. This simplifies the command quite a bit:

```console
$ sed -i '' 's/` bash/`shell/g' **/*.md
```

And that's pretty much it! Just keep in mind I'm using BSD sed on macOS, and if you're
using GNU sed (e.g. on Linux) you can omit the empty string argument to `-i`.
Got to love the random differences between BSD and GNU commands!

That's all I have for you today. Keep hacking!
