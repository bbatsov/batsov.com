---
title: Why F#?
date: 2025-03-30 17:54 +0300
tags:
- F#
- OCaml
- ML
- .NET
---

If someone had told me a few months ago I'd be playing with .NET again after a
15+ years hiatus I probably would have laughed at this.[^1] Early on in my
career I played with .NET and Java, and even though .NET had done some things
better than Java (as it had the opportunity to learn from some early Java
mistakes), I quickly settled on Java as it was a truly portable environment.

I guess everyone who reads my blog knows that in the past few years I've been
playing on and off with OCaml and I think it's safe to say that it has become
one of my favorite programming languages - alongside the likes of Ruby and
Clojure. My work with OCaml drew my attention recently to F#, an ML targeting
.NET, developed by Microsoft. The functional counterpart of the
(mostly) object-oriented C#. The newest ML language created...

## What is F#?

> Unfortunately, no one can be told what the Matrix is. You have to see it for yourself.
>
> -- Morpheus, The Matrix

Before we start discussing F#, I guess we should answer first the question
"What is F#?". I'll borrow a bit from the [official page](https://learn.microsoft.com/en-us/dotnet/fsharp/what-is-fsharp) to answer it.

F# is a universal programming language for writing succinct, robust and performant code.

F# allows you to write uncluttered, self-documenting code, where your focus remains on your problem domain, rather than the details of programming.

It does this without compromising on speed and compatibility - it is open-source, cross-platform and interoperable.

``` fsharp
open System // Gets access to functionality in System namespace.

// Defines a list of names
let names = [ "Peter"; "Julia"; "Xi" ]

// Defines a function that takes a name and produces a greeting.
let getGreeting name = $"Hello, {name}"

// Prints a greeting for each name!
names
|> List.map getGreeting
|> List.iter (fun greeting -> printfn $"{greeting}! Enjoy your F#")
```

**Trivia:** F# is the language that made the pipeline operator (`|>`) popular.

F# has numerous features, including:

- Lightweight syntax
- Immutable by default
- Type inference and automatic generalization
- First-class functions
- Powerful data types
- Pattern matching
- Async programming

A full set of features are documented in the [F# language guide](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/).

Looks pretty promising, right?

F# 1.0 was officially released in May 2005 by Microsoft Research. It was
initially developed by Don Syme at Microsoft Research in Cambridge and evolved
from an earlier research project called "Caml.NET," which aimed to bring OCaml
to the .NET platform.[^2] F# was officially moved from Microsoft Research to
Microsoft (as part of their developer tooling division) in 2010 (timed
with the release of F# 2.0).

F# has been steadily evolving since those early days and the most recent release
[F# 9.0](https://learn.microsoft.com/en-us/dotnet/fsharp/whats-new/fsharp-9) was
released in November 2024.  It seems only appropriate that F# would come to my
attention in the year of its 20th birthday!

There were several reasons why I wanted to try out F#:

- .NET became open-source and portable a few years ago and I wanted to check the progress on that front
- I was curious if F# offers any advantages over OCaml
- I've heard good things about the F# tooling (e.g. Rider and Ionide)
- I like playing with new programming languages

Below you'll find my initial impressions for several areas.

## The Language

As a member of the ML family of languages, the syntax won't surprise
anyone familiar with OCaml. As there are quite few people familiar with
OCaml, though, I'll mention that Haskell programmers will also feel right at
home with the syntax. And Lispers.

For everyone else - it'd be fairly easy to pick up the basics.

``` fsharp
// function application
printfn "Hello, World!"

// function definition
let greet name =
    printfn "Hello, %s!" name

greet "World"

// whitespace is significant, like in Python
let foo =
    let i, j, k = (1, 2, 3)

    // Body expression:
    i + 2 * j + 3 * k

// conditional expressions
let test x y =
  if x = y then "equals"
  elif x < y then "is less than"
  else "is greater than"

printfn "%d %s %d." 10 (test 10 20) 20

// Looping over a list.
let list1 = [ 1; 5; 100; 450; 788 ]
for i in list1 do
   printfn "%d" i

// Looping over a sequence of tuples
let seq1 = seq { for i in 1 .. 10 -> (i, i*i) }
for (a, asqr) in seq1 do
  printfn "%d squared is %d" a asqr

// A simple for...to loop.
let function1 () =
  for i = 1 to 10 do
    printf "%d " i
  printfn ""

// A for...to loop that counts in reverse.
let function2 () =
  for i = 10 downto 1 do
    printf "%d " i
  printfn ""

// Records

// Labels are separated by semicolons when defined on the same line.
type Point = { X: float; Y: float; Z: float }

// You can define labels on their own line with or without a semicolon.
type Customer =
    { First: string
      Last: string
      SSN: uint32
      AccountNumber: uint32 }

let mypoint = { X = 1.0; Y = 1.0; Z = -1.0 }

// Discriminated Union
type Shape =
    | Circle of radius: float
    | Rectangle of width: float * height: float

// Functing using pattern matching
let area shape =
    match shape with
    | Circle radius -> System.Math.PI * radius * radius
    | Rectangle (width, height) -> width * height

let circle = Circle 5.0
let rectangle = Rectangle(4.0, 3.0)

printfn "Circle area: %f" (area circle)
printfn "Rectangle area: %f" (area rectangle)
```

Nothing shocking here, right?

Here's another slightly more involved example:

``` fsharp
open System

// Sample data - simple sales records
type SalesRecord = { Date: DateTime; Product: string; Amount: decimal; Region: string }

// Sample dataset
let sales = [
    { Date = DateTime(2023, 1, 15); Product = "Laptop"; Amount = 1200m; Region = "North" }
    { Date = DateTime(2023, 2, 3);  Product = "Phone";  Amount = 800m;  Region = "South" }
    { Date = DateTime(2023, 1, 20); Product = "Tablet"; Amount = 400m;  Region = "North" }
    { Date = DateTime(2023, 2, 18); Product = "Laptop"; Amount = 1250m; Region = "East" }
    { Date = DateTime(2023, 1, 5);  Product = "Phone";  Amount = 750m;  Region = "West" }
    { Date = DateTime(2023, 2, 12); Product = "Tablet"; Amount = 450m;  Region = "North" }
    { Date = DateTime(2023, 1, 28); Product = "Laptop"; Amount = 1150m; Region = "South" }
]

// Quick analysis pipeline
let salesSummary =
    sales
    |> List.groupBy (fun s -> s.Product)                          // Group by product
    |> List.map (fun (product, items) ->                          // Transform each group
        let totalSales = items |> List.sumBy (fun s -> s.Amount)
        let avgSale = totalSales / decimal (List.length items)
        let topRegion =
            items
            |> List.groupBy (fun s -> s.Region)                   // Nested grouping
            |> List.maxBy (fun (_, regionItems) ->
                regionItems |> List.sumBy (fun s -> s.Amount))
            |> fst

        (product, totalSales, avgSale, topRegion))
    |> List.sortByDescending (fun (_, total, _, _) -> total)      // Sort by total sales

// Display results
salesSummary
|> List.iter (fun (product, total, avg, region) ->
    printfn "%s: $%M total, $%M avg, top region: %s"
        product total avg region)
```

Why don't you try saving the snippet above in a file called `Sales.fsx` and running it like this:

``` shell
dotnet fsi Sales.fsx
```

Now you know that F# is a great choice for ad-hoc scripts! Also, running `dotnet fsi` by itself
will pop an F# REPL where you can explore the language at your leisure.

I'm not going to go into great details here, as much of what I wrote about OCaml
[here]({% post_url 2022-08-29-ocaml-at-first-glance %}) applies to F# as well.
I'd also suggest this quick [tour of F#](https://learn.microsoft.com/en-us/dotnet/fsharp/tour)
to get a better feel for its syntax.

**Tip:** Check out the [F# cheatsheet](https://fsprojects.github.io/fsharp-cheatsheet/)
if you'd like to see a quick syntax reference.

One thing that made a good impression to me is the focus of the language designers on
making F# approachable to newcomers, by providing a lot of small quality of life improvements
for them. Below are few examples, that probably don't mean much to you, but would mean something
to people familiar with OCaml:

``` fsharp
// line comments
(* the classic ML comments are around as well *)

// mutable values
let mutable x = 5
x <- 6

// ranges and slices
let l = [1..2..10]
name[5..]

// C# method calls look pretty natural
let name = "FOO".ToLower()

// operators can be overloaded for different types
let string1 = "Hello, " + "world"
let num1 = 1 + 2
let num2 = 1.0 + 2.5

// universal printing
printfn "%A" [1..2..100]
```

I guess some of those might be controversial, depending on whether you're a ML
language purist or not, but in my book anything that makes ML more popular is a
good thing.

Did I also mention it's easy to work with unicode strings and regular expressions?

Often people say that F# is mostly a staging ground for future C# features, and perhaps that's true.
I haven't observed both languages long enough to have my own opinion on the subject, but I was impressed
to learn that `async/await` (of C# and later JavaScript fame) originated in... F# 2.0.

> It all changed in 2012 when C#5 launched with the introduction of what has now
> become the popularized `async/await` keyword pairing. This feature allowed you to
> write code with all the benefits of hand-written asynchronous code, such as not
> blocking the UI when a long-running process started, yet read like normal
> synchronous code. This `async/await` pattern has now found its way into many
> modern programming languages such as Python, JS, Swift, Rust, and even C++.
>
> F#’s approach to asynchronous programming is a little different from `async/await`
> but achieves the same goal (in fact, `async/await` is a cut-down version of F#’s
> approach, which was introduced a few years previously, in F#2).
>
> -- Isaac Abraham, F# in Action

Time will tell what will happen, but I think it's unlikely that C# will ever be able to fully replace F#.

I've also found this [encouraging comment from 2022](https://www.reddit.com/r/fsharp/comments/xlegmc/why_doesnt_microsoft_use_f/) that Microsoft might be willing to invest more in F#:

> Some good news for you. After 10 years of F# being developed by 2.5 people
> internally and some random community efforts, Microsoft has finally decided to
> properly invest in F# and created a full-fledged team in Prague this
> summer. I'm a dev in this team, just like you I was an F# fan for many years
> so I am happy things got finally moving here.

Looking at the changes in F# 8.0 and F 9.0, it seems the new full-fledged team
has done some great work!

## Ecosystem

It's hard to assess the ecosystem around F# after such a brief period, but overall it seems to
me that there are fairly few "native" F# libraries and frameworks out there and most people
rely heavily on the core .NET APIs and many third-party libraries and frameworks geared towards C#.
That's a pretty common setup when it comes to hosted languages in general, so nothing surprising here as well.

If you've ever used another hosted language (e.g. Scala, Clojure, Groovy) then you probably know what
to expect.

[Awesome F#](https://github.com/fsprojects/awesome-fsharp) keeps track of popular F# libraries, tools and frameworks. I'll highlight here the web development and data science libraries:

**Web Development**

- **Giraffe**: A lightweight library for building web applications using ASP.NET Core. It provides a functional approach to web development.
- **Suave**: A simple and lightweight web server library with combinators for routing and task composition. (Giraffe was inspired by Suave)
- **Saturn**: Built on top of Giraffe and ASP.NET Core, it offers an MVC-style framework inspired by Ruby on Rails and Elixir's Phoenix.
- **Bolero**: A framework for building client-side applications in F# using WebAssembly and Blazor.
- **Fable**: A compiler that translates F# code into JavaScript, enabling integration with popular JavaScript ecosystems like React or Node.js.
- **Elmish**: A model-view-update (MVU) architecture for building web UIs in F#, often used with Fable.
- **SAFE Stack**: An end-to-end, functional-first stack for building cloud-ready web applications. It combines technologies like Saturn, Azure, Fable, and Elmish for a type-safe development experience.

**Data Science**

- **Deedle**: A library for data manipulation and exploratory analysis, similar to pandas in Python.
- **DiffSharp**: A library for automatic differentiation and machine learning.
- **FsLab**: A collection of libraries tailored for data science, including visualization and statistical tools.

I haven't played much with any of them at this point yet, so I'll reserve any
feedback and recommendations for some point in the future.

## Documentation

The official documentation is pretty good, although I find it kind of weird that
some of it is hosted on [Microsoft's site](https://learn.microsoft.com/en-us/dotnet/fsharp/what-is-fsharp)
and the rest is on <https://fsharp.org/> (the site of the F# Software Foundation).

I really liked the following parts of the documentation:

- [F# Style Guide](https://learn.microsoft.com/en-us/dotnet/fsharp/style-guide/)
- [F# Design](https://github.com/fsharp/fslang-design) - a repository of RFCs (every language should have one of those!)
- [F# Standard Library API](https://fsharp.github.io/fsharp-core-docs/)

<https://fsharpforfunandprofit.com/> is another good learning resource. (even if it seems a bit dated)

## Dev Tooling

I've played with the F# plugins for several editors:

- Emacs (`fsharp-mode`)
- Zed (third-party plugin)
- Helix (built-in support for F#)
- VS Code ([Ionide](https://ionide.io/))
- Rider (JetBrains's .NET IDE)

Overall, Rider and VS Code provide the most (and the most polished) features,
but the other options were quite usable as well.  That's largely due to the fact
that the F# LSP server `fsautocomplete` (naming is hard!) is quite robust and
any editor with good LSP support gets a lot of functionality for free.

Still, I'll mention that I found the tooling lacking in some regards:

- `fsharp-mode` doesn't use TreeSitter (yet) and doesn't seem to be very actively developed (looking at the code - it seems it was derived from `caml-mode`)
- Zed's support for F# is quite spartan
- In VS Code shockingly the expanding and shrinking selection is broken, which is quite odd for what is supposed to be the flagship editor for F#

I'm really struggling with VS Code's keybindings (too many modifier keys and functions keys for my taste) and editing model, so I'll likely stick with Emacs going forward. Or I'll finally spend more quality time with neovim!

It seems that everyone is using the same code formatter (`Fantomas`), including the F# team, which is great!
The linter story in F# is not as great (seems the only popular linter [FSharpLint](https://fsprojects.github.io/FSharpLint/) is abandonware these days), but when your
compiler is so good, you don't really need a linter as much.

Oh, well... It seems that Microsoft are not really particularly invested in
supporting the tooling for F#, as pretty much all the major projects in this
space are community-driven.

Using AI coding agents (e.g. Copilot) with F# worked pretty well, but I didn't
spend much time on this front.

In the end of the day any editor will likely do, as long as you're using LSP.

By the way, I had an interesting observation while programming in F# (and OCaml for that matter) -
that when you're working with a language with a really good type system you don't really need that much
from your editor. Most the time I'm perfectly happy with just some inline type information (e.g. something like CodeLenses), auto-completion and the ability to easily send code to `fsi`. Simplicity continues
to be the ultimate sophistication...

## Use Cases

Given the depth and breath of .NET - I guess that sky is the limit for you!

Seems to me that F# will be a particularly good fit for data analysis and manipulation, because
of features like [type providers](https://learn.microsoft.com/en-us/dotnet/fsharp/tutorials/type-providers/).

Probably a good fit for backend services and even full-stack apps, although I haven't really played
with the F# first solutions in this space yet.

Fable and Elmish make F# a viable option for client-side programming and might offer
another easy way to sneak F# into your day-to-day work.

**Note:** Historically, Fable has been used to target JavaScript but since Fable
4, you can also target other languages such as TypeScript, Rust, Python, and
more.

Here's how easy it is to transpile an F# codebase into something else:

``` shell
# If you want to transpile to JavaScript
dotnet fable

# If you want to transpile to TypeScript
dotnet fable --lang typescript

# If you want to transpile to Python
dotnet fable --lang python
```

Cool stuff!

## Community

My initial impression of the community is that it's fairly small, perhaps even
smaller than that of OCaml.  The F# Reddit and Discord (the one listed on
Reddit) seem like the most active places for F# conversations. There's supposed
to be some F# Slack as well, but I couldn't get an invite for it. (seems the
automated process for issuing those invites has been broken for a while)

I'm still not sure what's the role Microsoft plays in the community, as I
haven't seen much from them overall.

For a me a small community is not really a problem, as long as the community is
vibrant and active. Also - I've noticed I always feel more connected to smaller
communities. Moving from Java to Ruby back in the day felt like night and day as
far as community engagement and sense of belonging go.

I didn't find many books and community sites/blogs dedicated to F#, but I didn't
really expect to in the first place.

The most notable community initiatives I discovered were:

- [Amplifying F#](https://amplifyingfsharp.io/) - an effort to promote F# and to get more businesses involved with it
- [F# for Fun and Profit](https://fsharpforfunandprofit.com) - a collection of tutorials and essays on F#
- [F# Lab](https://fslab.org/) - The community driven toolkit for datascience in F#
- [F# Weekly](https://sergeytihon.com/category/f-weekly/) - a weekly newsletter about the latest developments in the world of F#

Seems to me that more can be done to promote the language and engage new programmers and businesses
with it, although that's never easy 20 years into the existence of some project. I continue to be
somewhat puzzled as to why Microsoft doesn't market F# more, as I think it could be a great
marketing vehicle for them.

All in all - I don't feel qualified to comment much on the F# community at this point.

## The Popularity Contest

Depending on the type of person you are you may or may not care about a a programming language's
"popularity". People often ask my why I spent a lot of time with languages that are unlikely to
ever result in job opportunities for me, e.g.:

- Emacs Lisp
- Clojure
- OCaml
- F#

Professional opportunities are important, of course, but so are:

- having fun (and the F in F# stands for "fun")
- learning new paradigms and ideas
- challenging yourself to think and work differently

That being said, F# is not a popular language by most conventional metrics. It's not highly ranked
on TIOBE, StackOverflow or most job boards. But it's also not less popular than most "mainstream"
functional programming languages. The sad reality is that functional programming is still not
mainstream and perhaps it will never be.

A few more resources on the subject:

- [About F#'s popularity](https://medium.com/@lanayx/about-f-popularity-c9b78ed89252)
- [How Popular is F# in 2024](https://hamy.xyz/blog/2024-11_fsharp-popularity)
  - Here's also a [video](https://www.youtube.com/watch?v=JioaHcy_QE0&t=1s) for the article above

## F# vs OCaml

> The early conception of F# was simple: to bring the benefits of OCaml to .NET and .NET to OCaml: a
> marriage between strongly typed functional programming and .NET. Here “OCaml” meant both the
> core of the language itself, and the pragmatic approach to strongly-typed functional programming
> it represented. The initial task was relatively well-defined: I would re-implement the core of the
> OCaml language and a portion of its base library to target the .NET Common Language Runtime.
> The implementation would be fresh, i.e. not using any of the OCaml codebase, for legal clarity.
>
> -- Don Syme, creator of F#, The Early History of F#

F# was derived from OCaml, so the two languages share a lot of DNA. Early on
F# made some efforts to support as much of OCaml's syntax as possible, and it
even allowed the use of `.ml` and `.mli` file extensions for F# code. Over time
the languages started to diverge a bit, though.[^3]

Creating a language that's independent from OCaml, of course, was something
intended from the very beginning. That's also reflected in the decision
to chose the name F#, even if early versions of the language were called "Caml.NET":

> Although the first version of F# was initially presented as “Caml-for-.NET”,
> in reality it was always a new language, designed for .NET from day 1. F# was
> never fully compatible with any version of OCaml, though it shared a compatible
> subset, and it took Caml-Light and OCaml as its principal sources of design
> guidance and inspiration.
>
> -- Don Syme, The Early History of F#

If you ask most people about the pros and cons of F# over OCaml you'll probably
get the following answers.

**F# Pros**

- Runs on .NET
  - Tons of libraries are at disposal
- Backed by Microsoft
- Arguably it's a bit easier to learn by newcomers (especially those who have only experience with OO programming)
  - The syntax is slightly easier to pick up (I think)
  - The compiler errors and warnings are "friendlier" (easier to understand)
  - It's easier to debug problems (partially related to the previous item)
- Strong support for [async programming](https://learn.microsoft.com/en-us/dotnet/fsharp/tutorials/async)
- Has some cool features, absent in OCaml, like:
  - Anonymous Records
  - Active Patterns
  - Computational expressions
  - Sequence comprehensions
  - Type Providers
  - Units of measure

**F# Cons**

- Runs on .NET
  - The interop with .NET influenced a lot of language design decisions (e.g. allowing `null`)
- Backed by Microsoft
  - Not everyone likes Microsoft
  - Seems the resources allocated to F# by Microsoft are modest
  - It's unclear how committed Microsoft will be to F# in the long run
- Naming conventions: I like `snake_case` way more than `camelCase` and `PascalCase`
- Misses some cool OCaml features
  - First-class modules and functors
  - GADTs
- Doesn't have a friendly camel logo
- The name F# sounds cool, but is a search and filename nightmare (and you'll see FSharp quite often in the wild)

Both F# and OCaml can also target JavaScript runtimes as well - via [Fable][] on
the F# side and Js_of_ocaml and Melange on the OCaml side. Fable seems like a
more mature solution at a cursory glance, but I haven't used any of the three
enough to be able to offer an informed opinion.

In the end of the day both remain two fairly similar robust, yet niche,
languages, which are unlikely to become very popular in the future. I'm guessing
working professionally with F# is more likely to happen for most people, as .NET
is super popular and I can imagine it'd be fairly easy to sneak a bit of F# here
in there in established C# codebases.

One weird thing I've noticed with F# projects is that they still use XML project
manifests, where you have to list the source files manually in the order in
which they should be compiled (to account for the dependencies between them). I
am a bit shocked that the compiler can't handle the dependencies automatically,
but I guess that's because in F# there's not direct mapping between source files
and modules. At any rate - I prefer the OCaml compilation process (and Dune) way
more.

As my interest in MLs is mostly educational I'm personally leaning towards OCaml, but if I had to build
web services with an ML language I'd probably pick F#. I also have a weird respect for every language
with its own runtime, as this means that it's unlikely that the runtime will force some compromises
on the language.

## Closing thoughts

All in all I liked F# way more than I expected to! In a way it reminded me of my
experience with Clojure back in the day in the sense that Clojure was the most
practical Lisp out there when it was released, mostly because of its great
interop with Java.

I have a feeling that if .NET was portable since day 1 probably ClojureCLR would have become
as popular as Clojure, and likely F# would have developed a bigger community and
broader usage by now. I'm fairly certain I would have never dabbled in .NET again
if it hadn't been for .NET Core, and I doubt I'm the only one.

Learning OCaml is definitely not hard, but I think that people interested to learn some ML
dialect might have an easier time with F#. And, as mentioned earlier, you'll probably have an
easier path to "production" with it.

I think that everyone who has experience with .NET will benefit from learning F#.
Perhaps more importantly - everyone looking to do more with an ML family language
should definitely consider F#, as it's a great language in its own right, that gives
you access to one of the most powerful programming platforms out there.

Let's not forget about [Fable][], which makes it possible for you leverage
F# in JavaScript, Dart, Rust and Python runtimes!

So, why F#? In the F# community there's the saying that the "F" in F# stands for
"Fun". In my brief experience with F# I found this to be very true! I'll go a
step further and make the claim that F# is both seriously **fun** and seriously
practical!

Also if your code compiles - it will probably work the way you expect it to. I
hear that's generally considered a desirable thing in the world of programming!

That's all I have for you today. Please, share in the comments what do you love about F#!

In sane type systems we trust!

## Discussions

- [Hacker News](https://news.ycombinator.com/item?id=43546004)
- [Lobsters](https://lobste.rs/s/kubso9/why_f)

[^1]: I had some C# courses in the university and I wrote my bachelor's thesis in C#. It was a rewrite of Arch Linux's `pacman`, running on Mono. This was way back in 2007.
[^2]: See <https://fsharp.org/history/hopl-final/hopl-fsharp.pdf>
[^3]: <https://github.com/fsharp/fslang-suggestions/issues/985>

[Fable]: https://fable.io/
