---
layout: single
title: "Programming languages worth learning"
toc: true
tags:
- Programming
---

## Prelude

Programming languages have always been a passion of mine and through
the years I've learnt quite a few of them. The first one was Pascal,
some 13 years ago, and the last was Scala, just a couple of months ago.

Although the authors of many languages claim that the language they
created is the greatest thing after hot water, this is rarely the
case. Most of the "unique" features are not quite unique, and the truly
unique stuff is often just useless. I don't believe that there is a
single greatest and unparallelled language, but I do believe that some
languages are more valuable them others in term of both theory(the
concepts around which they revolve) and practice(the chances of you
landing a job with them or simply getting a task done).

In this blog post I'll review the ten or so languages that I've found to
be most enlightening/helpful for me over the years. I think that every
professional software engineer should have at least a passing
knowledge of them.

## C

The C programming language has been around for about forty
years now (it appeared in 1973). While it's often viewed as a higher
level assembly language today in the era of Java, .Net and Python, C
remains the sole choice for doing serious system programming - writing
drivers, all kinds of servers and virtual machines.

Learning C also give you an insight to the inner working of the
computer, like memory management and native data types (based on a CPU's
registries).

The best way to get started with C
hasn't changed in the past 20+ years - just pick a copy of ["The C
Programming Language"](http://www.amazon.com/Programming-Language-2nd-Brian-Kernighan/dp/0131103628) by K&R.

## Lisp

> Lisp is worth learning for the profound enlightenment
> experience you will have when you finally get it; that experience will
> make you a better programmer for the rest of your days, even if you
> never actually use Lisp itself a lot.
>
> -- Eric S. Raymond, How to Become a Hacker

One of the oldest programming languages around - created in 1958 and
still relevant today. Important for its unique code is data approach,
advanced code generation facilities (macros) and the ability to develop
software in incremental and interactive fashion.

Although many of the features that originally made it truly
unique (like garbage collection, if expression, function objects) are
now found in many modern languages, Lisp still offers some compelling
alternatives for those interested to explore it.

These days Common Lisp is considered the canonical Lisp dialect - a
multi-paradigm language with excellent support for imperative,
functional and object-oriented programming. Another popular dialect is
Scheme which is a simpler language focused mainly on functional
programming and until recently was a popular choice for teaching
introductory programming classes in many major US
universities (recently it's being displaced by Python).

You cannot start with a better introduction to Common Lisp than Peter
Seibel's
["Practical Common Lisp"](http://www.gigamonkeys.com/book/). If you
fancy Scheme more take a look at the classic text
["Structure and Interpretation of Computer Programs"](http://mitpress.mit.edu/sicp/full-text/book/book.html). A
good source of exercises for aspiring Lisp programmers are the
["99 Lisp Problems"](https://www.ic.unicamp.br/~meidanis/courses/mc336/2006s2/funcional/L-99_Ninety-Nine_Lisp_Problems.html).

## Java

Let's face it - if you're in the market for jobs a third of them are
about Java EE or Android development (the other two thirds are probably
related to PHP and .NET). The language is not quite
elegant, but the platform is truly magnificent. Although there are
many other languages targeting the JVM (Scala, Groovy, Clojure, JRuby,
Jython - just to name a few) Java is still predominant by a wide
margin and this is unlikely to change soon. It's actually [the most
popular programming language in the world](http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html).

I've taught a couple of introductory Java programming courses (in
Bulgarian), but I'd recommend the
["Core Java"](http://www.amazon.com/Core-Java-TM-I--Fundamentals-8th/dp/0132354764)
book over my lectures any day of
the week.

## Haskell

Functional programming has been gaining popularity in recent years
with the rise of parallel computers, and of the pure functional
programming languages Haskell is probably the closest to the
mainstream. It features great ideas like type inference, lazy
evaluation, monads, pattern matching. As with Lisp many of the
features of Haskell can be found in more impure packages(like Scala
and Clojure), but Haskell is still the top pure functional language in
my humble opinion.

There are great free Haskell learning resources
on-line like ["Learn you a Haskell for great good"](http://learnyouahaskell.com/) and ["Real world
Haskell"](http://book.realworldhaskell.org/read/)

## Perl

Once the undisputed king of the Internet Perl has recently fallen down
from grace in its battle with the newer generation of dynamic
languages like PHP, Ruby and Python. While I wouldn't advise anyone to
start writing Web or Enterprise apps with Perl it's still the best language for
writing administration and helper scripts with minimum fuss and maximum
developer throughput. It features the greatest support for text
processing ever and an extremely flexible (albeit a bit of confusing)
syntax.

Perl [CPAN](http://www.cpan.org/) is probably the largest collection of third party
libraries for a single language ever assembled.

Pick up a copy of ["Learning Perl"](http://oreilly.com/catalog/9780596520113) or just browse the excellent
*perldoc* and start coding in Perl right away.

## Clojure

Clojure is a Lisp dialect with an unique support for parallel and
concurrent programming and runs on top of the venerable JVM. If you're
looking to do some serious parallel programming look no further. Other
than that you'll find in Clojure a superb collection of functional
data structures, pervasive use of laziness, higher order functions and
tail-call optimizations,
and some rather novel ideas on the topics of state and
identity. Clojure also cleans up a bit the traditional Lisp
syntax (read this as Clojure has fewer parentheses than say Common Lisp).

To get started I recommend you to watch the free Clojure screencasts
on [YouTube](https://www.youtube.com/user/clojuretv).

## Prolog

The most famous language from the logic programming family. Solving a
problem like a sudoku puzzle in Prolog will be an eye opening
experience for any developer. While it's unlikely that you'll ever use
it practice the ideas found in it, Prolog will truly expand your thinking
horizons.

A good starting point in your journey to Prolog will be
["Learn Prolog Now"](http://www.learnprolognow.org/). You may do a
follow up with the collection of programming puzzles ["Prolog problems"](http://sites.google.com/site/prologsite/prolog-problems/).

## Ruby

A pure object oriented dynamic scripting language with a very nice support for
metaprogramming. It has versions written in Java (JRuby) and
.Net (IronRuby) which makes it easy to integrate it with any software
for those popular platforms. The language became popular with the rise of the Ruby
on Rails web framework, but it has many potential applications that don't
involve RoR or web development.

["Programming Ruby"](http://www.amazon.com/Programming-Ruby-1-9-Pragmatic-Programmers/dp/1934356085) and ["The Ruby Programming Language"](http://www.amazon.com/Ruby-Programming-Language-David-Flanagan/dp/0596516177) are two of the
best introductory Ruby books around.

## Python

A pure object oriented dynamic scripting language with a focus on
simplicity, readability and maintainability. Popular for development
of web applications, GUI applications and system administration
utilities. Driven by the motto "There is only one way to do it" and
the philosophy "It comes with battery included". Python is Google's
darling and is widely used by the IT giant.

A nice free on-line book about Python is ["Dive into Python 3"](http://diveintopython3.org/).

## C\#

The flagship language of the .NET platform. Similar in many aspects to
Java C# is never-the-less ahead then Java in the innovations
department - first to introduce concepts like Generics and Attributes
to the mainstream programmers. It features some nice improvements over
Java like properties, flexible namespaces and limited type inference.

The primary reason it's included in this list, however, is simply
the sheer amount of job openings for C## developers. In my home
country (Bulgaria) about a third of all programming positions are C##
related.

My favourite C# book happens to be
["C# in Depth"](http://www.amazon.com/C-Depth-Second-Jon-Skeet/dp/1935182471). You
might happen to enjoy it as well.

## Scala

An interesting blend of pure object orientation and functional
programming with some concurrency support baked in (actors). Of the
current crop of JVM languages, next to Clojure, Scala looks most
promising. It features an advanced static type system (more advanced
than Haskell's), state of the art Java integration, support for
pattern matching, extractors and other functional goodness. If any
language has a chance of displacing the Java programming language it
must be Scala... Even the father of Java James Gosling acknowledged
that if he decided to replace Java with another language it would be Scala.

Start your journey to Scala mastery with
["Programming in Scala"](http://www.artima.com/pins1ed/).

## JavaScript

We cannot conclude this whirlwind tour of notable programming
languages without mentioning the King of the Web, the language that
drove the Web 2.0 revolution - JavaScript. Although it has a terrible
name (JavaScript shares nothing with Java), a questionable programming
model built around mutating global variables, and prototype instead of
class inheritance, it is supported virtually everywhere (most web
browsers have built-in JavaScript interpreters) and any web
developer will do well to learn some JavaScript.

## Epilogue

We cannot be experts in ten or twenty programming languages - I'm
certain of that. I do,
however, believe that all the ideas and techniques that we discover in
different programming languages will generally enrich our thinking and
make us better software engineers in principle.

**P.S.** _If you're wondering why a certain "great" language X is not on
the list keep in mind that this is my personal (and highly subjective)
point of view on the subject. Who knows, in some dark and twisted
place there may very well be people who consider BASIC a programming
language masterpiece..._
