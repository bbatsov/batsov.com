---
title: Building Emacs from Source with pgtk
date: 2021-12-19 10:32 +0200
---

Yesterday the pure GTK (a.k.a. `pgtk`) feature branch was [finally merged](https://www.reddit.com/r/emacs/comments/rj8k32/the_pgtk_pure_gtk_branch_was_merged/) in Emacs's master.
In a [recent article]({% post_url 2021-12-06-emacs-is-not-a-proper-gtk-application %}) I mentioned
I was super excited about it, because this meant proper Wayland support for Emacs and by association - native support
for running Emacs's GUI version on Windows 11 with WSL.

Truth be told, I was under the impression the branch was already merged months ago and was going to be shipped with Emacs 28.1, but it turns out I was mistaken.[^1]
When I learned we have to wait for Emacs 29, instead of Emacs 28, I immediately decided to build Emacs from the `master` branch. I'm using
Ubuntu 20.04 (running on Windows 11) and the build process was super simple:

``` shellsession
$ git clone https://git.savannah.gnu.org/git/emacs.git
$ sudo apt install build-essentials libgtk-3-dev libgnutls28-dev texinfo
$ cd emacs
$ ./autogen.sh
$ ./configure --with-pgtk
$ make -j8
$ sudo make install
```

I guess those commands are self-explanatory, but let's go over them in some details:

* We clone Emacs's Git repo locally
* We install the packages needed to build Emacs with `pgtk` support.
  * `build-essentials` is GCC and other related libraries.
  * `texinfo` is needed for the Info documentation and `libgnutls28` is a generic dependency (you can disable `tls` support, though).
  * I guess it's clear why we need `libgtk-3-dev`.
  * Perhaps some other packages were also needed, but I just had them installed beforehand. You'll get pretty informative error messages from `configure`, so it'd be easy to install whatever else is needed.
* We switch to the Emacs's repo folder, so we can start the build process there.
* We configure, compile and install Emacs. I have an 8-core CPU, there for the `make -j8`.

As you can see I went with the minimal feature-set needed by me (only `-with-pgtk`). Feel free to add whatever `--with-x` flags you might need, but keep in mind that most
compilation flags will require you got install additional packages.

In the end Emacs will get installed in `/usr/local/` and you'll have the `emacs-29.0.50` binary under `/usr/local/bin`. Just run it and that's it. I'm writing this article from my brand new
Emacs 29 running on WSL and it's gorgeous - gone are the blurry fonts and the need to use a 3rd party X server as a stop-gap measure. It also seems that Emacs is a bit snappier, but this might
be just my wishful thinking.

The rumors about the my transition to the Dark Side (VS Code) were premature! Emacs forever!

[^1]: I've updated my other pgtk article to reflect this.
