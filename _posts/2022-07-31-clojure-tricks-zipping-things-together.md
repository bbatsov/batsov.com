---
title: 'Clojure Tricks: Zipping Things Together'
date: 2022-07-31 14:10 +0300
tags:
- Clojure
- Trick
---

Many programming languages have a function for combining the elements of multiple collections (e.g. arrays or lists) together. Typically this function is named `zip`. Here's an example from Haskell:

``` haskell
zip [1, 2] ['a', 'b'] -- => [(1, 'a'), (2, 'b')]
```

Clojure doesn't have a `zip` function in the standard library, which leads many newcomers to the language to wonder what to use in its place. The answer is quite simple:

```clojure
;; let's zip a couple of lists, even if no one really uses them in Clojure
(map vector '(1 2 3) '(4 5 6))
;; => ([1 4] [2 5] [3 6])

;; works with vectors as well
(map vector [1 2 3] [4 5 6])
;; => ([1 4] [2 5] [3 6])

;; actually this works with anything seq-able
(map vector "this" "that")
;; => ([\t \t] [\h \h] [\i \a] [\s \t])

;; and you can mix and match different seq types
(map vector [1 2 3 4] "this")
;; => ([1 \t] [2 \h] [3 \i] [4 \s])
```

The function `vector` takes a variable number of arguments and produces a vector out of them. And vectors happen to be the idiomatic way to represent the concept of tuples (common in other functional programming languages) in Clojure. By the way, you can zip as many sequences together as your heart desires:[^1]

```clojure
(map vector [1 2 3] [4 5 6] [7 8 9])
;; => ([1 4 7] [2 5 8] [3 6 9])
```

And they don't all have to be of the same length either:

```clojure
(map vector [1 2 3] [4 5 6] [7 8 9] [10 11])
;; => ([1 4 7 10] [2 5 8 11])
```

One more thing... Clojure also has a function named `zipmap` that can zip a couple of seqs into a map:

```clojure
(zipmap [:a :b :c :d :e] [1 2 3 4 5])
;; => {:a 1, :b 2, :c 3, :d 4, :e 5}
```

That's all I have for you today. Zip long and prosper!

[^1]: That's a big advantage over Haskell's `zip` mentioned earlier, as it can only combine two lists. There's a similar function called `zip3` that can combine three lists.
