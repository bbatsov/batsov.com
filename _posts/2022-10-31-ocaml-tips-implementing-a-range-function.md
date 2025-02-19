---
title: 'OCaml Tips: Implementing a range Function'
date: 2022-10-31 08:45 +0200
tags:
- Tips
- OCaml
---

Lots of programming languages have some built-in range functionality, that's
typically used to generate a list/array of integer numbers. Here are
a couple of examples from Ruby and Clojure:

```ruby
# This is Ruby

(1..10).to_a
# => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

(1..10).filter(&:even?)
# => [2, 4, 6, 8, 10]

# And some related functionality, that doesn't use literal ranges
5.downto(1).to_a # => [5, 4, 3, 2, 1]

10.step(1, -2).to_a # => [10, 8, 6, 4, 2]
```

``` clojure
;; This is Clojure

(range 1 10)
;; => (1 2 3 4 5 6 7 8 9)

(range 1 10 2)
;; => (1 3 5 7 9)

(range 100 0 -10)
;; (100 90 80 70 60 50 40 30 20 10)
```

Ruby has a special syntax for ranges (`..` and `...`) and Clojure provides
a `range` function to generate ranges (basically a sequences of integer numbers).
I'm pretty sure you've seen something like this. Not the most useful function in
the world for sure, but it's handy from time to time.

There are many ways we can implement something similar in OCaml, with the
simplest one probably being to use `List.init` internally:

``` ocaml
(* simplest/shortest possible version *)
let range i = List.init i succ

range 10
- : int list = [1; 2; 3; 4; 5; 6; 7; 8; 9; 10]

(* a version with some boundaries *)
let range from until =
  List.init (until - from) (fun i -> i + from)

range 1 10
- : int list = [1; 2; 3; 4; 5; 6; 7; 8; 9]

range 5 15
- : int list = [5; 6; 7; 8; 9; 10; 11; 12; 13; 14]

(* let's name this -- for good measure *)
let (--) = range

1 -- 10
- : int list = [1; 2; 3; 4; 5; 6; 7; 8; 9]

(* you can also consider using the names --> and --< for inclusive and exclusive ranges *)
```

The above implementations are super basic and have a few quirks (e.g. the second
implementation doesn't allow setting the step and it can't handle descending
ranges), but they mostly get the job done. A more full-featured implementation
would look something like this:

``` ocaml
let range ?(from=1) ?(step=1) until =
  let cmp = match step with
    | i when i < 0 -> (>)
    | i when i > 0 -> (<)
    | _ -> raise (Invalid_argument "step cannot be 0")
  in
  Seq.unfold (function
        | i when cmp i until -> Some (i, i + step)
        | _ -> None
    ) from
```

This function has a few advantages over the implementations so far:

- It uses optional labeled arguments (`from` and `step`)
- It allows us to set the step explicitly
- It handles descending ranges
- It's implemented in terms of `Seq`, meaning it's lazy

**Note:** The order of the arguments in the definition matters, as optional
arguments are only filled in once a positional argument after them has been
applied. If `?step` is the last argument that can never happen.

And here's how using it in practice looks:

``` ocaml
range 10 |> List.of_seq;;
- : int list = [1; 2; 3; 4; 5; 6; 7; 8; 9]

range ~from:10 20 |> List.of_seq;;
- : int list = [10; 11; 12; 13; 14; 15; 16; 17; 18; 19]

range 100 ~step:10 |> List.of_seq;;
- : int list = [1; 11; 21; 31; 41; 51; 61; 71; 81; 91]

range ~from:5 20 ~step:5 |> List.of_seq;;
- : int list = [5; 10; 15]

range ~from:20 5 ~step:(-5) |> List.of_seq;;
- : int list = [20; 15; 10]
```

Now we're talking!

One funny thing to note is that originally I wanted to use `from` and `to` as
the parameter names, but I couldn't use `to` as it's a keyword is OCaml:

``` ocaml
for variable = start_value to end_value do
  expression
done
```

I keep forgetting about this, as I never use those `for` loops and Clojure has
spoiled me with its extremely small set of keywords.

Another funny bit worth sharing, as that one of the test cases for `Seq.unfold` is
exactly a trivial implementation of `range`:

``` ocaml
(* unfold *)
let () =
  let range first last =
    let step i = if i > last then None
                 else Some (i, succ i) in
    Seq.unfold step first
  in
  begin
    assert ([1;2;3] = !!(range 1 3));
    assert ([] = !!(range 1 0));
  end
;;
```

A [quick
search](https://doc.sherlocode.com/?q=int%20-%3E%20int%20-%3E%20int%20list) on
Sherlodoc reveals a ton of similar functions in many OCaml libraries, so clearly
there's some use for a `range` function in one form or another.

That's all I have for you today. Keep hacking!
