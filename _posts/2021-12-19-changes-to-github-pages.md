---
title: Changes to GitHub Pages
date: 2021-12-19 18:44 +0200
tags:
- Jekyll
- GitHub Pages
---

Today I've noticed that updates to my site resulted in GitHub Pages deployment errors, even though everything was working fine locally with `jekyll --serve` and I hadn't changed my site's `config.yml` in a while.

I also noticed that now GitHub Pages is using GitHub Actions[^1] to build and deploy Jekyll sites and you can now see [the build logs](https://github.com/bbatsov/batsov.com/runs/4573932124?check_suite_focus=true):

```
github-pages 222 | Error:  The minimal-mistakes-jekyll theme could not be found.
```

It took me a while to figure this out, but it turned out that GitHub tightened the `config.yml` validation and now some scenarios that used to work will result in errors. In my particular case I had the following in my `config.yml`:

``` yaml
theme                    : "minimal-mistakes"
remote_theme             : "mmistakes/minimal-mistakes"
```

Turns out you shouldn't have both keys, so I removed `theme` and everything started working again. As a reminder - you should use `theme` only with the themes that
are [supported natively](https://pages.github.com/themes/) by GitHub Pages, and
`remote_theme` with all other themes.

Seems that the recent changes are just the first step towards GitHub Pages moving away from Jekyll:

> The initial benefit of this change is enabling you to see your build logs and any errors that may occur which has been a long standing issue for Pages users. However, in the future this will enable us to give you the ability to fully customize your pages build and deployment workflow to use any static site generator you want without having to push the build output to a special branch of the repository.

While I'm very fond of Jekyll, I welcome GitHub's efforts to provide more options to its users. I was a bit frustrated with the breakage, but without it I wouldn't have learned about the changes and the plans for the future.

I hope this short article will help those of you who happen to run into the same issue.

[^1]: See <https://github.blog/changelog/2021-12-16-github-pages-using-github-actions-for-builds-and-deployments-for-public-repositories/>.
