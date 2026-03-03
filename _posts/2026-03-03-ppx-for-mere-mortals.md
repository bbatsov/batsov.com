---
title: 'Learning OCaml: PPX for Mere Mortals'
date: 2026-03-03 10:00 +0200
tags:
- OCaml
- Learning OCaml
- Meta-programming
---

When I started learning OCaml I kept running into code like this:

```ocaml
type person = {
  name : string;
  age  : int;
} [@@deriving show, eq]
```

My first reaction was "what the hell is `[@@deriving show, eq]`?" Coming from
languages like Ruby and Clojure, where metaprogramming is either built into the
runtime (reflection) or baked into the language itself (macros),
OCaml's approach felt alien. There's no runtime reflection, no macro system in the
Lisp sense -- just this mysterious `@@` syntax that somehow generates code at
compile time.

That mystery is **PPX** (PreProcessor eXtensions), and once you understand it,
a huge chunk of the OCaml ecosystem suddenly makes a lot more sense. This article
is my attempt to demystify PPX for people like me -- developers who want to
*use* PPX effectively without necessarily becoming PPX authors themselves.

<!--more-->

## What Is PPX and Why Should You Care?

OCaml is a statically typed language with no runtime reflection. That means you
can't do things like "iterate over all fields of a record at runtime" or
"automatically serialize any type to JSON." The type information simply isn't
available at runtime -- it's erased during compilation. One of my biggest
frustrations as a newcomer was not being able to just print arbitrary data for
debugging -- there's no generic `print` or `inspect` that works on any type.
That frustration was probably my first real interaction with PPX.

PPX solves this by generating code at **compile time**. When the OCaml compiler
parses your source code, it builds an Abstract Syntax Tree (AST) -- a tree data
structure that represents the syntactic structure of your program. PPX rewriters
are programs that receive this AST, transform it, and return a modified AST back
to the compiler. The compiler then continues as if you had written the generated
code by hand.

In practical terms, this means that when you write:

```ocaml
type color = Red | Green | Blue
[@@deriving show]
```

The PPX rewriter generates something like this behind the scenes:

```ocaml
type color = Red | Green | Blue

let pp_color fmt = function    (* uses OCaml's Format module for pretty-printing *)
  | Red -> Format.fprintf fmt "Red"
  | Green -> Format.fprintf fmt "Green"
  | Blue -> Format.fprintf fmt "Blue"

let show_color x = Format.asprintf "%a" pp_color x  (* returns the formatted string *)
```

You get a pretty-printer for free, derived from the type definition. No
boilerplate, no manual work, and it stays in sync with your type automatically.

If you've used Rust's `#[derive(Debug, PartialEq)]` or Haskell's `deriving
(Show, Eq)`, the idea is very similar. The syntax is different, but the
motivation is identical -- generating repetitive code from type definitions.

## Why Isn't This Built into the Language?

If you're coming from Rust, you might wonder why OCaml doesn't just have a
built-in macro system like `proc_macro`. It's a fair question, and the answer
says a lot about OCaml's design philosophy.

OCaml has always favored a **small, stable language core**. The compiler is
famously lean and fast, and the language team is conservative about adding
complexity to the specification. A full macro system baked into the compiler
would be a significant undertaking -- it would need to be designed, specified,
maintained, and kept compatible across versions, forever.

Instead, OCaml took a more minimal approach: the compiler provides just two
things -- **extension points** and **attributes** -- as syntactic hooks in the
AST. Everything else lives in the ecosystem. The actual PPX rewriters are
ordinary OCaml programs that happen to transform ASTs. The [ppxlib](https://github.com/ocaml-ppx/ppxlib)
framework that ties it all together is a regular library, not part of the
compiler.

This has some real advantages:

- **The ecosystem can evolve independently.** ppxlib can ship new features,
  fix bugs, and improve APIs without waiting for a compiler release. Compare
  this to Rust, where changes to the proc macro system require the full RFC
  process and a compiler update.
- **Tooling stays simple.** Because `[@@deriving show]` and `[%blob "file"]`
  are valid OCaml syntax, every tool -- editors, formatters, documentation
  generators -- can parse PPX-annotated code without knowing anything about
  the specific PPX. The code is always syntactically valid OCaml, even before
  preprocessing.
- **The compiler stays lean.** No macro expander, no hygiene system, no
  special compilation phases -- just a hook that says "here, transform this
  AST before I type-check it."

The trade-offs are real, though. Rust's proc macros are more tightly
integrated -- you get better error messages pointing at macro-generated code,
better IDE support for macro expansions, and the macro system is a documented,
stable part of the language. With PPX, you're sometimes left staring at
cryptic type errors in generated code and reaching for `dune describe pp` to
figure out what went wrong.

That said, OCaml's approach feels very OCaml -- pragmatic, minimal, and
trusting the ecosystem to build what's needed on top of a simple foundation.
And in practice, it works remarkably well.

## A Bit of History

PPX wasn't OCaml's first metaprogramming system. Before PPX, there was
**Camlp4** (and its fork **Camlp5**) -- a powerful but complex preprocessor that
maintained its own parser, separate from the compiler's parser. Camlp4 could
extend OCaml's syntax in arbitrary ways, which sounds great in theory but was a
maintenance nightmare in practice. Every OCaml release risked breaking Camlp4,
and code using Camlp4 extensions often couldn't be processed by standard tools
like editors and documentation generators.

OCaml 4.02 (2014) introduced **extension points** and **attributes** directly
into the language grammar -- syntactic hooks specifically designed for
preprocessor extensions. This was a much simpler and more maintainable approach:
PPX rewriters use the compiler's own AST, the syntax is valid OCaml (so tools
can still parse your code), and the whole thing is conceptually just "AST in,
AST out."

Camlp4 was officially retired in 2019. Today, the PPX ecosystem is built on
[ppxlib](https://github.com/ocaml-ppx/ppxlib), a unified framework that
provides a stable API across OCaml versions and handles all the plumbing for PPX
authors.

## Understanding the Syntax

Before diving into specific libraries, let's decode the bracket soup. PPX uses two
syntactic mechanisms built into OCaml:

### Extension Nodes

Extension nodes are placeholders that a PPX rewriter **must** replace with
generated code (compilation fails if no PPX handles them):

```ocaml
(* Expression-level extension *)
let pos = [%here]            (* replaced with current source position *)
let data = [%blob "icon.png"] (* replaced with file contents as a string *)

(* The "let%" infix syntax is sugar for expression extensions *)
let%bind x = fetch () in     (* equivalent to [%bind let x = fetch () in ...] *)
process x
```

### Attributes

Attributes attach metadata to existing code. Unlike extension nodes, the
compiler silently ignores attributes that no PPX handles:

```ocaml
(* [@attr] -- attaches to the nearest expression/pattern/type *)
x + y [@warning "-8"]

(* [@@attr] -- attaches to the enclosing declaration *)
type t = { name : string; age : int }
[@@deriving show]

(* [@@@attr] -- floating attribute at module level *)
[@@@warning "-32"]
```

The one you'll see most often is `[@@deriving ...]` on type declarations. The
distinction between `@`, `@@`, and `@@@` is about scope -- one `@` for the
innermost node, two for the enclosing declaration, three for the whole
module-level.

**Tip:** Don't worry about memorizing all of this upfront. In practice, you'll
mostly use `[@@deriving ...]` and occasionally `[%extension ...]` or `let%...` --
and the specific PPX library's documentation will tell you exactly which syntax
to use.

## Using PPX with Dune

To use a PPX library in your project, you add it to the `preprocess` stanza in
your `dune` file:

```dune
(library
 (name mylib)
 (libraries yojson)
 (preprocess (pps ppx_deriving.show ppx_deriving.eq ppx_deriving_yojson)))
```

That's it. List all the PPX rewriters you need after `pps`, and Dune takes
care of the rest (it even combines them into a single binary for performance). For
`ppx_deriving` plugins specifically, you use dotted names like `ppx_deriving.show`.

## The PPX Libraries You'll Actually Use

Let's look at the PPX libraries that cover probably 90% of real-world use cases.

### ppx_deriving -- The Swiss Army Knife

[ppx_deriving](https://github.com/ocaml-ppx/ppx_deriving) is the community's
general-purpose deriving framework. It comes with several built-in plugins:

```ocaml
type point = { x : float; y : float }
[@@deriving show, eq, ord]

(* Generates:
   val pp_point : Format.formatter -> point -> unit
   val show_point : point -> string
   val equal_point : point -> point -> bool
   val compare_point : point -> point -> int *)
```

`show` is the one you'll reach for first -- it's essentially the answer to
"how do I just print this thing?" that every OCaml newcomer asks sooner or
later. The most commonly used plugins:

| Plugin | What it generates |
|--------|-------------------|
| `show` | `pp_t` and `show_t` -- pretty-printing |
| `eq` | `equal_t` -- structural equality |
| `ord` | `compare_t` -- total ordering |
| `make` | Smart constructors with optional/default args |

A neat convention: if your type is named `t` (as is idiomatic in OCaml), the
generated functions drop the type name suffix -- you get `pp`, `show`, `equal`,
`compare` instead of `pp_t`, `show_t`, etc.

You can also customize behavior per field with attributes:

```ocaml
type file = {
  name : string [@equal fun a b ->
    String.lowercase_ascii a = String.lowercase_ascii b];
  perm : int    [@compare fun a b -> compare b a]  (* reverse order *)
} [@@deriving eq, ord]
```

And you can derive for anonymous types inline:

```ocaml
(* Sort a list of pairs by their second element *)
let sorted = List.sort [%derive.ord: int * int] pairs

(* Print a list of pairs *)
let () = print_endline ([%derive.show: (string * int) list] data)
```

### ppx_deriving_yojson -- JSON Serialization

[ppx_deriving_yojson](https://github.com/ocaml-ppx/ppx_deriving_yojson)
generates JSON serialization and deserialization functions using the
[Yojson](https://github.com/ocaml-community/yojson) library:

```ocaml
type person = {
  name : string;
  age  : int;
  email : string option;
} [@@deriving yojson]

(* Generates:
   val person_to_yojson : person -> Yojson.Safe.t
   val person_of_yojson : Yojson.Safe.t -> (person, string) result *)
```

```ocaml
let alice = { name = "Alice"; age = 30; email = Some "alice@example.com" }

let json = person_to_yojson alice |> Yojson.Safe.to_string
(* {"name":"Alice","age":30,"email":"alice@example.com"} *)
```

You can use `[@@deriving to_yojson]` or `[@@deriving of_yojson]` if you only
need one direction.

This is incredibly useful in practice -- writing JSON
serializers by hand for complex types is tedious and error-prone.

### ppx_sexp_conv -- S-expression Serialization

If you're using Jane Street's [Core](https://github.com/janestreet/core)
library, you'll encounter S-expression serialization everywhere.
(**Tip:** Jane Street bundles most of their PPXs into a single
[ppx_jane](https://github.com/janestreet/ppx_jane) package, so you can add
just `ppx_jane` to your `pps` instead of listing each one individually.)
[ppx_sexp_conv](https://github.com/janestreet/ppx_sexp_conv) generates
converters between OCaml types and S-expressions:

```ocaml
type config = {
  host : string;
  port : int    [@default 8080];
  debug : bool  [@sexp.bool];
} [@@deriving sexp]

(* Generates:
   val sexp_of_config : config -> Sexp.t
   val config_of_sexp : Sexp.t -> config *)
```

The attributes here are quite handy -- `[@default 8080]` provides a default
value during deserialization, and `[@sexp.bool]` means the field is represented
as a present/absent atom rather than `(debug true)`.

### ppx_fields_conv and ppx_variants_conv -- Accessor Generation

Two more Jane Street PPXs that you'll see a lot in Core-based codebases.
[ppx_fields_conv](https://github.com/janestreet/ppx_fields_conv) generates
first-class accessors and iterators for record fields:

```ocaml
type person = {
  name : string;
  age  : int;
} [@@deriving fields]

(* Generates field accessors, a fold over all fields, iter, map, etc. *)
let () = Fields.iter
  ~name:(fun name -> Printf.printf "name: %s\n" name)
  ~age:(fun age -> Printf.printf "age: %d\n" age)
```

[ppx_variants_conv](https://github.com/janestreet/ppx_variants_conv) does
something similar for variant types -- generating constructors as functions,
fold/iter over all variants, and more.

### ppx_inline_test and ppx_expect -- Testing

These Jane Street PPXs let you write tests directly in your source files:

```ocaml
(* Simple boolean test *)
let%test "addition works" = 1 + 1 = 2

(* Test with side effects *)
let%test_unit "list operations" =
  let l = [1; 2; 3] in
  assert (List.length l = 3);
  assert (List.hd l = 1)
```

[ppx_expect](https://github.com/janestreet/ppx_expect) is particularly nice --
it captures printed output and compares it against expected output:

```ocaml
let%expect_test "printing" =
  List.iter (fun x -> Printf.printf "%d " x) [1; 2; 3];
  [%expect {| 1 2 3 |}]
```

If the output doesn't match, the test fails and you can run `dune promote` to
automatically update the expected output in your source file. It's a very
productive workflow for testing functions that produce output.

### ppx_let -- Monadic Syntax

[ppx_let](https://github.com/janestreet/ppx_let) provides syntactic sugar for
working with monads and other "container" types:

```ocaml
(* Without ppx_let -- callback nesting hell *)
let result =
  bind (fetch_user id) (fun user ->
    bind (fetch_posts user) (fun posts ->
      return (user, posts)))

(* With ppx_let -- reads like sequential code *)
let result =
  let%bind user = fetch_user id in
  let%bind posts = fetch_posts user in
  return (user, posts)
```

How does `ppx_let` know which `bind` to call? It looks for a `Let_syntax`
module in scope that provides the underlying `bind` and `map` functions. In
practice, you'll typically open a module that defines `Let_syntax` before
using `let%bind`:

```ocaml
open Lwt.Syntax  (* or Deferred.Let_syntax, etc. *)
```

**Note:** Since OCaml 4.08, the language has built-in
[binding operators](https://v2.ocaml.org/manual/bindingops.html)
(`let*`, `and*`, `let+`, `and+`) that cover the basic use cases of `ppx_let`
without needing a preprocessor. If you're not using Jane Street's ecosystem,
binding operators are probably the simpler choice. `ppx_let` still offers extra
features like `match%bind`, `if%bind`, and optimized `let%mapn` though.

### ppx_blob -- Embedding Files

[ppx_blob](https://github.com/johnwhitington/ppx_blob) is beautifully simple --
it embeds a file's contents as a string at compile time:

```ocaml
let icon_data = [%blob "assets/icon.png"]
let shader_source = [%blob "shaders/vertex.glsl"]
let default_config = [%blob "config/defaults.json"]
```

No more worrying about file paths at runtime or packaging data files with your
binary. The file contents become part of your compiled program.

### ppx_string -- String Interpolation

One thing that's always bugged me about OCaml is the lack of string
interpolation. [ppx_string](https://github.com/janestreet/ppx_string) fills that
gap:

```ocaml
let greeting name age =
  [%string "Hello, %{name}! You are %{age#Int} years old."]
```

The `#Int` suffix tells the PPX to convert the value using `Int.to_string`. You
can use any module that provides a `to_string` function.

## Writing Your Own PPX (A Gentle Introduction)

Most OCaml developers will never *need* to write a PPX, but understanding the
basics helps demystify the whole system. Let's build a very simple one.

Say we want an extension `[%upcase ...]` that converts a string literal to
uppercase at compile time. Here's the complete implementation using
[ppxlib](https://github.com/ocaml-ppx/ppxlib):

```ocaml
(* ppx_upcase.ml *)
open Ppxlib

let extension =
  Extension.V3.declare
    "upcase"
    Extension.Context.expression
    Ast_pattern.(single_expr_payload (estring __))
    (fun ~ctxt expr ->
      let loc = Expansion_context.Extension.extension_point_loc ctxt in
      let uppercased = String.uppercase_ascii expr in
      Ast_builder.Default.estring ~loc uppercased)

let rule = Context_free.Rule.extension extension
let () = Driver.register_transformation ~rules:[rule] "ppx_upcase"
```

The dune file:

```dune
(library
 (name ppx_upcase)
 (kind ppx_rewriter)
 (libraries ppxlib))
```

And usage:

```ocaml
let greeting = [%upcase "hello world"]
(* After preprocessing: let greeting = "HELLO WORLD" *)
```

The key pieces are:

1. **`Extension.V3.declare`** -- registers an extension with a name, the context where it
   can appear (expressions, patterns, types, etc.), the expected payload pattern,
   and an expansion function.
2. **`Ast_pattern`** -- a pattern-matching DSL for destructuring AST nodes. Here
   `estring __` matches a string literal and captures its value.
3. **`Ast_builder.Default`** -- helpers for constructing AST nodes. `estring`
   builds a string literal expression.
4. **`Driver.register_transformation`** -- registers the rule with ppxlib's
   driver.

For more complex PPXs (especially derivers), you'll also want to use
**Metaquot** (`ppxlib.metaquot`), which lets you write AST-constructing code
using actual OCaml syntax instead of manual AST builder calls:

```ocaml
(* Without metaquot -- verbose *)
Ast_builder.Default.pexp_apply ~loc
  (Ast_builder.Default.evar ~loc "string_of_int")
  [(Nolabel, expr)]

(* With metaquot -- just write OCaml *)
[%expr string_of_int [%e expr]]
```

The ppxlib documentation has excellent
[tutorials](https://ocaml-ppx.github.io/ppxlib/ppxlib/quick_intro.html) if you
want to go deeper.

## Debugging PPX-generated Code

One practical tip: when something goes wrong with PPX-generated code and you're
staring at a confusing type error, you can inspect what the PPX actually
generated:

```shell
# Show the preprocessed output for a file (dune 3.x+)
dune describe pp src/myfile.ml

# Alternatively, build the .pp.ml target and inspect it directly
dune build src/.mylib.pp/myfile.pp.ml
cat _build/default/src/.mylib.pp/myfile.pp.ml

# Or use ocaml-print-intf to see the generated interface
dune exec -- ocaml-print-intf src/myfile.ml
```

Seeing the expanded code often makes the error immediately obvious.

## The PPX Ecosystem Today

Most of the introductory PPX content out there was written around 2018-2019,
so it's worth noting how things have evolved since then.

The big story has been **ppxlib's consolidation of the ecosystem**. Back in
2019, some PPX rewriters still used the older `ocaml-migrate-parsetree` (OMP)
library, creating fragmentation. By 2021, [nearly all PPXs had migrated to
ppxlib](https://discuss.ocaml.org/t/an-update-on-the-state-of-the-ppx-ecosystem-and-ppxlib-s-transition/8200),
effectively ending the split. Today ppxlib is *the* way to write PPX rewriters
-- there's no real alternative to consider.

The transition hasn't always been smooth, though. In 2025, ppxlib 0.36.0
bumped its internal AST to match OCaml 5.2, which changed how functions are
represented in the parse tree. This [broke many downstream
PPXs](https://discuss.ocaml.org/t/breaking-ppx-api-changes-and-the-package-ecosystem/16837)
and temporarily split the opam universe between packages that worked with the
new version and those that didn't. The community worked through it with
proactive patching, but it highlighted an ongoing tension in the PPX world:
ppxlib shields you from most compiler changes, but major AST overhauls still
ripple through the ecosystem.

On the API side, ppxlib is gradually [deprecating its copy of
`Ast_helper`](https://ocaml.org/changelog/2025-10-01-ppxlib-0362) in favor of
`Ast_builder`, with plans to remove `Ast_helper` entirely in a future 1.0.0
release. If you're writing a new PPX today, use `Ast_builder` exclusively.

Meanwhile, OCaml 4.08's built-in binding operators (`let*`, `let+`, etc.) have
reduced the need for `ppx_let` in projects that don't use Jane Street's
ecosystem. It's a nice example of the language absorbing a pattern that PPX
pioneered. Perhaps one day we'll see more of this (e.g. native string interpolation).

## Further Reading

This article covers a lot of ground, but the PPX topic is pretty deep and complex,
so depending on how far you want to go you might want to read more on it.
Here are some of the best resources I've found on PPX:

- [Preprocessors and PPXs](https://ocaml.org/docs/metaprogramming) -- the
  official OCaml documentation on metaprogramming. A solid reference, though
  it assumes some comfort with the compiler internals.
- [An Introduction to OCaml PPX
  Ecosystem](https://tarides.com/blog/2019-05-09-an-introduction-to-ocaml-ppx-ecosystem/)
  -- Nathan Rebours' 2019 deep dive for Tarides. This is the most thorough
  tutorial on *writing* PPX rewriters I've seen. Some API details have changed
  since 2019 (notably the `Ast_helper` → `Ast_builder` shift), but the
  concepts and approach are still excellent.
- [ppxlib Quick
  Introduction](https://ocaml-ppx.github.io/ppxlib/ppxlib/quick_intro.html) --
  ppxlib's own getting-started guide. The best place to begin if you want to
  write your own PPX.
- [A Guide to PreProcessor
  eXtensions](http://ocamlverse.net/content/ppx.html) -- OCamlverse's
  reference page with a comprehensive list of available PPX libraries.
- [A Guide to Extension Points in
  OCaml](https://whitequark.org/blog/2014/04/16/a-guide-to-extension-points-in-ocaml/)
  -- Whitequark's original 2014 guide that introduced many developers to PPX.
  Historically interesting as a snapshot of the early PPX days. I was amused
  to see whitequark's name pop up here -- we collaborated quite a bit back in
  the day on her Ruby [parser](https://github.com/whitequark/parser) project,
  which was instrumental to [RuboCop](https://github.com/rubocop/rubocop).
  Seems you can find (former) Rubyists in pretty much every language community.

## Epilogue

PPX might look intimidating at first -- all those brackets and `@` symbols can
feel like line noise. But the core idea is simple: PPX generates boilerplate
code from your type definitions at compile time. You annotate your types with
what you want (`show`, `eq`, `yojson`, `sexp`, etc.), and the PPX rewriter
produces the code you'd otherwise have to write by hand.

For day-to-day OCaml programming, you really only need to know:

1. `[@@deriving ...]` on type declarations to generate useful functions
2. How to add PPX libraries to your dune file with `(pps ...)`
3. Which PPX libraries exist for common tasks (serialization, testing,
   pretty-printing)

The "writing your own PPX" part is there for when you need it, but honestly most
OCaml developers get by just fine using the existing ecosystem.

That's all I have for you today. Keep hacking!
