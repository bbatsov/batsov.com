---
title: 'How to Vim: Joining Lines'
date: 2025-12-29 09:36 +0200
tags:
- vim
---

Joining adjacent lines is something that comes up
often while editing text/code. That's why it should come
as no surprise that this is something well supported by
Vim. You have two ways to join lines at your disposal:

- Using `J` joins lines with a space between each line
- Using `gJ` joins lines without a space between each line

If you use the commands by themselves they are going to join
the current line with the line after it, but like usual in
Vim the real magic happens when you operate on some range
of lines or text objects:

- `5J` will join the next 5 lines
- `vipJ` will select the current paragraph in visual mode and join all the lines there

In general I think that when it comes to joining multiple lines visual mode makes
a lot of sense, as you're pretty clear up front what you're about to join.

There a few more nuances to joining lines, especially if you're using `:join` instead
of `J`, but I think that few people need to know of those. For the curious
readers there's always `:help J` to learn more on the topic.

That's all I have for you today. Keep hacking!

