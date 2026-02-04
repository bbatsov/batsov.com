---
title: 'Learning OCaml: Parsing Data with Scanf'
date: 2025-04-06 11:40 +0300
tags:
- OCaml
- Learning OCaml
---

In my [previous article]({% post_url 2025-04-04-learning-ocaml-regular-expressions %}) I mentioned that OCaml's
`Stdlib` leaves a lot to be desired when it comes to regular
expressions. One thing I didn't discuss back then was that
the problem is somewhat mitigated by the excellent module
[Scanf](https://ocaml.org/manual/5.3/api/Scanf.html), which makes it easy to parse structured data.

Image that we're dealing with a simple investment portfolio,
where we have multiple records containing:

- Ticker symbol (e.g. `AAPL`)
- Number of shares
- Current share price

Let's assume this portfolio is stored in `.csv` file and each
entry there looks something like `APPL,10,150.50`. While there
are many ways to parse this data, I think `Scanf` is probably
the simplest and most elegant of them:

```ocaml
Scanf.sscanf "AAPL, 10, 150.5" "%[^,], %d, %f" (fun ticker shares price -> (ticker, shares, price));;
- : string * int * float = ("AAPL", 10, 150.5)
```

As you can see we're using a parsing format specifier that's pretty similar to what we'd
normally use with `printf`. `%[^,]` is kind of weird and it means "read string until ,".
We can't use the regular `%s` format specifier here, as it expects space-separated strings.
This, however, will work fine with `%s`:

```ocaml
Scanf.sscanf "John Doe 33" "%s %s %d" (fun name surname age -> (name, surname, age));;
- : string * string * int = ("John", "Doe", 33)
```

Here's a more complete example that parses a few portfolio records and
calculates the value of the portfolio:

```ocaml
(* Example portfolio entries as strings *)
let portfolio_lines = [
  "AAPL,10,178.23";
  "GOOG,5,150.50";
  "MSFT,20,299.01";
]

(* Parse a line into (ticker, shares, price) *)
let parse_line line =
  Scanf.sscanf line "%[^,],%d,%f" (fun ticker shares price ->
    (ticker, shares, price)
  )

(* Compute total value of the portfolio *)
let total_value entries =
  List.fold_left (fun acc (_ticker, shares, price) ->
    acc +. float_of_int shares *. price
  ) 0.0 entries

let () =
  let open Printf in
  let portfolio = List.map parse_line portfolio_lines in
  List.iter (fun (ticker, shares, price) ->
    printf "%s: %d shares at $%.2f\n" ticker shares price
  ) portfolio;
  let total = total_value portfolio in
  printf "Total portfolio value: $%.2f\n" total
```

Not bad, right?

`Scanf` has several functions in it and quite a lot of format specifiers that you can
leverage in various situations.

The formatted input functions can read from any kind of input, including
strings, files, or anything that can return characters. The more general source
of characters is named a formatted input channel (or scanning buffer) and has
type `Scanf.Scanning.in_channel`. The more general formatted input function reads
from any scanning buffer and is named `bscanf`.

Generally speaking, the formatted input functions have 3 arguments:

- the first argument is a source of characters for the input,
- the second argument is a format string that specifies the values to read,
- the third argument is a receiver function that is applied to the values read.

My trivial examples dealt only with input strings, but you can easily leverage
other input sources. Here's an example reading from the standard input:

```ocaml
Scanf.scanf "%s %f\n" (fun name price ->
    Printf.printf "Item: %s, Price: %.2f\n" name price)

(* input -> Table 100.20 *)

(* output -> Item: Table, Price: 100.20 *)
- : unit = ()
```

Enter something like "Chair 20.25" and observe the results.

I'd encourage everyone to get familiar with the module's
documentation for all the nitty-gritty details.

Please, share in the comments how you're using `Scanf` in your OCaml projects and any tips
you might have about making the best of it.

That's all I have for you. Keep hacking!
