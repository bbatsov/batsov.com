---
title: 'Learning OCaml: Matching Anything or the Lack of Anything'
date: 2025-02-27 13:48 +0200
tags:
- OCaml
- Learning OCaml
---

I've noticed that some newcomers to OCaml are a bit confused by code like the following:

```ocaml
let () = print_endline "Hello, world"

let _ = foo bar
```

Both of those are forms of pattern matching, but one of them is a lot stricter
than the other. In OCaml `()` is the single value of the `unit` type that
indicates the absence of any meaningful value. You can think of it as something like `void` in
other languages. What this means is that `let ()` would only match an
expression that actually return `unit` (like the various `print_*` functions) and you'd get a compilation error
otherwise:

```console
$ ocaml foo.ml
File "./foo.ml", line 1, characters 9-11:
1 | let () = 10
             ^^
Error: The constant 10 has type int but an expression was expected of type unit
```

`_` on the other hand is a placeholder for "anything" and it will match... anything. It's useful
in cases when you just need to discard something. A common example to illustrate it would be something
like pattern matching on the elements of a list. Consider the following trivial function that returns
the last item from a list:

```ocaml
let rec last = function
  | [] -> None
  | [x] -> Some x
  | _ :: t -> last t
```

Just as in `match`, you can use `let _` to match against any value, effectively discarding it.

In practice, `let ()` is often used at the top level of programs to indicate the
main entry point, while `let _` is used when you need to evaluate an expression
for its side effects but don't care about its return value. I mostly use `let _` when inserting
debug `print` expressions in a chain of nested `let`s. Here's an example:

```ocaml
let foo x =
  let _ = print_int x in
  let y = x * 2 in
  let _ = print_int y in
  let z = y + 10 in
  z
```

The above example is a bit contrived, but I hope you get the idea. Also, you can totally use `let ()` in the example above,
although it seems to me that using `let _` is more intention revealing in such cases.

That's all I have for you today. Keep hacking!
