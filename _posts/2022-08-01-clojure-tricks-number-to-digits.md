---
title: 'Clojure Tricks: Number to Digits'
date: 2022-08-01 10:42 +0300
tags:
- Clojure
- Trick
---

If you're into programming puzzles you probably know that there's a whole class
of problems about doing something (e.g. some calculations) with the digits of a
number. This means you need to break down a number into its digits first. I've
always assumed that those problems exist just because decomposing a number to
its digits is a classic example of recursion:

``` clojure
(defn digits [n]
  (if (< n 10)
    [n]
    (conj (digits (quot n 10)) (rem n 10))))

(digits 3361346435)
;; => [3 3 6 1 3 4 6 4 3 5]
```

That being said, I've also noticed that many people are approaching the problem
differently when faced with it (including me) - they typically convert the
number to string and then convert back the digit characters into
numbers. Probably the simplest way to do this is something like this:

``` clojure
(defn digits [n]
  (map #(read-string (str %)) (str n)))

(digits 3361346435)
;; => (3 3 6 1 3 4 6 4 3 5)
```

Notably, this solution doesn't require using the Java interop or any clever tricks.
If you know Java's API a bit better you might think of leveraging `Character/digit` instead:

``` clojure
(defn digits [n]
  (map #(Character/digit % 10) (str n)))
```

Much simpler, right? Now it's time for the final approach to solving the problem that is relatively common - namely a simple but clever trick to convert digit characters into numbers:

``` clojure
(defn char->int [c]
  (- (int c) 48))

(defn digits [n]
  (map char->int (str n)))
```

This relies on the fact that the integer value for `\0` is 48, for `\1` is 49 and so on. For some reason that's my favorite solution - probably because I love programming puzzles and I like (occasionally) writing unreadable code.

So, who has a different approach for converting a number to its digits? I've to hear about it!
