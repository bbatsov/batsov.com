---
title: Working with Multiple Versions of Java on Ubuntu
date: 2021-12-10 12:28 +0200
tags:
- Java
- Ubuntu
- Linux
- Tutorials
---

Today I encountered a bug that was specific to JDK 16 on a project I was working
on, and I needed to switch back my Java version to something older.  I realized
I had forgotten (once again) how to switch between multiple Java version on Ubuntu
(Debian), so I've decided to write a short article that would help me remember
this better.[^1]

You can install easily multiple version of Java on Ubuntu via `apt`:

```console
$ sudo apt install openjdk-8-jdk openjdk-8-source openjdk-8-doc
$ sudo apt install openjdk-11-jdk openjdk-11-source openjdk-11-doc
```

Typically the newest version of Java you install will become the default, but you can easily change this:

```console
$ sudo update-alternatives --config java
There are 2 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                          Priority   Status
------------------------------------------------------------
  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java   1111      auto mode
* 1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java   1111      manual mode
  2            /usr/lib/jvm/java-8-openjdk-amd64/bin/java    1081      manual mode

Press <enter> to keep the current choice[*], or type selection number:
```

Notice that pressing 0 mean "auto-select the newest Java available" (in our case Java 11).
You can now select Java 8 by pressing 2 and verify the command worked properly like this:

```console
$ java -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-8u292-b10-0ubuntu1~20.04-b10)
OpenJDK 64-Bit Server VM (build 25.292-b10, mixed mode)
```

You'll need to repeat the above steps for `javac` (the Java compiler binary) as well:

```console
$ sudo update-alternatives --config javac
```

This much I already knew, even if I keep forgetting the exact name of
`update-alternatives`, but today I learned something new as well. You can
actually simplify the process a bit by using the specialized command
`update-java-alternatives`:

```console
$ update-java-alternatives -l
java-1.11.0-openjdk-amd64      1111       /usr/lib/jvm/java-1.11.0-openjdk-amd64
java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64

$ sudo update-java-alternatives -s java-1.11.0-openjdk-amd64
```

Quite handy! You can also go back to the latest Java version with a shorthand:

```console
$ sudo update-java-alternatives -a
```

`-a` stands for `--auto`, meaning the Java version with highest priority (in our case Java 11 with priority 1111).

That's all I have for you today. Short and sweet!

[^1]: Or at least look up the information faster.
