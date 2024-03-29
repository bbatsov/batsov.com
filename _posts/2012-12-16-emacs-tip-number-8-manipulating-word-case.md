---
layout: single
title: "Emacs Tip #8: Manipulating Word Case"
date: 2012-12-16 17:57
tags:
- Emacs
- Tips
---

One operation that we have to do fairly often when editing text is
manipulating the case of words. The most popular case manipulations
are probably **capitalize**, **convert to lowercase** and **convert to
uppercase**. Emacs naturally has built-in commands for all of those.

Pressing `M-c` runs the command `capitalize-word`, which will
capitalize the next word and move the cursor after it. Pressing `M--
M-c` will capitalize the previous word without moving the cursor.

Pressing `M-l` runs the command `downcase-word`, which will lowercase
the next word and move the cursor after it. Pressing `M-- M-l` will
lowercase the previous word without moving the cursor.

Pressing `M-u` runs the command `upcase-word`, which will uppercase the
next word and move the cursor after it. Pressing `M-- M-u` will uppercase
the previous word without moving the cursor.
