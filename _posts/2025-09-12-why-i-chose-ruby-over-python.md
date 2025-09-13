---
title: Why I Chose Ruby over Python: Redux
date: 2025-09-12 9:50 +0300
tags:
- Ruby
- Python
---

This year I spent a bit of time playing with Python, after
having mostly ignored it since 2005 when was learning it
originally. I did like Python back then, but a few years afterwards
I discovered Ruby and quickly focused my entire attention on it.

There were many (mostly small) reasons why I leaned towards Ruby
back then and playing with Python now made me remember a few of
them. I thought it might be interesting to write a bit about those,
so here we go.

**Disclaimer:** This is not a rant and I know that:

- the things I prefer in Ruby over Python are super subjective
- for every thing that Ruby does "better" there's something else that Python does better

So, treat this as an amusing personal account and nothing more than that.

## Built-in functions

Probably the thing about Python that bothered me the most what stuff that would
normally be methods are global functions (that often call some object methods internally).
I'm referring to the likes of:

- `len`
- `sum`
- `pow`

You can find the full list [here](https://docs.python.org/3/library/functions.html).

I'm guessing the reason for this (as usual) is historical, but I much prefer the way Ruby does things.
E.g. `str.length` instead of `len(str)` or `arr.sum` vs `sum(arr)`.

## The `del` keyword

`del` is a pretty weird beast as it can:

- delete variables
- list items
- dictionary items

``` python
numbers = [1, 2, 3, 4, 5]
del numbers[2]
numbers
# => [1, 2, 4, 5]

del numbers
numbers
# => NameError: name 'numbers' is not defined
```

Why would someone want a keyword for removing items from lists and dictionaries instead of some method is beyond me. Ruby's arrays have methods like:

- `delete` (to remove some values)
- `delete_at` (to remove some index)

## Too many statements (and functions returning `None`)

In Ruby almost everything is an expression (meaning that evaluating it would
result in a value). In Python a lot of things are consider "statements" -
something executed for their side effects only. If you haven't used
languages like Ruby or Lisp this might sound a bit strange, but if we go back
to the previous section about `del`, we can observe that:

- There's no obvious way to determine if `del` actually removed something
- In Ruby, however, most deletions result in informative results

``` ruby
arr = (1..10).to_a
# => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
arr.delete(1)
# => 1
arr.delete(1)
# => nil
arr.delete_at(0)
# => 2
```

That's something that I really value and I consider it one of the bigger
practical advantages of Ruby over Python.

## Semantic indentation

At first I thought the semantic indentation used by Python is super cool,
as it reduces a bit the typing one needs to do:

```python
if a < b:
    max = b
else:
    max = a
```

In hindsight, however, I quickly realized that it also:

- makes things harder for tooling, as it can't really rearrange indented code
  sections
- in editors you can't have much in terms of auto-indenting as you type (as the
  editor can't know when a block finishes without you outdenting explicitly)

One more thing - 4 spaces by default seems a tad too much to me, although that's obviously debatable.

**P.S.** I feel obliged to admit I'm not a big fan of `do/end` either and would have preferred `{}` instead, but it is how it is.

## The `bool` type

I'm not a fan of Python's `bool` type as for me it's a pretty weird duck:

- `bool` was added only in Python 2.3
- it inherits from `int`
- `True` and `False` are essentially 1 and 0

I get how things ended up the way they are, but for me it's not OK to be able to write code like `True + 1`.
I'm also not a fan of treating empty collection literals as `False`, although I definitely have less issues
with this than with 0 and 1.

To compare this with Ruby:

- `boolean` has nothing to do with numbers there
- the only things are considered logically false are `false` and `nil`

This definitely resonates better with me.

**Side note:** Lately I've been playing a lot with languages like OCaml, F# and Rust, that's why to
me it now feels extra strange to have a boolean type that works like an integer type.

### Ranges

I really like the range literals in Ruby:

- `1..n` (inclusive range)
- `1...n` (exclusive range)

Python has the `range` function that kind of gets the job done, but for whatever reason
it doesn't have the option to mark something as inclusive range.

Definitely not a big deal, but one of the many small touches of Ruby's syntax that I came to appreciate over time.

### Naming conventions

Ruby predicates typically have names ending in `?` - e.g. `even?`, `odd?`, `digit?`. This makes them
really easy to spot while reading some code. Python sticks to the more common convention of prefixing
such methods with `is_`, `has_`, etc and that's fine. One thing that bothers me a bit is that often
there's not spaces between the prefix and the rest of the name (e.g. `str.isdigit?`), which doesn't
read great in my opinion.

More importantly, in Ruby and Python it's common to have destructive and
non-destructive versions of some methods. E.g. - `arr.sort!` vs `arr.sort` in
Ruby, and `list.sort` vs `sorted(list)` in Python.

I don't know about you, but to me it seems that:

- Ruby encourages you to favor the non-destructive versions of the methods, unlike Python
- Ruby's more consistent than Python

I'm guessing the reasons here are also historical.

### Triple-quoted string literals

Multi-line text literals are common in many languages, but I'm not super fond of:

```python
x = """a multi-line text
enclosed by
triple quotes
"""
```

Who thought that typing those would be easy?

It's not that HEREDOCS in Ruby are great either, but I guess they are at least more common in various programming languages.

## The million Python package managers

Ruby has `rubygems` and `bundler`. That's it. Everyone uses them. Life is simple.
Things are a lot more complicated in the realm of Python where several different tools
have been in fashion over the years:

- `easy_install`
- `pip`
- `pipx`
- `poetry`

Now it seems that `uv` might replace them all. Until something replaces `uv` I guess...

## Epilogue

And that's a wrap. I'm guessing at this point most Rubyists reading this would probably agree with
my perspective (shocking, right?) and most Pythonistas won't. And that's fine.
I'm not trying to convince anyone that Ruby's a better language than Python, I'm just
sharing the story of how I ended up in team Ruby almost 20 years ago.

Back in the day I felt that Ruby's syntax was more elegant and more consistent than Python's, and
today my sentiment is more or less the same. Don't get me wrong, though - I like Python overall
and enjoy using it occasionally. It just doesn't make me as happy as Ruby does.

I've long written about my frustrations with Ruby, so it feels pretty good to write for once about the aspects
of Ruby that I really enjoy. Keep hacking!

**P.S.** After writing this I realized I had already written a [similar article]({% post_url 2011-05-03-ruby-or-python %}) 14 years ago, but I had totally forgotten about it! Oh, well...
