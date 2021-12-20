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

One thing to note is that when you're installing Emacs from source you won't get a menu entry for Emacs in your Windows start menu or Linux distribution application launcher.
This you can easily fix by creating the necessary `.desktop` file (e.g. `/usr/share/applications/emacs29.desktop` on Ubuntu):

```
[Desktop Entry]
Name=Emacs 29
GenericName=Text Editor
Comment=Edit text
MimeType=text/english;text/plain;text/x-makefile;text/x-c++hdr;text/x-c++src;text/x-chdr;text/x-csrc;text/x-java;text/x-moc;text/x-pascal;text/x-tcl;text/x-tex;application/x-shellscript;text/x-c;text/x-c++;
Exec=emacs %F
Icon=emacs
Type=Application
Terminal=false
Categories=Development;TextEditor;
StartupWMClass=Emacs
Keywords=Text;Editor;
```

If you decide to uninstall the version of Emacs you've installed from source you can just run `sudo make uninstall` from the Emacs source folder.

It took me a while to get everything working, but it was also a lot of fun. In recent years I rarely build applications from source,
so I've forgotten how easy and educational this was.[^2] I was even a bit afraid of the whole process! I hope this article will encourage
more of you to play with new features or customize their Emacs installation.

The rumors about the my transition to the Dark Side (VS Code) were premature! Emacs forever!

[^1]: I've updated my other pgtk article to reflect this.
[^2]: When I was a Gentoo user I had spent countless hours tweaking the build flags for all the packages I used frequently. Good times!
