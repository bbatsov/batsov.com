---
title: Switching to Minimal Mistakes
date: 2021-11-01 12:14 +0200
tags:
- Jekyll
---

Today I've switched the blog's theme from [Hydeout](https://github.com/fongandrew/hydeout) to [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/). I did so for several reasons:

- I didn't like the color scheme that Hydeout used for syntax highlighting, and I didn't want to fiddle with this myself.
- I wanted a theme that provides more flexibility and had active maintenance, big community and good documentation.
- I liked the visuals of Minimal Mistakes. And its name. I know I've made plenty of big mistakes so far, so the prospect of minimal mistakes is appealing to me.

I considered changing the blog's theme for a while now, but I couldn't find any
theme that I really liked. In general Jekyll themes are a hot mess - most of
them are buggy, abandonware or some combination of both. I think that the only
theme that I've never had any issues with was Jekyll's default theme - "minima".
It's not fancy, but it gets the job done and doesn't create any problems.

Minimal Mistakes was the only theme that got my attention during search for a good Jekyll theme, but
for some reason I was never really in the mood to sit down and do the actual migration work. Until today.
What triggered me to go
forward was a [small
Hydeout bug](https://github.com/fongandrew/hydeout/pull/104#issuecomment-954457430) into
which I ran a few days ago. I guess I just needed a little push.

The migration process was super simple and it basically consisted of the following steps:

* Read the [Quick Start Guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/) and copy and edit a few config files that it refers to.
* Replace the uses of the `post` and `page` layouts with the `single` layout (for me this simply meant editing the metadata of a bunch of posts of pages).
* Push to GitHub Pages.

The documentation is so good, that I got everything working for the first try. I think then entire process took me about an hour.

There are a lot of features of Minimal Mistakes that I haven't leveraged yet,
but I'm quite happy with the initial result and the simplicity of the migration process.
I plan to gradually adopt more of the theme and I might write a follow-up article, if I feel that
something deserves additional attention.
