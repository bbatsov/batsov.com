---
title: 'OCaml Tips: Converting a String to a List of Characters'
date: 2022-10-24 10:33 +0300
tags:
- OCaml
- Tips
---

While playing with OCaml I was surprised to learn there's no built-in
function the convert a string to a list of its characters. Admittedly, that's
not something you need very often, but it does come handy from time to time.
There are many ways to implement such a function ourselves and the one I like
the most makes use of `List.init`:

``` ocaml
let explode_string s = List.init (String.length s) (String.get s);;
val explode_string : string -> char list = <fun>

explode_string "hello, world!";;
- : char list = ['h'; 'e'; 'l'; 'l'; 'o'; ','; ' '; 'w'; 'o'; 'r'; 'l'; 'd'; '!']
```

I went with the name `explode_string` as the name `explode` is often used to describe this type of operation (with `implode` being the name of the inverse operation).

Searching for the signature `string -> char list` on [Sherlodoc](https://doc.sherlocode.com/) reveals that many libraries offer some version of the above function.
Perhaps one day we'll have something like `String.to_list` in the standard library.

By the way, it's also pretty easy to define the inverse operation - namely creating a string out of a list of characters:

``` ocaml
let implode_char_list l = String.of_seq (List.to_seq l);;
val implode_char_list : char list -> string = <fun>

implode_char_list (explode_string "hello");;
- : string = "hello"
```

Note that `List.init` was added in OCaml 4.06 and `String.of_seq` was added in OCaml 4.07.

Keep in mind that lists of chars are quite memory inefficient, as each character
takes 4 bytes (64 bits) on a modern 64 bit CPU. When you factor the pointers to
the next element in the list, each character is effectively taking 8 bytes,
which is pretty far from the memory efficiency of a string. The take away is that
you should avoid using them if you're dealing with large data sets.

That's all I have for you today. Keep hacking!
