---
title: Running Jekyll on Ruby 3.4
date: 2025-01-12 11:03 +0200
tags:
- Jekyll
- Ruby
---

Yesterday I've installed the newly released Ruby 3.4, but this caused an issue with Jekyll:

```
bundler: failed to load command: jekyll (/Users/bbatsov/.rbenv/versions/3.4.1/bin/jekyll)
<internal:/Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/3.4.0/rubygems/core_ext/kernel_require.rb>:37:in 'Kernel#require': cannot load such file -- csv (LoadError)
        from <internal:/Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/3.4.0/rubygems/core_ext/kernel_require.rb>:37:in 'Kernel#require'
        from /Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/gems/3.4.0/gems/jekyll-4.3.4/lib/jekyll.rb:28:in '<top (required)>'
        from <internal:/Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/3.4.0/rubygems/core_ext/kernel_require.rb>:37:in 'Kernel#require'
        from <internal:/Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/3.4.0/rubygems/core_ext/kernel_require.rb>:37:in 'Kernel#require'
        from /Users/bbatsov/.rbenv/versions/3.4.1/lib/ruby/gems/3.4.0/gems/jekyll-4.3.4/exe/jekyll:8:in '<top (required)>'
        from /Users/bbatsov/.rbenv/versions/3.4.1/bin/jekyll:25:in 'Kernel#load'
        from /Users/bbatsov/.rbenv/versions/3.4.1/bin/jekyll:25:in '<top (required)>'
```

At first I thought that updating to the latest version of Jekyll[^1] with
`bundle update` would be enough to address this problem, but it turned out that
wasn't the case.

It wasn't hard to guess that the `csv` library has been removed from Ruby's `stdlib` in Ruby 3.4, as the process to
move non-essential libraries out of the standard library has been ongoing for years now. So, I've added `csv` to
my `Gemfile`, did a `bundle install` and... got an error that `base64` was missing as well... Oh, well...

At the end of the day the solution to the problem is to add these 2 lines to your `Gemfile`:

```ruby
# TODO: Remove when this gets fixed in Jekyll
gem "csv"
gem "base64"
```

Don't forget to run `bundle install` afterwards.

Eventually those libraries will be made Jekyll runtime dependencies and when a
new version of Jekyll is released with this change, you can remove them from your
`Gemfile`. I've noticed there's already [a Jekyll
PR](https://github.com/jekyll/jekyll/pull/9736) to address this problem, but it might be
a while until it's merged and a new release is cut. Sadly, Jekyll is pretty
close to abandonware these days and every time something like this happens I'm
thinking that I should probably look more closely into the alternatives.

That's all I have for you today. Keep hacking!

[^1]: That's Jekyll 4.3 at the time I'm writing this.
