---
title: Wayward Predicates in Ruby
date: 2025-09-04 15:21 +0300
tags:
- Ruby
---

Recently I've been wondering how to name Ruby methods that have predicate
looking names (e.g. `foo?`), but don't behave like predicates - namely they
return other values besides the canonical `true` and `false`.[^1]

Here are a few classic examples:

```ruby
Float::INFINITY.infinite? # => 1

0.nonzero? # => nil
1.nonzero? # => 1

# funny enough...
0.zero? # => true
5.zero? # => false
```

Naming is hard and it took me a while to narrow my list of ideas to two options:

- wayward predicates
- deviant predicates

In the end I felt that "wayward predicates" was the way to go, as it sounds
less deviant.[^2] 

Common vocabulary is important when discussing domain-specific
topics and it makes it easier for people to refer to things within the domains.
That's why I'm writing this short article - perhaps it will be a first step to
establishing a common name for a pretty common thing.

Also, I've always been a believer that naming should be both informative and fun,
and that's certainly one fun name. Wayward predicates can surprise us unpleasantly
every now and then, but they are also a classic example of the flexibility of Ruby
and it's continued resistance to sticking to narrowly defined rules and guidelines.

That's all I have for you today. Keep hacking!

[^1]: This came up in the context of RuboCop, in case someone's wondering.
[^2]: See also <https://docs.rubocop.org/rubocop/cops_naming.html#namingpredicatemethod> for the first "official" usage of the term.
