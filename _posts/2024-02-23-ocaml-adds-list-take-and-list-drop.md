---
title: OCaml Adds `List.take` and `List.drop`
date: 2024-02-23 08:12 +0100
tags:
- OCaml
---

One of my small issues with OCaml is that the standard library is quite spartan.
Sometimes it misses functions that are quite common in other (similar)
languages. One such example are functions like `drop`, `drop_while`, `take` and
`take_while` in the [List](https://v2.ocaml.org/api/List.html) module.[^1] What's weird is that the similar
[Seq](https://v2.ocaml.org/api/Seq.html) module features all those functions
since OCaml 4.14.

Fortunately, that's finally changing! While perusing the [OCaml
changelog](https://github.com/ocaml/ocaml/blob/trunk/Changes) a few days ago, I
noticed a reference to a [recently merged pull
request](https://github.com/ocaml/ocaml/pull/9968) that adds the missing `List`
functions. It's interesting that this PR is a follow-up to another PR that was a
bit more ambitious and was created [way back in Oct
2020](https://github.com/ocaml/ocaml/pull/9968). Oh, well - better late than
never, right?

As you can imagine there's nothing fancy about the implementation of the new functions:

``` ocaml
let take n l =
  let[@tail_mod_cons] rec aux n l =
    match n, l with
    | 0, _ | _, [] -> []
    | n, x::l -> x::aux (n - 1) l
  in
  if n < 0 then invalid_arg "List.take";
  aux n l

let drop n l =
  let rec aux i = function
    | _x::l when i < n -> aux (i + 1) l
    | rest -> rest
  in
  if n < 0 then invalid_arg "List.drop";
  aux 0 l

let take_while p l =
  let[@tail_mod_cons] rec aux = function
    | x::l when p x -> x::aux l
    | _rest -> []
  in
  aux l

let rec drop_while p = function
  | x::l when p x -> drop_while p l
  | rest -> rest
```

Pretty standard recursive implementations. If you're not familiar with
`@tail_mod_cons` - it's basically tail-call optimization for `::` (a.k.a. `cons`)
in the final position of a recursive function.[^2]

It seems the new `List` functions will be shipped with OCaml 5.3. OCaml 5.2 is
not out at the time I'm writing this, but I'm guessing the PR missed the merge
window for 5.2.  In the meantime - we can continue to rely on the excellent
[Containers
library](http://c-cube.github.io/ocaml-containers/last/containers/CCList/index.html)
for that functionality.

That's all I have for you today. Keep hacking!

[^1]: I believe it was Haskell that popularized them.
[^2]: See <https://v2.ocaml.org/manual/tail_mod_cons.html> for more details.
