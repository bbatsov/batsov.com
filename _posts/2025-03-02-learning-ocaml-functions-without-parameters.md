---
title: 'Learning OCaml: Functions without Parameters'
date: 2025-03-02 12:47 +0200
tags:
- OCaml
- Learning OCaml
---

A couple of days ago I noticed on OCaml's Discord server that someone was
confused by OCaml function applications (invocations) like these:

``` ocaml
print_newline ()

read_input ()
```

To people coming from "conventional" programming languages this might look like
calling a function/method without any arguments. (e.g. `foo()` in Python) Of course,
function application in OCaml is quite different from JavaScript, Python and the like -
the function arguments are space separated and simply follow the function's name:

``` ocaml
foo arg1 arg1 arg3
```

So what are those `()` then and why are they needed? I'll start with the second part of the question.
In OCaml you can't really define a function without any parameters - if we have to be super
precise, every function takes **exactly** one parameter, no matter how it might look
at a glance.[^1] If you try to do something like:

``` ocaml
let my_print = print_endline "Hello"
val my_print : unit = ()
```

You'll just end up with static binding to nothing. Or not quite nothing, as it's time to
talk about `()`, which happens to be the single instance of the `unit` type, used to represent
the absence of a meaningful value. So, when you want to define a function that doesn't need
any parameters the convention is to use a single `unit` parameter:

``` ocaml
(* This defines a function that takes unit *)
let say_hello () =
  print_endline "Hello, world!"

(* same here *)
let random_int () =
  Random.int 100
```

I hope this also explains why'd have to call such functions with `()` as their
argument. If you don't do this - you'd just receive the underlying function
object as the result:

``` ocaml
# say_hello;;
- : unit -> unit = <fun>
# say_hello ();;
Hello, world!
- : unit = ()
# random_int;;
- : unit -> int = <fun>
# random_int ();;
- : int = 10
```

Basically, you need to pass the argument to have an actual function application.
Remember that in OCaml (and most functional programming languages), functions are first-class
objects that you can pass around like any other value.

And that's a wrap. I hope you learned something useful today. Keep hacking!

[^1]: See <https://dev.realworldocaml.org/variables-and-functions.html#multi-argument-functions>.
