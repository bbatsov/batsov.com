---
layout: post
title: Replace Text in Multiple Files
date: 2025-02-19 13:31 +0200
tags:
- Tips
- macOS
- CLI
---

Today I wanted to update a bit markup in my blog and it took me some time
to get it right. Basically I wanted to replace the language in some fenced
code blocks and my instinct was to go for `find` and `sed`:

```console
$ find . -name "*.md" -type f -exec sed -i 's/\` bash/\`shell/g' {} +
```

This didn't work at first, because `sed -i` expects a backup file extension (e.g. `bak`),
for the files it'd be changing. I didn't want this, as I didn't want to deal with dealing those files afterwards,
so I tweaked the command like this:

```console
$ find . -name "*.md" -type f -exec sed -i '' 's/\` bash/\`shell/g' {} +
```

And that's pretty much it! This keep in mind I'm using BSD sed on macOS, and if you're
using GNU sed (e.g. on Linux) you'd have do `-i""` (without a space between `-i` and the empty string).
Got to love the random differences between BSD and GNU commands!

That's all I have for you today. Keep hacking!
