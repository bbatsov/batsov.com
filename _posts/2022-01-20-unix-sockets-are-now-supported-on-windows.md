---
title: Unix Sockets are Now Supported on Windows
date: 2022-01-20 10:57 +0200
tags:
- Windows
---

I was surprised to learn that [Windows has added support for Unix sockets](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) (AF_UNIX)
a few years ago (in 2017). From the announcement article:

> Beginning in Insider Build 17063, you'll be able to use the unix socket (AF_UNIX) address family on Windows to communicate between Win32 processes. Unix sockets allow inter-process communication (IPC) between processes on the same machine.

In practice that means that all recent builds of Windows 10 support Unix sockets, plus Windows 11, of course. It's really great to see the evolution of Windows in the past few years - it becomes more developer-friendly every day.
