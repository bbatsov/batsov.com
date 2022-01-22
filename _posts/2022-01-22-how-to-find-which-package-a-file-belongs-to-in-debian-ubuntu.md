---
title: How to Find Which Package a File Belongs to in Debian/Ubuntu
date: 2022-01-22 10:55 +0200
tags:
- Tutorial
- Linux
- Ubuntu
- Debian
---

Occasionally I need to figure out which Debian package some file comes from (e.g. because I want to remove a redundant package or find related packages). There are a couple of ways to do this in Debian, with the simplest being the following:

``` shellsession
$ dpkg -S /usr/bin/ag
silversearcher-ag: /usr/bin/ag
$ dpkg -S /usr/bin/gcc
gcc: /usr/bin/gcc
```

`dpkg` is a built-in command, so it's always around. `-S` stands for `--search`:

```
-S, --search filename-search-pattern...
Search for a filename from installed packages.
```

Note, that it's best to use absolute paths if you want to get a concrete package as the result. Observe the difference here:

``` shellsession
$ dpkg -S /bin/ls
coreutils: /bin/ls
$ dpkg -S bin/ls
kmod: /sbin/lsmod
pciutils: /usr/bin/lspci
util-linux: /bin/lsblk
util-linux: /usr/bin/lsns
usbutils: /usr/bin/lsusb
e2fsprogs: /usr/bin/lsattr
util-linux: /usr/bin/lsmem
util-linux: /usr/bin/lslogins
initramfs-tools-core: /usr/bin/lsinitramfs
util-linux: /usr/bin/lsipc
kmod: /bin/lsmod
util-linux: /usr/bin/lslocks
gnupg-utils: /usr/bin/lspgpot
lsof: /usr/bin/lsof
coreutils: /bin/ls
util-linux: /usr/bin/lscpu
klibc-utils: /usr/lib/klibc/bin/ls
lshw: /usr/bin/lshw
lsb-release: /usr/bin/lsb_release
```

Here you can stop reading, as 99% of the time that's probably the best option for you. Still, there's one more way to approach the problem, namely by using `apt-file`. You'll need to install `apt-file` and initialize its database first:

``` shellsession
$ sudo apt install apt-file
$ sudo apt-file update
```

Using `apt-file` is quite simple:

``` shellsession
$ apt-file search /usr/bin/gcc
gcc: /usr/bin/gcc
gcc: /usr/bin/gcc-ar
gcc: /usr/bin/gcc-nm
gcc: /usr/bin/gcc-ranlib
gcc-10: /usr/bin/gcc-10
gcc-10: /usr/bin/gcc-ar-10
gcc-10: /usr/bin/gcc-nm-10
gcc-10: /usr/bin/gcc-ranlib-10
gcc-7: /usr/bin/gcc-7
gcc-7: /usr/bin/gcc-ar-7
gcc-7: /usr/bin/gcc-nm-7
gcc-7: /usr/bin/gcc-ranlib-7
gcc-8: /usr/bin/gcc-8
gcc-8: /usr/bin/gcc-ar-8
gcc-8: /usr/bin/gcc-nm-8
gcc-8: /usr/bin/gcc-ranlib-8
gcc-9: /usr/bin/gcc-9
gcc-9: /usr/bin/gcc-ar-9
gcc-9: /usr/bin/gcc-nm-9
gcc-9: /usr/bin/gcc-ranlib-9
gcc-opt: /usr/bin/gcc-3.3
gcc-opt: /usr/bin/gcc-3.4
gcc-opt: /usr/bin/gcc-4.0
gcc-python3-dbg-plugin: /usr/bin/gcc-with-python3_dbg
gcc-python3-plugin: /usr/bin/gcc-with-python3
gccbrig: /usr/bin/gccbrig
gccbrig-10: /usr/bin/gccbrig-10
gccbrig-7: /usr/bin/gccbrig-7
gccbrig-8: /usr/bin/gccbrig-8
gccbrig-9: /usr/bin/gccbrig-9
gccgo: /usr/bin/gccgo
gccgo-10: /usr/bin/gccgo-10
gccgo-7: /usr/bin/gccgo-7
gccgo-8: /usr/bin/gccgo-8
gccgo-9: /usr/bin/gccgo-9
pentium-builder: /usr/bin/gcc
xutils-dev: /usr/bin/gccmakedep
```

As you can see by default it provides all possible matches from its database.
One advantage that `apt-file` has over `dpkg` is that it will search through *all
available* packages as opposed to *all installed* packages. That might be handy
sometimes when you know you need to have some file installed, but you don't know
which package contains it. Admittedly, I didn't know about `apt-file` until very
recently myself, which is part of the reason I've decided to put together this short article.[^1]

That's all I have for you today. Keep hacking!

[^1]: When I write about something I just learned, the likelihood of retaining the new knowledge increases significantly. Still, nothing beats applying the new knowledge in practice all the time.
