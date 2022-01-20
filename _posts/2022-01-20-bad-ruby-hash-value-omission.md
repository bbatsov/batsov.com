---
title: 'Bad Ruby: Hash Value Omission'
date: 2022-01-20 14:09 +0200
tags:
- Ruby
- Language Design
- Code Style
---

Ruby 3.1 was recently released and it features one major syntax addition - you can now omit values in hash literals and keyword arguments in certain cases. The [release notes](https://www.ruby-lang.org/en/news/2021/12/25/ruby-3-1-0-released/) give the following examples:

- `{x:, y:}` is syntax sugar for `{x: x, y: y}`.
- `foo(x:, y:)` is syntax sugar for `foo(x: x, y: y)`.

Note that `x` and `y` are local variable that are in scope. My first reaction was "WTF???". My second was to check how this made it to the language.

The [feature request ticket](https://bugs.ruby-lang.org/issues/14579) has a few more usage examples:

``` ruby
x = 1
y = 2
h = {x:, y:}
p h # => {:x=>1, :y=>2}

def login(username: ENV["USER"], password:)
  p(username:, password:)
end

login(password: "xxx") # => {:username=>"shugo", :password=>"xxx"}
```

What it doesn't have, however, is any rationale behind the proposed change. Weird. I get that the idea is to emulate the syntax of ES6, but I find this to be quite misguided if the end result is reduced code readability. What's the point in copying features from other languages if they don't add value to Ruby itself?

I've noticed that everyone reacted negatively to the proposal initially, including [Matz](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/86123):

> I prefer this syntax to #11105, but this introduces a Ruby-specific syntax different from ES6 syntax.
>
> Besides that, I don't like both anyway because they are not intuitive (for me).
>
> -- Matz

This was in 2018. 4 years later, however, he changed his mind:

> After the RubyKaigi 2021 sessions, we have discussed this issue and I was finally persuaded.
> Our mindset has been updated (mostly due to mandatory keyword arguments).
> Accepted.

Oh, well... Sadly, no one bothered to share those convincing arguments in the ticket itself. I'm disappointed that in the end of the day the result is more complexity in the core language that serves mostly to allow writing shorter, but harder to read code.

This episode is just a continuation of the all the language changes in Ruby in recent years that I've found detrimental and that eventually prompted me to write my short essay [Ruby's Creed](https://metaredux.com/posts/2019/04/02/ruby-s-creed.html) in 2019. It's clear to me at this point that Ruby's direction hasn't changed and is unlikely to change. That makes me sad. I thought Ruby was all about programmer happiness.
