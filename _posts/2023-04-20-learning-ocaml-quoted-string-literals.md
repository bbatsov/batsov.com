---
title: 'Learning OCaml: Quoted String Literals'
date: 2023-04-20 16:23 +0300
tags:
- OCaml
- Learning OCaml
---

While learning OCaml I've noticed one curious feature - it has two
types of string literals. The first type are the common and quite
familiar "double-quoted string literals" (or perhaps simply "string literals"?):

``` ocaml
let greeting = "Hello, World!\n"
let superscript_plus = "\u{207A}";;
val greeting : string = "Hello, World!\n"
val superscript_plus : string = "⁺"
```

Nothing really surprising here, right? But then there are also what OCaml calls
[quoted string
literals](https://v2.ocaml.org/manual/lex.html#sss:stringliterals) (a.k.a. "quoted strings"):

``` ocaml
let quoted_greeting = {|"Hello, World!"|}
let nested = {ext|hello {|world|}|ext};;
val quoted_greeting : string = "\"Hello, World!\""
val nested : string = "hello {|world|}"
```

Now that's something you don't see every day, right? Here's how the official
manual describes them:

> Quoted string literals provide an alternative lexical syntax for string
> literals. They are useful to represent strings of arbitrary content without
> escaping. Quoted strings are delimited by a matching pair of
> `{ quoted-string-id |` and `| quoted-string-id }` with the same `quoted-string-id` on
> both sides. Quoted strings do not interpret any character in a special way but
> requires that the sequence `| quoted-string-id }` does not occur in the string
> itself. The identifier `quoted-string-id` is a (possibly empty) sequence of
> lowercase letters and underscores that can be freely chosen to avoid such
> issue.

Note that all special escape sequences are ignored in quoted strings:

``` ocaml
(* regular strings *)
let greeting = "Hello, World!\n"
let superscript_plus = "\u{207A}";;
val greeting : string = "Hello, World!\n"
val superscript_plus : string = "⁺"

(* quoted strings *)
let greeting = {|Hello, World!\n|}
let superscript_plus = {|\u{207A}|};;
val greeting : string = "Hello, World!\\n"
val superscript_plus : string = "\\u{207A}"
```

In this way you can say they are pretty similar to single-quoted strings in
languages like Perl and Ruby (think `'string'`).

[Real World OCaml](https://dev.realworldocaml.org) has a [nice
example](https://dev.realworldocaml.org/testing.html#scrollNav-2-3) that
illustrates the usefulness of quoted strings:

``` ocaml
let%expect_test _ =
  let example_html =
    {|
    <html>
      Some random <b>text</b> with a
      <a href="http://ocaml.org/base">link</a>.
      And here's another
      <a href="http://github.com/ocaml/dune">link</a>.
      And here is <a>link</a> with no href.
    </html>|}
  in
  let soup = Soup.parse example_html in
  let hrefs = get_href_hosts soup in
  print_s [%sexp (hrefs : Set.M(String).t)]
```

As you can see one good use-case for quoted strings is embedding snippets of
code, as those typically tend to have lots of things that would normally need to be
escaped.
And because the delimiters are so flexible you don't really have to worry about
content that uses `{| |}` internally - after all you can easily change this to
whatever you want.

One more thing. Quoted strings `{|...|}` can be combined with [extension
nodes](https://v2.ocaml.org/manual/extensionnodes.html#s:extension-nodes) to
embed foreign syntax fragments. Those fragments can be interpreted by a
preprocessor and turned into OCaml code without requiring escaping quotes. A
syntax shortcut is available for them:

``` ocaml
{%%foo|...|}               === [%%foo{|...|}]
let x = {%foo|...|}        === let x = [%foo{|...|}]
let y = {%foo bar|...|bar} === let y = [%foo{bar|...|bar}]
```

For instance, you can use `{%sql|...|}` to represent arbitrary SQL statements –
assuming you have a `ppx`-rewriter that recognizes the `%sql` extension.

I have to say I think it's a bit funny that OCaml's quoted strings are called
"quoted strings". It's not like double-quoted strings (think `"string"`) are
unquoted, right? Pretty sure this name doesn't help with the discoverability of
this useful feature. Oh, well - naming is hard!

That's all I have for you today. Please, share in the comments whether you use
quoted strings and what are some of your favorite use-cases for them. Keep
hacking!
