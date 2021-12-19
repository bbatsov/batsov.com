---
title: Emacs is not a Proper GTK Application
date: 2021-12-06 19:34 +0200
link: https://emacshorrors.com/posts/psa-emacs-is-not-a-proper-gtk-application.html
tags:
- Emacs
- Windows
- WSL
---

I got reminded yesterday that Emacs is not a proper GTK application after installing
Windows 11 to make use of its [built-in support for Linux GUI apps running in WSL](https://docs.microsoft.com/en-us/windows/wsl/tutorials/gui-apps). Windows uses Wayland/Weston and the results are great with native GTK apps like GEdit, but
unfortunately [HiDPI scaling doesn't work properly with Emacs](https://github.com/microsoft/wslg/issues/190). Bummer!

The upcoming Emacs 28 should fix this, as it introduces a pure GTK front-end
(a.k.a. `pgtk`). I'm wondering whether to build Emacs 28 from source or not, so
I can get it sooner. In the mean time my old setup [based on
X410](https://emacsredux.com/blog/2020/09/23/using-emacs-on-windows-with-wsl2/)
continues to work really great and I guess I'll stick to it for now.

At any rate - it seems that the ultimate Emacs experience on Windows is right around the corner.
