---
title: 'Learning OCaml: Verifying tail-recursion with @tailcall'
date: 2024-01-16 10:52 +0200
tags:
- OCaml
- Learning OCaml
---

How can you be sure that an OCaml function you wrote is actually tail-recursive?
You can certainly compile the code and look at the generated assembly code, but that'd be quite the overkill, given there is a much simpler way to do this.

OCaml 4.03 introduced the `@tailcall` [attribute](https://v2.ocaml.org/manual/attributes.html) which will trigger a compiler warning if it's not placed at an actual tail-call.[^1] It should be used like this:

> `(f [@tailcall]) x y` warns if f x y is not a tail-call

Here are a couple of trivial examples to help illustrate this:

``` ocaml
(* tail-recursive factorial function *)
let rec fact1 acc x =
  if x <= 1 then acc else (fact1 [@tailcall]) (acc * x) (x - 1)

(* non tail-recursive factorial function *)
let rec fact2 x =
  if x <= 1 then 1 else x * (fact2 [@tailcall]) (x - 1)
```

Save the code above in a file named `tailcall.ml` and compile it with `ocamlc`:

``` shellsession
$ ocamlc tailcall.ml
File "./tailcall.ml", line 5, characters 28-55:
5 |   if x <= 1 then 1 else x * (fact2 [@tailcall]) (x - 1)
                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^
Warning 51 [wrong-tailcall-expectation]: expected tailcall
```

As you can see the compiler properly detected that `fact2` is not tail-recursive, as the tail-call is `*` instead of `fact2`.
Small, but handy feature that helps you ensure your code works the way you intended it to work.

That's all I have for you today. Keep hacking!

[^1]: Basically the tail-call is the call that triggers the recursion in the function and for a function to be tail-recursive the last call has to be an invocation of the recursive function itself.
