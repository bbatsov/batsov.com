---
title: Automate the Creation of GitHub Releases
date: 2025-02-27 16:21 +0200
tags:
- GitHub Actions
- Project Maintenance
---

I maintain many open-source projects and one of the most common
tasks for me is to "create a GitHub release", which is more or less
the process of attaching some release notes and build artifacts
to a Git tag, so GitHub users would see them on the "Releases"
page of a project. Here's [an example](https://github.com/rubocop/rubocop/releases),
from my RuboCop project.

So, here is what I'd normally do when the time comes to prepare a new release:

- Tag the new release
- Generate some release note (or just copy the relevant section of a project's changelog)
- Copy the release notes manually to GitHub

While, it's not a particularly complicated process, when doing it often for many projects
it becomes kind of annoying. More importantly - from time to time I've forgotten to do it.[^1]
A couple of days ago, a co-maintainer for some projects pointed out we haven't created the
GitHub releases for a couple of projects for a while now. This finally forced me to devise
a proper solution for the problem.

I felt the natural way to solve this would be with GitHub Actions. I was fairly
certain this wouldn't be complex, but it turned out it's even simpler than I
thought, as there's a [ready made GHA on the GHA
marketplace](https://github.com/ncipollo/release-action). The whole solution
looks like this:

``` yaml
name: Create GitHub Release

on:
  push:
    tags:
      - "v*"  # Trigger when a version tag is pushed (e.g., v1.0.0)

jobs:
  create-release:
    runs-on: ubuntu-latest

    permissions:
      contents: write

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Create GitHub Release
        uses: ncipollo/release-action@v1
        with:
          tag: ${{ github.ref_name }}
          name: RuboCop ${{ github.ref_name }}
          bodyFile: relnotes/${{ github.ref_name }}.md
          token: ${{ secrets.GITHUB_TOKEN }}
```

This action will be triggered every time you push a new tag, with a name
starting with `v` (e.g. `v1.2.3`). It's configured to look for the
release notes in a file named `relnotes/tag-name.md`, which happens to be
aligned with how RuboCop generates its release notes. You can adjust
`bodyFile` however you see fit. If you don't maintain release notes, you can
also generate them automatically from the commits in the release like this:

``` yaml
name: Create GitHub Release

on:
  push:
    tags:
      - "v*"  # Trigger when a version tag is pushed (e.g., v1.0.0)

jobs:
  create-release:
    runs-on: ubuntu-latest

    permissions:
      contents: write

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Create GitHub Release with Auto-Generated Notes
        uses: ncipollo/release-action@v1
        with:
          tag: ${{ github.ref_name }}
          name: cider-nrepl ${{ github.ref_name }}
          generateReleaseNotes: true  # Auto-generate release notes based on PRs and commits
          token: ${{ secrets.GITHUB_TOKEN }}

```

This can be further customized to include only certain commits, but that's beyond the scope of this article.
The `release-action` is quite configurable and I'd encourage everyone interested in using it to peruse
its [documentation](https://github.com/ncipollo/release-action?tab=readme-ov-file#action-inputs).

That's all I have you today. Automate all the boring tasks!

[^1]: That's the main weakness of any manual process.
