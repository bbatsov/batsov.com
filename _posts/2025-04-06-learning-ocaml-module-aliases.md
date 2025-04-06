---
title: 'Learning OCaml: Module Aliases'
date: 2025-04-06 22:06 +0300
tags:
- OCaml
- Learning OCaml
---

OCaml is famous for allow you to do a lot of things like modules. Like really a lot!
Advanced features like functors, aside, it's really common to either alias
module names to something shorter or localize `open Module_name` to a smaller
scope:

```ocaml
(* module alias *)
module Printf = P

(* open module for subsequent scope *)
let open Printf in
let portfolio = List.map parse_line portfolio_lines in
List.iter (fun (ticker, shares, price) ->
  printf "%s: %d shares at $%.2f\n" ticker shares price
) portfolio;
let total = total_value portfolio in
printf "Total portfolio value: $%.2f\n" total

(* open module for an expression *)
List.(map (fun x -> x * 2) [1; 2; 3; 4; 5] |> fold_left (+) 0);;
```

All of them have their uses, but I'd like to also mention one less known
approach - namely a scoped module alias:

```ocaml
let module P = Printf in
let portfolio = List.map parse_line portfolio_lines in
List.iter (fun (ticker, shares, price) ->
  P.printf "%s: %d shares at $%.2f\n" ticker shares price
) portfolio;
let total = total_value portfolio in
P.printf "Total portfolio value: $%.2f\n" total
```

I think in some way that's the best of both worlds as it makes it obvious
that certain functions are coming from a module, and you're still not
doing that much extra typing. Finding the right balance between conciseness,
readability and maintainability is never easy, though.

**Note:** Interestingly OCaml's younger sibling F# chose not to implement
scoped module opens at all. I'm guessing this happened due to maintainability
concerns.

What are your thoughts on the subject? In which situations would you prefer
`let open Module in` over a local module alias and vice versa?

That's all I have for you today. Keep hacking!
