---
title: 'OCaml Tips: Multiple Let Bindings'
date: 2022-08-28 11:35 +0300
tags:
- OCaml
- Tips
---

One thing that was a bit weird for me in OCaml early on was how to introduce
multiple `let` bindings (e.g. in the body of a function definition). Think
something like this in Clojure:

``` clojure
(let [a 1
      b 2
      c 3])
```

I've noticed many people were using the following syntax:

``` ocaml
let a = 1 in
let b = 2 in
let c = 3 in
a + b + c
```

This works, but seemed a bit too verbose to, especially given that in this case
the bindings don't depend on one another (e.g. we don't compute `c` from `a` and `b`). Turned there's a slight variation of the `let` syntax for cases like this one:

``` ocaml
let a = 1
and b = 2
and c = 3 in
a + b + c

(* or more compactly *)
let a = 1 and b = 2 and c = 3 in a + b + c
```

Still a bit too verbose for my taste, but I definitely like it a more over multiple `let ... in` expressions, as it reads better. As a bonus - this style of introducing bindings clearly shows that all the bindings we introduced are independent of each other, which reduces some of the mental overhead for readers of the code.[^1]

Note, however, that you can't do something like this:

``` ocaml
let a = 1
and b = 2
and c = a + b in
c
```

This will result in a compilation error, as you can't have any of the bindings refer to other bindings within the same `let` expression. Here you'll need `let ... in` to make the `c` binding work:

``` ocaml
let a = 1 and b = 2 in
let c = a + b in
c
```

There's one other option for compact `let` bindings - pattern matching! Most commonly you'd match against a tuple or some record:

``` ocaml
let (a, b, c) = (1, 2, 3) in
a + b + c

let {a; b; c; _} = t in
a + b + c
```

That's all I have for you today. Keep hacking!

[^1]: You can read more on the subject [here](https://v2.ocaml.org/manual/expr.html#sss:expr-localdef).
