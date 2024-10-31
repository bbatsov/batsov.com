---
layout: post
title: How to Fix X11 Forwarding on WSL
date: 2024-10-31 10:47 +0200
tags:
- Windows
- WSL
- X11
---

I guess most of you are aware that I've been using Windows 11 + WSL as my main development environment in the past 4 years.[^1] I've spent some time running Emacs with the built-in Windows Wayland server, but after encountering some hard to solve problems I've returned to using X11 as it worked more reliably for me.

All I needed to do was to install some X11 server for Windows (I chose X410) and setup the `DISPLAY` env variable (usually in your `.bashrc` or `.zshrc`). That's how I did this originally, following X410's user manual:

``` shell
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0
```

This worked great for years, but at some point things broke after an Windows update.[^2] I've started to see the following error when I tried to launch GUI apps from WSL:

```
Error: Can't open display: 10.255.255.254:0.0
```

When this happened the first time I was too busy/lazy to check what exactly had changed, so I just hardcoded the `DISPLAY` to the actual IP of my Windows box (e.g. `172.24.192.1:0`) and I mostly forgot about this, but recently I did a bit of digging and discovered that the proper way to extract it automatically is:

```
export DISPLAY=$(ip route list default | awk '{print $3}'):0
```

Works like a charm! Seems the contents of `/etc/resolve.conf` got changed for whatever reasons, that I didn't bother to investigate.

That's all I have for you today. I'm mostly writing this as a reminder to myself, as I'm well aware that ChatGPT will render such articles useless (alongside StackOverflow) a few years down the line. Keep hacking!

[^1]: [Long story.](https://metaredux.com/posts/2021/07/31/back-to-linux.html)
[^2]: I believe it was Windows 11 23H2.
