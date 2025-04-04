---
title: 'Learning OCaml: Regular Expressions'
date: 2025-04-04 14:22 +0300
tags:
- OCaml
- Learning OCaml
- Regular Expressions
---

One of the things that bothered me initially in OCaml was the poor support for
working in regular expressions in the [standard library]({% post_url 2025-03-14-ocaml-s-standard-library %}).
Technically speaking, there's no support for them at all!

What do I mean by this? Well, there's the older [Str](https://ocaml.org/manual/5.3/api/Str.html) library that provides support for regular expressions, but it's:

- not really a part of the standard library (it's bundled with OCaml, but not part of `Stdlib`)
- it doesn't work with unicode characters, as it treats strings as sequences of bytes
- very confusingly named (when I see something named `Str` I'm thinking of strings)

**Note:** Use `#require "str";;` in the top-level to load `Str`.

Here's a trivial example using it:

``` ocaml
let text = "hello123world" in
let re = Str.regexp "[0-9]+" in
if Str.string_match re text 0 then
  Printf.printf "Matched: %s\n" (matched_string text)
else
  Printf.printf "No match\n";;

Str.global_replace (Str.regexp "[0-9]+") "#" "hello123world456";;
- : string = "hello#world#"

let re = Str.regexp {|hello \([A-Za-z]+\)|} in
      Str.replace_first re {|\1|} "hello world"
- : string = "world"

Str.split (Str.regexp "[ \t]+") "hello world";;
- : string list = ["hello"; "world"]
```

I hope the examples are self-explanatory. `Str`'s API is quite similar to what you'd find in most imperative
languages, which is part of the reason the library is frowned upon.

**Tip:** If you find string literals like `{|foo bar|}` strange, please consult [this article]({% post_url 2023-04-20-learning-ocaml-quoted-string-literals %}).

I won't dwell much on `Str` as few people use it these days, especially if they need to do more
complex tasks with regular expressions. Enter the [Re](https://github.com/ocaml/ocaml-re) library.
Before we do something with `Re` we'll need to install it:

``` shell
opam install re
```

One interesting thing about `Re` is that it supports various flavors of regular expressions:

- Perl-style regular expressions (module `Re.Perl`);
- Posix extended regular expressions (module `Re.Posix`);
- Emacs-style regular expressions (module `Re.Emacs`);
- Shell-style file globbing (module `Re.Glob`).

Okay, shell globbing is not exactly regular expressions, and I'm not sure who would want to use Emacs style regular expressions
outside Emacs, but you sure have options! I'm a big fan of Perl's regular expressions, so I'll stick with them going forward.

Now, let's see it in action (I encourage to try the examples below in `utop`):

``` ocaml
#require "re";;

(* basic matching *)
let re = Re.Perl.re "[0-9]+" |> Re.compile in
let text = "hello123world" in
match Re.exec_opt re text with
| Some group -> Printf.printf "Matched: %s\n" (Re.Group.get group 0)
| None -> Printf.printf "No match\n"
;;

(* replace matches *)
let replace_digits str =
  let re = Re.Perl.re "[0-9]+" |> Re.compile in
  Re.replace_string re ~by:"#"
    str
;;

print_endline (replace_digits "hello123world456");;

(* use matching groups *)
let re = Re.Perl.re "(\\w+)-(\\d+)" |> Re.compile in
match Re.exec_opt re "item-42" with
| Some group ->
    let name = Re.Group.get group 1 in
    let number = Re.Group.get group 2 in
    Printf.printf "name: %s, number: %s\n" name number
| None -> print_endline "No match"
;;

(* composable regular expressions *)
let word = Re.rep1 Re.wordc;;
let dash = Re.char '-' ;;
let digits = Re.rep1 Re.digit;;

let re =
  Re.seq [word; dash; digits] |> Re.compile
;;

let input = "hello-123" in
match Re.exec_opt re input with
| Some g -> print_endline ("Matched: " ^ Re.Group.get g 0)
| None -> print_endline "No match"
;;

(* iterate over all matches *)
let re = Re.Perl.re "\\d+" |> Re.compile;;

let all_matches str =
  Re.all re str
  |> List.iter (fun g -> Printf.printf "Match: %s\n" (Re.Group.get g 0))
;;

all_matches "a1 b22 c333";;
```

I hope it's clear that `Re` allows you to program in a more functional way.
I've barely scratched the surface here, as the library has pretty big API,
that everyone serious about it should eventually explore.
Below is a list of its most useful combinators:

| Combinator         | Meaning                      |
|--------------------|------------------------------|
| `Re.char c`        | Match a single char          |
| `Re.string s`      | Match exact string           |
| `Re.alt [r1; r2]`  | Alternation (r1 \| r2)       |
| `Re.seq [r1; r2]`  | Concatenation (r1 r2)        |
| `Re.rep r`         | Zero or more (`r*`)          |
| `Re.rep1 r`        | One or more (`r+`)           |
| `Re.opt r`         | Optional (`r?`)              |
| `Re.group r`       | Capture group                |
| `Re.compile`       | Compile the regex            |

And here's a brief comparison of `Str` vs `Re`:

| Feature            | `Str` (legacy)                        | `Re` (modern)                                     |
|--------------------|---------------------------------------|---------------------------------------------------|
| Availability       | Built-in (kind of)                    | External library (`re` package)                   |
| API Style          | Imperative, stateful                  | Functional, composable                            |
| Regex Flavor       | POSIX-like                            | Multiple backends (`Perl`, `Str`, `Emacs`, etc.)  |
| Unicode support    | Poor                                  | Better (though OCaml string handling is limited)  |
| Match iteration    | Awkward (`search_forward` loop)       | Elegant (`Re.all`, `Re.iter`)                     |
| Replacement        | String only                           | Function or string                                |
| Error messages     | Vague                                 | Clear, structured                                 |
| Composability      | Poor (regexp strings only)            | Excellent (regex combinators like `seq`, `alt`)   |

To sum it up:

- Use `Str` only if you want zero dependencies and can tolerate legacy, clunky APIs.
- Use `Re` if you care about code clarity, safety, composability, and are okay with pulling in an external dependency (which you should be in 2025).

That article sat in my backlog for quite a while, as regular expressions were
one of the most frustrating aspects for me when I started to play with OCaml
(Perl and Ruby had really spoiled me on that front), but eventually I kind of
got used to them, so I no longer felt much need to write the article. Still,
I hope some newcomers to OCaml will find it userful!

That's all I have for you today. Keep hacking!
