---
title: 'Why Emacs: Redux'
date: 2021-11-16 10:20 +0200
tags:
- Emacs
---

> redux <br>
> adjective
>   1. brought back; resurgent: <br>
>   the Victorian era redux.

A decade ago, I wrote an article titled [Why Emacs?]({% post_url 2011-11-19-why-emacs %}).
I came across the article a few days ago and I felt that given its "anniversary" it'd be nice to revisit it.

2011 was an interesting year for me. It was the year in which I [adopted Jekyll]({% post_url 2011-04-23-moving-to-jekyll %}) for this blog,
which resulted in my most productive year in terms of articles I wrote. It was also the year which marked the birth of my first OSS projects -
[Projectile](https://github.com/bbatsov/projectile) and [Emacs Prelude](https://github.com/bbatsov/prelude). It was also the final year of my
career as an (extra)ordinary programmer.[^1] Emacs was a huge part of my life back then, and the "Why Emacs?" article was an attempt to share my
passion for it with the world.

Fast-forward to the future and my life is very different today. The only programming I do these days is on my OSS projects, but I happen to have [a lot of those](/projects/). On the job most of the typing I do happens in Slack and Google Docs. My views on programming are very different from what they were back in 2011. One thing hasn't changed though - I still love Emacs and I'm quite passionate about it.

I still write short articles about Emacs over at <https://emacsredux.com>. I still try different Emacs packages from time to time, and try to improve my workflow and my configuration. As I got older I developed a lot of appreciation for the "less is more" mindset and I'm using significantly fewer Emacs packages than before. In particular I stopped using Emacs for things like email, rss, chat, twitter, etc. I'm digressing...

My main aim today is to share how I'd make the case for Emacs today. You'll notice that in the original article I spent a lot of
time focusing on technicalities:

- editing experience
- overall feature set (mostly how Emacs compares to popular IDEs)
- resource utilization

They are important, of course, but they are not the most important thing.

I also made some wild claim that using IDEs impairs your thinking. Clearly I was around peak Emacs zealotry back then! Today we have even more IDE features in Emacs and I think that's a good thing. In the end of the day Emacs has never been about enforcing one particular workflow - quite the contrary. Emacs is all about giving you choices!

> Lisp isn't a language, it's a building material.
>
> -- Ward Cunningham

Emacs is not really an editor either! I believe that Emacs is the ultimate editor building material. The out-of-the-box experience is kind of basic and somewhat weird, but I don't think that anyone should be using Emacs like this. You take this simple foundation, you shape and mold it to your taste and preferences and you end up with the best
possible editor for _you_ and you alone. That's the reason why you should consider using Emacs.

Of course, one can make pretty much the same argument for our arch rival vim, and perhaps even for the modern king of editors VS Code. I've always liked `vim`
and can't say anything bad about it, other than I still don't like Vimscript. For me, personally, the ability to extend Emacs easily with Emacs Lisp remains
its number one advantage over vim.[^2] For all the good things that VS Code has brought to the table (e.g. LSP), I'm still somewhat skeptical about it in the long run.
I don't like open-source projects that are de-facto owned by one company. There's always the conflict of interest between the company's agenda and the needs of its
users, and we know that the interests of the company usually come first.

I've also seen plenty of new editors rise and fall in the past 20 years - Komodo, TextMate, Sublime Text, Atom, etc. Emacs and vim are the only editors that stood the test of time, and I have a feeling they will be with us for decades to come. This means that an investment in them will be likely paying you dividends much longer than an investment in a newer editor or IDE.

On a more practical note I'll also mention that Emacs has improved a lot since the time of my original article. Here are a few highlights:

- Today we have more great Emacs packages than ever. I blame the rise of GitHub and MELPA for that. The old maxim "Emacs has a mode for it!" is truer than ever.
- Emacs's internal APIs are much more powerful than before. One can see that Clojure certainly influenced a few of them (e.g. `if-let`, `thread-first`, `seq.el`, `map.el`, etc).
- Emacs now supports a limited form of concurrency (introduced in Emacs 26).
- Emacs 27 started shipping a built-in JSON parser.
- Emacs has good support for LSP, which allows us to harvest the efforts of a lot of people for free. The gap between Emacs and VS Code and IDEs is a lot narrower than it used to be.
- Emacs 28 will bring massive performance gains with [native compilation](https://akrl.sdf.org/gccemacs.html).
- Emacs has fantastic support for programming in Clojure.[^3] I think the popularity of Clojure and the fact that Emacs was the only editor to support it well early on gave Emacs a massive boost around the time of my original article.
- Emacs 29 will bring us [proper support for emojis](https://lars.ingebrigtsen.no/2021/10/28/emacs-emojis-a-%e2%9d%a4%ef%b8%8f-story/)!

And that's only the tip of the iceberg. Emacs is definitely not standing still and both the core and the third-party package ecosystem are constantly
evolving and getting better. It's safe to say that Emacs is no sinking ship, regardless of its modest usage compared to the likes
of vim and VS Code.

So, why should you try Emacs in 2021?

- You're a curious person and a constant tinkerer who likes playing with vintage software. You happen to have a lot of extra time at home on your hands because of the pandemic and you want to make the best of it.
- You want to experience life outside the mainstream.
- You've always wanted to learn a bit of Lisp.
- You want to build an editor that's uniquely tailored to your needs and preferences.

Beware - Emacs is highly addictive and it might easily consume years of your life! I guess I've spent just as much time on
Emacs projects as I did on refining my Emacs configuration. Now, it's time to repeat after me the following chant 3 times:

> Emacs is power!
>
> Emacs is magic!
>
> Emacs is forever!

You're now ready to begin your life-long journey to Emacs mastery. Meta-x forever! In parentheses we trust!

[^1]: Or an individual contributor (IC), as this is known in some companies.
[^2]: You know me - I love Lisps!
[^3]: Just google for CIDER. :-)
