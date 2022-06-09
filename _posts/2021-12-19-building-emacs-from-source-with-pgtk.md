---
title: Building Emacs from Source with pgtk
date: 2021-12-19 10:32 +0200
tags:
- Emacs
- WSL
---

Yesterday the pure GTK (a.k.a. `pgtk`) feature branch was [finally merged](https://www.reddit.com/r/emacs/comments/rj8k32/the_pgtk_pure_gtk_branch_was_merged/) in Emacs's master.
In a [recent article]({% post_url 2021-12-06-emacs-is-not-a-proper-gtk-application %}) I mentioned
I was super excited about it, because this meant proper Wayland support for Emacs and by association - native support
for running Emacs's GUI version on Windows 11 with WSL.

Truth be told, I was under the impression the `pgtk` branch was already merged months ago and was going to be shipped with Emacs 28.1, but it turns out I was mistaken.[^1]
When I learned we have to wait for Emacs 29, instead of Emacs 28, I immediately decided to build Emacs from the `master` branch. I'm using
Ubuntu 20.04 (running on Windows 11) and the build process was super simple:

``` shellsession
$ git clone git://git.sv.gnu.org/emacs.git
$ sudo apt install build-essential libgtk-3-dev libgnutls28-dev libtiff5-dev libgif-dev libjpeg-dev libpng-dev libxpm-dev libncurses-dev texinfo
$ cd emacs
$ ./autogen.sh
$ ./configure --with-pgtk
$ make -j8
$ sudo make install
```

I guess those commands are self-explanatory, but let's go over them in some details:

* We clone Emacs's Git repo locally (this will take a while!)
* We install the packages needed to build Emacs with `pgtk` support.
  * `build-essential` is GCC and other related libraries (e.g. Make and `libc`).
  * `texinfo` is needed for the Info documentation and `libgnutls28` is a generic dependency (you can disable `gnutls` support with `--with-gnutls=no`).
  * There are bunch of deps for dealing with images (e.g. `tiff`, `png`, `jpeg`)
  * `libncurses` is a library for console UIs
  * I guess it's clear why we need `libgtk-3-dev`.
  * Perhaps some other packages were also needed, but I just had them installed beforehand. You'll get pretty informative error messages from `configure`, so it'd be easy to install whatever else is needed.
* We switch to the Emacs's repo folder, so we can start the build process there.
* We configure, compile and install Emacs. I have an 8-core CPU, therefore the `make -j8`.

As you can see I went with the minimal feature-set needed by me (only
`-with-pgtk`). Feel free to add whatever `--with-x` (optional feature) flags you
might need, but keep in mind that most optional features will require you to
install additional packages. You can see a list of all compilation flags with
`./configure --help`. It's also a good idea to check out the [official Emacs installation guide](https://git.savannah.gnu.org/cgit/emacs.git/tree/INSTALL).

Out of the available optional features I think that the two most
useful for the majority of people are probably native JSON support (`--with-json`) and
native compilation support (`--with-native-compilation`). If you decide to go for them you'll need to install a few extra packages and tweak the `configure` command:

``` shellsession
# Native JSON
$ sudo apt install libjansson4 libjansson-dev
# Native Complilation
$ sudo apt install libgccjit0 libgccjit-10-dev gcc-10 g++-10
$ export CC=/usr/bin/gcc-10 CXX=/usr/bin/gcc-10
$ ./configure --with-native-compilation --with-json --with-pgtk
```

In the end Emacs will get installed in `/usr/local/` and you'll have the `emacs` (and `emacs-29.0.50`) binary under `/usr/local/bin`. Just run it and that's it. I'm writing this article from my brand new
Emacs 29 running on WSL and it's gorgeous - gone are the blurry fonts and the need to use a 3rd party X server as a stop-gap measure. It also seems that Emacs is a bit snappier, but this might
be just my wishful thinking. My favorite improvement - Emacs doesn't die when my computer goes to sleep (this was a nasty limitation of the X server I was using with Windows 10).

![emacs_with_pgtk.png](/assets/images/emacs_with_pgtk.png)

You'll notice that now Emacs has proper GTK "chrome" (e.g. the frame
title/header and the menubar). While the screenshot above is from Windows,
everyone using Wayland on Linux will experience the same benefits as well.

You can tweak the chrome by with the handy `gnome-tweaks` utility:

``` shellsession
$ apt install gnome-tweaks
```

It's a simple GUI app where you can customize all sorts of [visual appearance
attributes](https://itsfoss.com/gnome-tweak-tool/) of GNOME/GTK applications.
For instance - I use it to select a GNOME theme and to add minimize and
maximize buttons to the application windows.

On a related note - you may encounter some warnings on Emacs startup that some files from the cursor theme cannot be loaded.
Basically, the problem is that most likely no GNOME theme is currently selected (simply because you don't use GNOME directly).
Just select some theme in `gnome-tweak` and the warnings will go away.

Emacs 29 also ships with an improved global minor mode for scrolling with a
mouse or a touchpad, that you might want to enable as well:

``` elisp
(pixel-scroll-precision-mode)
```

Here's the description of `pixel-scroll-precision-mode` from Emacs's NEWS:

> When enabled, and if your mouse supports it, you can scroll the
> display up or down at pixel resolution, according to what your mouse
> wheel reports.  Unlike 'pixel-scroll-mode', this mode scrolls the
> display pixel-by-pixel, as opposed to only animating line-by-line
> scrolls.

In my experience this resulted in much smoother scrolling. It's not very clear to me what's the difference with the older `pixel-scroll-mode`, but the new one definitely worked better.

`make install` will also create an `emacs.desktop` file under `/usr/local/share/applications/emacs.desktop`, so you'd get a menu entry for Emacs (and `Emacs Client`) in your Windows start menu or Linux distribution application launcher. For some reason with this launcher Emacs's icon got replaced with a generic Linux icon in Windows,
but I have been unable to figure out what exactly went wrong. I'll update the article when I figure this out.

![emacs_windows_launcher.png](/assets/images/emacs_windows_launcher.png)

If you decide to uninstall the version of Emacs you've installed from source you can just run `sudo make uninstall` from the Emacs source folder.

It took me a while to get everything working, but it was also a lot of fun. In recent years I rarely build applications from source,
so I've forgotten how easy and educational this was.[^2] I was even a bit afraid of the whole process! I hope this article will encourage
more of you to play with new features or customize their Emacs installation.

The rumors about the my transition to the Dark Side (VS Code) were premature! Emacs forever!

[^1]: I've updated my other pgtk article to reflect this.
[^2]: When I was a Gentoo user I had spent countless hours tweaking the build flags for all the packages I used frequently. Good times!
