---
title: OCaml's Standard Library (`Stdlib`)
date: 2025-03-14 11:18 +0200
tags:
- OCaml
- Learning OCaml
---

Every programming language comes with some "batteries" included -
mostly in the form of its standard library. That's typically all
of the functionality that's available out-of-the-box, without the need
to install additional libraries. (although the definition varies from
language to language) Usually standard libraries are pretty similar,
but I think that OCaml's a bit "weird" and slightly surprising in some
regards, so I decided to write down a few thoughts on it and how to
make the best of it.

OCaml's standard library is called `Stdlib` and it's the source of much
"controversy" in the OCaml community. Historically `Stdlib` was focused only the
needs of the OCaml compiler (many people called it "the compiler library" for
that reason) and it was very basic when it comes to the functionality that it
provided.  This is part of the reason why libraries like Jane Street's `Base`
and `Core` (alternatives to `Stdlib`), and `OCaml Containers` (complementary
extensions to `Stdlib`) become so popular in the OCaml community.

From what I gathered, the compiler authors felt it was the responsibility of the
users of the language to find (or create) the right libraries for their
use-cases and preferred to keep the standard library as lean as possible.  I get
their reasoning, but I think this backfired to some extent, as it's not
something that many newcomers to a language would expect. The standard library
was definitely a point of surprise and disappointment for me when playing with
OCaml for the first time. I still remember how surprised I was that the book
[Real World OCaml](https://dev.realworldocaml.org/) began with the instructions
to replace the built-in standard library with the more full-featured `Base` and
`Core` libraries. I was used to fairly minimal standard library from my time
with Clojure, but OCaml really outdid Clojure in this regard!

These days, however, I've noticed an increased focus on aligning the `Stdlib`
functionality with the expectations of most programmers. That's obvious when you
check the recent OCaml releases, that feature many additions to it:

- [OCaml 4.14](https://ocaml.org/releases/4.14.0#standard-library): 40+ functions added to `Seq` alone.
- [OCaml 5.1](https://ocaml.org/releases/5.1.0#standard-library): 57 new standard library functions.
- [OCaml 5.2](https://ocaml.org/releases/5.2.0#standard-library): Around 20 new functions added to the standard library.
- [OCaml 5.3](https://ocaml.org/releases/5.3.0#standard-library): Around 20 new functions in the standard library (in the `Domain`, `Dynarray`, `Format`, `List`, `Queue`, `Sys`, and `Uchar` modules).

I've written about some of those recent additions in the past - e.g. [`List.take` and
`List.drop`]({% post_url 2024-02-23-ocaml-adds-list-take-and-list-drop %}) and I
think they'll be quite helpful for newcomers to the language.

Looking at many of the discussions extending the `Stdlib` APIs, the goal is
often to emulate what other functional programming languages like Haskell and
Clojure provide in their own standard libraries. Here's one [pull
request](https://github.com/ocaml/ocaml/pull/10583) from OCaml 4.14 that extends
the `Seq` module:

> This PR (co-developed by @c-cube and myself) adds many new functions to the
> module Seq. Our goal is to include most of the functions that one might
> naturally expect to find in this module, and which have been missing until
> now. Many of these functions are analogous to functions that exist in
> Haskell's Prelude and Data.List. Some functions (such as memoize, once,
> of_iterator and to_iterator) have no Haskell equivalent.

I think the trend to extend `Stdlib` started somewhere around [OCaml
4.07](https://ocaml.org/releases/4.07.0) and has accelerated recently.
That probably won't surprise long-term users of OCaml, as the `Stdlib`
module didn't even exist before. I'll come back to this topic later in the article.

## Exploring Stdlib

The [`Stdlib` module](https://ocaml.org/manual/5.3/api/Stdlib.html) is
automatically opened at the beginning of each compilation. All components of
this module can therefore be referred by their short name, without prefixing
them by `Stdlib`.

In particular, it provides the basic operations over the built-in types
(numbers, booleans, byte sequences, strings, exceptions, references, lists,
arrays, input-output channels, ...) and the standard library modules.
In OCaml 5.3 `Stdlib` consists of the following modules:

- `Arg`: parsing of command line arguments
- `Array`: array operations
- `ArrayLabels`: array operations (with labels)
- `Atomic`: atomic references
- `Bigarray`: large, multi-dimensional, numerical arrays
- `Bool`: boolean values
- `Buffer`: extensible buffers
- `Bytes`: byte sequences
- `BytesLabels`: byte sequences (with labels)
- `Callback`: registering OCaml values with the C runtime
- `Char`: character operations
- `Complex`: complex numbers
- `Condition`: condition variables to synchronize between threads
- `Domain`: Domain spawn/join and domain local variables
- `Digest`: MD5 message digest
- `Dynarray`: Dynamic arrays
- `Effect`: deep and shallow effect handlers
- `Either`: either values
- `Ephemeron`: Ephemerons and weak hash table
- `Filename`: operations on file names
- `Float`: floating-point numbers
- `Format`: pretty printing
- `Fun`: function values
- `Gc`: memory management control and statistics; finalized values
- `Hashtbl`: hash tables and hash functions
- `In_channel`: input channels
- `Int`: integers
- `Int32`: 32-bit integers
- `Int64`: 64-bit integers
- `Lazy`: deferred computations
- `Lexing`: the run-time library for lexers generated by ocamllex
- `List`: list operations
- `ListLabels`: list operations (with labels)
- `Map`: association tables over ordered types
- `Marshal`: marshaling of data structures
- `MoreLabels`: include modules Hashtbl, Map and Set with labels
- `Mutex`: locks for mutual exclusion
- `Nativeint`: processor-native integers
- `Oo`: object-oriented extension
- `Option`: option values
- `Out_channel`: output channels
- `Parsing`: the run-time library for parsers generated by ocamlyacc
- `Printexc`: facilities for printing exceptions
- `Printf`: formatting printing functions
- `Queue`: first-in first-out queues
- `Random`: pseudo-random number generator (PRNG)
- `Result`: result values
- `Scanf`: formatted input functions
- `Seq`: functional iterators
- `Set`: sets over ordered types
- `Semaphore`: semaphores, another thread synchronization mechanism
- `Stack`: last-in first-out stacks
- `StdLabels`: include modules Array, List and String with labels
- `String`: string operations
- `StringLabels`: string operations (with labels)
- `Sys`: system interface
- `Type`: type introspection
- `Uchar`: Unicode characters
- `Unit`: unit values
- `Weak`: arrays of weak pointers

Lots of good stuff here! Sure, it's not anything like the standard libraries of
languages like `Ruby`, `Python` or `Java`, but you have the basics covered, at
least to some extent.

Note that unlike the core `Stdlib` module, sub-modules are not automatically
“opened” when compilation starts, or when the toplevel system (e.g. `ocaml` or
`utop`) is launched. Hence it is necessary to use qualified identifiers
(e.g. `List.map`) to refer to the functions provided by these modules, or to add
`open` directives.

One thing I found somewhat peculiar at first was the presence of two versions of
some standard library modules - e.g. `List` and `ListLabels`. Both of them have
the same functions, but the `ListLabels` module makes heavy use of labeled
parameters. I'm not sure what's the reasoning behind this, but I'm guessing this
was influenced by the `Base` library, that's using labels everywhere
pervasively. Here are a few examples:

``` ocaml
(* Using List module *)
let squares_list = List.map (fun x -> x * x) [1; 2; 3; 4; 5]
(* Result: [1; 4; 9; 16; 25] *)

(* Using ListLabels module *)
let squares_list_labels = ListLabels.map [1; 2; 3; 4; 5] ~f:(fun x -> x * x)
(* Result: [1; 4; 9; 16; 25] *)

(* Using List module *)
let sum_list = List.fold_left (+) 0 [1; 2; 3; 4; 5]
(* Result: 15 *)

(* Using ListLabels module *)
let sum_list_labels = ListLabels.fold_left [1; 2; 3; 4; 5] ~init:0 ~f:(+)
(* Result: 15 *)
```

The labeled arguments in `ListLabels` make it clear what each parameter means -
e.g. `~init` for the initial value and `~f` for the folding function. I'm not
sure how I feel about labeled arguments in general, as in most cases I don't
think they are really needed, but you've got the option if you want it.

One notable omission from `Stdlib` is some module for dealing with regular
expressions. OCaml bundles the (controversial)
[str](https://ocaml.org/manual/5.3/libstr.html) module, but it's not part of
`Stdlib` and you have to link it to your applications manually:

``` shell
ocamlc other options -I +str str.cma other files
ocamlopt other options  -I +str str.cmxa other files
```

Not to mention that you probably want to use something different instead. (e.g. `re`)

**Note:** The [documentation of
`Stdlib`](https://ocaml.org/manual/5.3/stdlib.html) is excellent and I highly
recommend everyone to peruse it.

## `Base` or `Stdlib`?

A lot of people might be wondering whether to use Jane Street's standard library
`Base` or `Stdlib`?  I'm guessing there was a time when `Base` offered bigger
advantages over `Stdlib`, but today it's harder to recommend `Base` over
`Stdlib`. Especially when you factor in the library [OCaml
Containers](https://github.com/c-cube/ocaml-containers) which provides numerous
extensions to `Stdlib`.

My advice for most newcomers would be to start with `Stdlib` and mix in Containers if
needed. If you deem they are not enough for you - feel free to explore `Base` at this point.

I think `Base` (and `Core`) are excellent and battle-tested libraries, but I still think
it's a good idea for everyone to be familiar with OCaml's "native" standard library. And for
all of us to be pushing to make it better, of course.

## A note about the core library

Sometimes you might hear mentions of OCaml's "core library" (not to be confused
with `Core` by Jane Street) and you might wonder what's that exactly.

Well, the "core library" is composed of declarations for built-in types and
exceptions, plus the module Stdlib that provides basic operations on these
built-in types.

You can learn more about the core library [here](https://ocaml.org/manual/5.3/core.html).

## A note about `Pervasives`

Early on in my OCaml journey I'd find references here and there to a library
named `Pervasives`, that sounded more or less like a standard library.
Turns out that `Pervasives` got renamed to `Stdlib` in OCaml 4.07. Here are a few highlights
from the release notes of this quite important release:

- The standard library is now packed into a module called `Stdlib`, which is
  open by default. This makes it easier to add new modules to the standard
  library without clashing with user-defined modules.
- The `Bigarray` module is now part of the standard library.
- The modules `Seq`, `Float` were added to the standard library.

I know `Pervasives` was kept around for a while for backwards compatibility and it seems it's no
longer present in OCaml 5.x.

## Epilogue

OCaml's `Stdlib` is often cited as a reason why the language is not popular, and
I think that's a valid argument. Still, it seems to me that lately `Stdlib` has
been moving in the right direction, and the out-of-the-box OCaml experience got
improved because of this. I can only hope that this trend will continue and that
as a result OCaml will become more beginner-friendly and more useful out-of-the-box.

What improvements would you like to see there going forward?

That's all I have for you today. Keep hacking!
