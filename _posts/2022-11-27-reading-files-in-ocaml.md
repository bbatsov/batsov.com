---
title: Reading Files in OCaml
date: 2022-11-27 09:52 +0200
tags:
- OCaml
---

One thing I've noticed on my [journey to learn OCaml]({% post_url
2022-08-19-learning-ocaml %}) was that reading (text) files wasn't as
straightforward as with many other programming languages. To give you some point
of reference - here's how easy it is to do this in Ruby:

``` ruby
# read entire file to string
content = File.read(filename)

# read lines into an array of lines
lines = File.readlines(filename)

# process lines one at a time (memory efficient when dealing with large files)
File.foreach(filename) { |line| puts line }
```

In my beloved Clojure the situation is similar:

``` clojure
;; read entire file into string
(slurp filename)

;; process lines one at a time
(use 'clojure.java.io)

(with-open [rdr (reader filename)]
  (doseq [line (line-seq rdr)]
    (println line)))
```

Basically there are three common operations when
dealing with text files:

- reading the whole contents as a single string
- reading the whole contents of a collection of lines (often that's just a slight variation of the previous operation)
- reading and processing lines one by one (useful when dealing with large files)

When I had to play with files in OCaml for the first time I did some digging
around and I noticed that many people were either rolling out their own
`read_lines` function based on the built-in `input_line` function or using Jane
Street's `Base` library.

``` ocaml
(* Using Base *)
open Core.Std
let contents = In_channel.read_all file
let lines = In_channel.read_lines file

(* homemade read_lines that gathers all lines in a list *)
let read_lines name : string list =
  let ic = open_in name in
  let try_read () =
    try Some (input_line ic) with End_of_file -> None in
  let rec loop acc = match try_read () with
    | Some s -> loop (s :: acc)
    | None -> close_in ic; List.rev acc in
  loop []

let lines = read_lines filename

(* homemade read_lines that processes each line *)
let read_lines file process =
  let in_ch = open_in file in
  let rec read_line () =
    let line = try input_line in_ch with End_of_file -> exit 0
    in (* process line in this block, then read the next line *)
       process line;
       read_line ();
in read_line ()

read_lines filename print_endline
```

Obviously, this gets the job done, but I was quite surprised such basic
operations are not covered in the standard library. Turns out, however, that the
situation has changed recently with OCaml 4.14 with the introduction of the
module `In_channel`:[^1]

``` ocaml
(* read the entire file *)
let read_file file =
  In_channel.with_open_bin file In_channel.input_all

(* read lines *)
let read_lines file =
  let contents = In_channel.with_open_bin file In_channel.input_all in
  String.split_on_char '\n' contents

List.iter print_endline (read_lines filename)
```

While you still need to roll out your own `read_file` and `read_lines`
functions, the implementation is significantly simpler than before. Even more
importantly, the code is now more reliable as noted by Daniel Bünzli:[^2]

> Be careful, `input_line` is a footgun and has led to more than one bug out there – along with `open_in` and `open_out` defaulting to text mode and thus lying by default about your data.
>
> `input_line` will never report an empty final line and performs newline translations if your channel is in text mode. This means you can’t expect to recover the exact file contents you just read by doing `String.concat "\n"` on the lines you input with `input_line`.
>
> Also of course it doesn’t help with making sure you correctly close your channels and don’t leak them in case of exception. The new functions finally make that a no brainer.

You can also use `In_channel.input_line` to read file contents line by line and
avoid excessive memory allocation. I'm still missing something like Clojure's
`line-seq` that create a lazy seq from which you can obtain the file lines, but
I guess this should be doable in OCaml one way or another.[^3]

One interesting library that I've discovered was [Iter](https://github.com/c-cube/iter/) and it particular its module [Iter.IO](https://c-cube.github.io/iter/dev/iter/Iter/IO/index.html). It provides a basic interface to manipulate files as iterator of chunks/lines. The iterators take care of opening and closing files properly; every time one iterates over an iterator, the file is opened/closed again. Here's are a few examples from the library's documentation:

``` ocaml
(* Example: copy a file "a" into file "b", removing blank lines: *)
Iterator.(IO.lines_of "a" |> filter (fun l -> l <> "") |> IO.write_lines "b")

(* By chunks of 4096 bytes: *)
Iterator.IO.(chunks_of ~size:4096 "a" |> write_to "b")

(* Read the lines of a file into a list: *)
Iterator.IO.lines "a" |> Iterator.to_list
```

Cool stuff! I'll make sure to explore further at some point.

Perhaps the takeaway for you today is to use libraries like `Base` and
`Containers` instead of relying solely on the standard library, perhaps it's
not. I'll leave that for you to decide. If you decide to stick with the standard
library - I encourage you to peruse the [documentation of
`In_channel`](https://v2.ocaml.org/api/In_channel.html) to learn more about the
functions it offers and the advantages of using it over the legacy `input_line`
function.

I really wish that someone would update [OCaml's page on file
manipulation](https://ocaml.org/docs/file-manipulation) to include coverage of
the OCaml 4.14 functionality (perhaps I'll do this myself).  I'm guessing this
outdated page and other legacy docs are sending a lot of people in the wrong
direction, which was the main reason I've decided to write this article.

That's all I have for you today. Keep hacking!

[^1]: <https://discuss.ocaml.org/t/ocaml-compiler-development-newsletter-issue-3-june-september-2021/8598#channels-in-the-standard-library-2>
[^2]: <https://discuss.ocaml.org/t/how-do-you-read-the-lines-of-a-text-file/8834/8>
[^3]: After writing the article I've noticed that there's a `read_lines_seq` function in the [Containers](https://github.com/c-cube/ocaml-containers/blob/master/src/core/CCIO.mli) library.
