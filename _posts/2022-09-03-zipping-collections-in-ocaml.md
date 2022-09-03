---
title: Zipping Collections in OCaml
date: 2022-09-03 11:13 +0300
tags:
- OCaml
---

Many programming languages have a function for combining the elements of multiple collections (e.g. arrays or lists) together. Typically this function is named `zip`. Here's an example from Haskell:

``` haskell
zip [1, 2] ['a', 'b'] -- => [(1, 'a'), (2, 'b')]
```

When I've started to [learn OCaml]({% post_url 2022-08-19-learning-ocaml %}) it took a me a while to find the matching function, as it was named differently - namely `combine`. Here's how it works for lists:

``` ocaml
List.combine [1;2;3] [4;5;6];;
- : (int * int) list = [(1, 4); (2, 5); (3, 6)]

(* notice the super informative error message *)
List.combine [1;2;3] [4;5;6;7];;
Exception: Invalid_argument "List.combine".
```

There's also `Array.combine`, which work identically for arrays:

``` ocaml
Array.combine [|1;2;3|] [|4;5;6|];;
- : (int * int) array = [|(1, 4); (2, 5); (3, 6)|]
```

One thing to note is that `List.combine` (and `Array.combine`) will raise an exception if the two lists don't have the same size. Whether this is good or bad I'll leave to you to decide. Personally, I prefer the behavior where the length of the shorter list determines the length of the result. The popular library [Containers](https://github.com/c-cube/ocaml-containers) (it packs many extensions to the standard library), features just the right function for this:

``` ocaml
CCList.combine_shortest [1;2;3] [4;5;6;7];;
- : (int * int) list = [(1, 4); (2, 5); (3, 6)]
```

There's also `CCList.combine`, which is basically a more efficient version of `List.cobime` from the standard library.

The popular [Base](https://opensource.janestreet.com/base/) library has similar functions to the those in the standard library, but there they are named `List.zip`, `List.zip_exn`, `Array.zip` and `Array.zip_exn`. Both of the require the lists/arrays to be of the same length, but the first returns an error type and the second raises an exception.

The reverse operation of zipping/combining is named `split` in the standard library:

``` ocaml
List.split [(1, 4); (2, 5); (3, 6)]
- : int list * int list = ([1; 2; 3], [4; 5; 6])
```

In `Base` it's named `unzip`.

As you can see - you've got plenty of options for zipping collections in OCaml, even if they require a bit of discovery time. That's all I have for you today! Keep hacking!
