---
title: 'Bad Ruby: Hash Value Omission'
date: 2022-01-20 14:09 +0200
tags:
- Ruby
- Language Design
- Code Style
---

Ruby 3.1 was recently released and it features one major syntax addition - you can now omit values in hash literals and keyword arguments in certain cases.[^1] The [release notes](https://www.ruby-lang.org/en/news/2021/12/25/ruby-3-1-0-released/) give the following examples:

- `{x:, y:}` is syntax sugar for `{x: x, y: y}`.
- `foo(x:, y:)` is syntax sugar for `foo(x: x, y: y)`.

Note that `x` and `y` are local variables that are in scope. My first reaction was "WTF???". My second reaction was to check how this made it to the language.

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

What it doesn't have, however, is any rationale behind the proposed change. Weird. I get that the idea is to emulate the syntax of ES6, but I find this to be quite misguided if the end result is reduced code readability.[^2] What's the point in copying features from other languages if they don't add value to Ruby itself? What's the point
of shaving off a few characters/keystrokes if the reader of the code might struggle to understand it?

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

Oh, well... Sadly, no one bothered to share those convincing arguments in the ticket itself. I'm disappointed that in the end of the day the result is more complexity in the core language that serves mostly to allow writing shorter, but harder to read code. We should never forget the following words of wisdom:

> Programs must be written for people to read, and only incidentally for machines to execute.
>
> -- Harold Abelson, Structure and Interpretation of Computer Programs

So, why do I feel that the new syntax makes things harder to read?

- at a glance `{x:, y:}` seems like a hash without values. Simple as that. Yeah, when I know about the new syntax I'll infer that there are some
locals with the same names as the keys that are currently in scope, but why bother with all of this? Explicit is (almost always) better than implicit when it
comes to code readability/maintainability!
- modern editors and IDEs are already quite smart - if you want to type less, such a problem can be solved at this level (e.g. IDEs would infer that you probably want to put `x` after `x:`).
- how can I jump the local variable at point (e.g. I want to inspect it) if there's no local variable at point?
- there are also some "fun" cases to consider:

``` ruby
# the shorthand somewhat surprisingly works with methods without params
def destroy (); puts "surprise"; end

{destroy:} # this will print "surprise"

# do you think this code behaved the same way before Ruby 3.1 and in Ruby 3.1?
# see - https://github.com/rubocop/rubocop/issues/10359
def some_method(token)
  user = double :user, id: 42, token:
  described_class.build(user)
end
```

Not to mention that this complicated further Ruby's already complicated parsers
and created a boatload of work for me and the other members of RuboCop's team.
Observing some of the interactions of the new syntax with other Ruby code
(e.g. method calls using the new syntax followed by conditional modifiers) left
me believing that the full implications of this were not carefully thought out.

I'm left pondering if I'd feel differently about the feature if it was
implemented in terms of a different syntax, that's not so ambiguous. Probably
yes.  Still, I've decided to give this syntax the benefit of the doubt and not
discourage it in the default RuboCop configuration (RuboCop introduced support
for Ruby 3.1 in version 1.24). As usual I'll leave it to the broader community
to figure out if something will become a Ruby idiom in the long run or not.

> Adopting new syntax is usually the least effective productivity improvement.
>
> And a language where the most easy path of change is such syntax additions is not providing the evolutionary path for meaningful improvements.
>
> -- Markus Schirp

This episode is just a continuation of the all the language changes in Ruby in recent years that I've found detrimental and that eventually prompted me to write my short essay [Ruby's Creed](https://metaredux.com/posts/2019/04/02/ruby-s-creed.html) in 2019. It's clear to me at this point that Ruby's direction hasn't changed and is unlikely to change. That makes me sad. I thought Ruby was all about programmer happiness.[^3]

[^1]: The feature is officially named "hash value omission". It's also known as "hash punning" after the similar "object punning" in JavaScript.
[^2]: There is a bit more rationale in [another ticket](https://bugs.ruby-lang.org/issues/17292).
[^3]: I have no doubt this change made someone happy, but I also wonder how many people did it leave as frustrated as me.
