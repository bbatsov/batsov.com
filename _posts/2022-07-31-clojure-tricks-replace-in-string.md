---
title: 'Clojure Tricks: Replace in String'
date: 2022-07-31 11:12 +0300
tags:
- Clojure
- Trick
---

Today I saw a clever bit of Clojure code involving `clojure.string/replace`, that reminded me how powerful the Clojure standard library is. I guess pretty much everyone knows that `replace` is normally used to replace some part of a string using a regular expression to describe what exactly to replace:

```clojure
(str/replace "OCaml rocks!" #"([Hh]askell)|([Oo][Cc]aml)" "Clojure")
;; => "Clojure rocks!"

;; we can refer to the match groups in the replacement string
(str/replace "Haskell rocks!" #"([Hh]askell)|([Oo][Cc]aml)" "$1 is nice, but Clojure")
;; => "Haskell is nice, but Clojure rocks!"
```

Pretty useful and pretty straightforward. But wait, there's more! I had forgotten you can actually use a function for the replacement, which allows us to do more sophisticated things. Here's how we can capitalize every word with 5 or more letters in a string:

```clojure
(str/replace "master of Clojure is pulling the strings" #"\w{5,}" str/upper-case)
;; => "MASTER of CLOJURE is PULLING the STRINGS"
```

If you've got more match groups in your regular expression you can use all of them in the function that you're using to generate the replacements:

```clojure
(str/replace "pom kon sor" #"(.)o(.)" (fn [[_ a b]] (str (str/upper-case a) "-o-" (str/upper-case b))))
"P-o-M K-o-N S-o-R"
```

Note here the use of deconstructing to account that each match is essentially a vector of the full match and each group match (e.g. `["pom" "p" "m"]`).

That's I have for you today! Feel free to share with me more fun usages of `replace`!
