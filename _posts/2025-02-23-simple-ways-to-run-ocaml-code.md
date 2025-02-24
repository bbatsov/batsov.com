---
title: Simple Ways to Run OCaml Code
date: 2025-02-23 20:49 +0200
tags:
- OCaml
---

When people think of OCaml they are usually thinking of compiling code to a
binary before they are able to run it. While most OCaml code is indeed compiled
to binaries, you don't really need to do this, especially while you're learning
the language and are mostly playing with small exercises.

Imagine you have something like this in a file named `hello.ml`:

```ocaml
let () = print_endline "Hello, world!"
```

You can compile this if you want, but you can also run it directly with OCaml's
interpreter `ocaml`:

```console
$ ocaml hello.ml 
Hello, world!
```

This approach should be familiar to anyone who has ever used a scripting
language like Perl, Python, Ruby or JavaScript. You can do the same with `utop`
as well:

```console
$ utop hello.ml 
Hello, world!
```

Of course, one can argue that it's just as simple to start `utop` and then
do `#use "hello.ml"` from it. Feel free to do whatever works best for you.

You can take things one step further like this:

```ocaml
#!/usr/bin/env ocaml
let () = print_endline "Hello, world!"
```

Now you can make this file an executable script and run it directly:

```console
$ chmod +x hello.ml
$ ./hello.ml
Hello, world!
```

While this approach should be used mostly when dealing with code that doesn't
use external libraries, there's nothing preventing you from doing so:

```ocaml
#use "topfind";;
#require "package_name";;
(* Your OCaml code here *)
```

This should be familiar to everyone who has required any packages in `utop`.
`topfind` is a file that [ocamlfind](https://github.com/ocaml/ocamlfind) installs
in the standard library, so that it can be used from the toplevel.
Don't forget to install `ocamlfind` first:

```shell
opam install ocamlfind
```

One last thing before we wrap up - you might be wondering about the use
of `let ()` in the simple examples I've provided. Technically that's not
needed, but you have to keep in mind that when you have multiple expressions in
the source files with boundaries that the compiler can't infer you'll need to
separate those with `;;`. Using `let` for everything eliminates the need for this:

```ocaml
#!/usr/bin/env ocaml
let () = print_endline "Hello, world!"

let () = print_endline "Bye, world!"
```

The example above works. The one below, however, doesn't:

```ocaml
#!/usr/bin/env ocaml
print_endline "Hello, world!"

print_endline "Bye, world!"
```

It will result in a syntax error, because to OCaml this code is basically one expression.
To fix this will need to add `;;` to help the compiler:

```ocaml
#!/usr/bin/env ocaml
print_endline "Hello, world!";;

print_endline "Bye, world!";;
```

If you know that you can also use `;` to separate expressions that are
evaluation only for their side effects (like `print_endline`) you might be
tempted to write instead the following:

```ocaml
#!/usr/bin/env ocaml
print_endline "Hello, world!";
print_endline "Bye, world!";;
```

I'm not a big fan of this at the top-level, though, as it's intended to be
used mostly in bindings:

```ocaml
let a = 1 in
let b = 2 in
print_int a;
print_int b;
a + b
```

If you have any other tips on running simple OCaml programs, please
share those in the comments.

That's all I have for you today. Keep hacking!
