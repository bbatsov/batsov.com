---
title: Remove Stale Labels Instantly with GitHub Actions
date: 2025-05-22 10:23 +0300
tags:
- GitHub
- GitHub Actions
---

I maintain a lot of OSS projects and often I struggle to keep up with all the
tickets and pull requests they receive. That's why I'm fond of the [stale GHA
workflow](https://github.com/actions/stale) that automatically marks issues and
PRs as stale after a (configurable) period of inactivity.  This serves to
remind me about things I've forgotten about and keeps contributors engaged.[^1]

One annoying issue with the stale workflow is that it runs only once a day, so
any activity on the issue or PR in the mean time is not reflected immediately.
Fortunately, it's very easy to address this by adding an additional GHA that
just removes the "stale" label right after a new comment is added.

Here's the GHA code that does this:

```markdown
name: Unmark issues and pull requests as stale on activity
on:
  issue_comment:
    types: [created]

# actions/stale does this automatically, but only once a day.
# This immediately removes the label when the user creates a comment.
jobs:
  remove-stale-label:
    if: (github.repository == 'rubocop/rubocop')
    runs-on: ubuntu-latest
    permissions:
      issues: write
      pull-requests: write
    steps:
      - name: Remove stale label
        env:
          GH_TOKEN: ${{ github.token }}
        run: gh issue edit ${{ github.event.issue.number }} --remove-label "stale" -R ${{ github.repository }}
```

Pretty simple, right? The above is the code we use in my popular RuboCop
project, so I'm fairly confident it works well in practice.  Just save it as
something like `unstale.yml` and add it alongside your other GHAs.

That's all I have for you today. Keep hacking!

[^1]: I know both points are controversial and many people hate automated staleness checks.

