---
title: Interactive Programming in a Nutshell
date: 2021-12-24 10:16 +0200
link: https://common-lisp.net/features
tags:
- Lisp
- Clojure
- Emacs
- Interactive Programming
---

Today I came across this great summary of the concept of interactive programming:

> Development in Common Lisp is interactive. There's no separate compile/run/debug cycle. Instead of that, the program is developed while it runs. Compilation is incremental, and functions can be created and updated on the fly. As the program is running, all objects are available and can be inspected all the time. This is much more than a simple REPL; the whole environment, from the IDE to the language is prepared for this type of development.
>
> -- https://common-lisp.net/features

Obviously, this is not something specific to Common Lisp and it applies to most Lisp dialects and many other programming languages.

For me interactive programming is probably *the most important advantage* of Lisps and Emacs over other programming languages and editors. I cannot imagine any productive workflow without it!

I've written a bit about interactive programming with Clojure and Emacs [here](https://docs.cider.mx/cider/usage/interactive_programming.html). Still, I always feel that this is one of those concepts that you can't really understand until you've experienced it. Just like the Matrix.
