---
layout: single
title: "Enabling 3D support for Nouveau in Fedora 13"
tags:
- Fedora
- Linux
- Hardware
---

Most of you probably have heard that Fedora 13 will feature
experimental 3D support for the Nouveau open source driver for Nvidia
cards. This support, however, is not enabled by default. To enable it
you need to install the `mesa-dri-drivers-experimental` package. I,
personally, do it like this (if you haven’t configured `sudo`, you’ll
have to perform this as `root`):

``` shell
$ sudo yum install mesa-dri-drivers-experimental
```

Afterwards you can enable Compiz, play [chromium-bsu](https://chromium-bsu.sourceforge.io/) or whatever else your heart desires.
