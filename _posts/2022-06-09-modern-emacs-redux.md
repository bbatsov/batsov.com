---
title: 'Modern Emacs: Redux'
date: 2022-06-09 10:29 +0300
tags:
- Emacs
- Meta
---

> New is always better.
>
> -- Barney Stinson

My recent article [Who Needs Modern Emacs?]({% post_url 2022-06-01-who-needs-modern-emacs %}) generated a bit of controversy. I've received a lot of feedback over email, [Reddit](https://www.reddit.com/r/emacs/comments/v2fjcd/who_needs_modern_emacs/), and [Lobsters](https://lobste.rs/s/nkea9j/who_needs_modern_emacs) and I feel the need to address some of that feedback.

Let's start with some comments that caught my attention:

> The difficulty with posts like this and the responses is that it all comes from the same echo chamber of established, long time, respected Emacs users/devs who already have a vested interest in keeping the status quo and having others adapt rather than face adapting themselves. I'm not saying they're wrong necessarily, but I think it's worth seriously exploring the positive effects of modernizing aspects of Emacs. And I think to see how it can help all us grey beards, we need look no further than the effects that Doom had on the Emacs community.

I totally loved this comment! To set the record straight - I'm not opposed to
modernizing Emacs, but rather to a particular notion of what modernization is
about. I'm concerned about meddling with the essence of Emacs (e.g. its Elisp
core) to chase after potential users that might never materialize. I'm also
tired of non-stop comparisons with editors like Sublime Text and VS Code and all
the people urging Emacs to try to emulate them.[^1] There's a popular saying that
always comes to my mind when I hear something like this:

> If we all think alike then nobody's thinking at all.

It is fine to have editors that deviate from the established norms. The fact that something is very popular doesn't make it great.
Having diverse alternatives is not a bad thing.

There's also the point that I've never seen an editor make a big comeback, which
makes me fairly confident that no amount of changes will likely make Emacs super
popular down the road. The narrative for modernization is always that this is going to bring
Emacs a lot of new users (and hopefully more contributors) and change its
fortunes, but I never bought this and I'm still not buying it.

Here's a bit more food for thought - how much resources have Microsoft, one of the biggest companies in the world, invested
in making VS Code a success? Is it realistic to expect that any community of volunteers can match those?

I'm also fairly certain that if our definition of innovation gets reduced to "copy features/behavior" from the popular editors of the day we would never
see new ground-breaking tools like `org-mode`, `magit`, SLIME, CIDER, etc.

So, instead of chasing wild geese, I'd rather focus on improvement efforts that make Emacs better, while preserving its essence.

> Doom (and before it Spacemacs) brought forth a ton of new (likely Vim) users willing to try out Emacs, and created a lot of momentum in new content that the whole Emacs community benefitted from. But before Doom existed, we had naysayers just like this post claiming that something like Doom wasn't necessary or needed. What we need is some visionary thinking that moves beyond this. We can continue to debate changing (or configuring) terms like buffer/kill/yank vs file/cut/paste or which themes are included or which keybindings - but in the end, to me it comes down to one thing - how do we continue to see Emacs grow and increase adoption?

True that. I've long been a believer that the Emacs distros are the most viable way to cater to the needs of different groups of Emacs users. That's why I've spent
years working on [Emacs Prelude](https://github.com/bbatsov/prelude). Distros provide us a good way to validate some ideas that might make it all the way to vanilla Emacs (e.g. new defaults, additional packages bundled and enabled by default, etc) and they make it possible to do things that will likely never make it in vanilla Emacs (packages depending on proprietary software, vim keybindings by default, etc).

It seems that many people have missed that particular point of mine - given how flexible Emacs is (being in essence editor building material), you don't need to push all changes you want to Emacs itself; you can achieve quite a lot externally in the form of a reusable configuration.

> For my $.02, I'd love to see a bit more movement from the devs in this arena, but more importantly a ton more movement from the Emacs distro community in this arena, to create something that feels a bit more out-of-the-box like Sublime Text or VSCode. We have some good starts with NANO, but that's not quite enough. I think a lot more people would be willing to try and stick with Emacs with a stater kit geared toward those users that simply had a file browser and file tabs, more familiar keybindings, modern terminology, more sane behaviors (stop unselecting regions all the time!!!), and some better default options. Learning to configure from there with elisp isn't the issue to adoption - the issue is how much elisp you have to start with to get to something that feels familiar.

No argument from me on this point as well, although I do think that unless you know Elisp you're unlikely to ever unlock the full potential of Emacs. Learning Elisp was
definitely game-changing for me as an Emacs user and I allowed me push Emacs much further than I ever thought was possible. If I can't truly bend Emacs to my will and I'm just an user of some configuration than a lot of it appeals just disappears for me.

> Emacs needs to be as easy to use out of the box as atom. Then, you can configure it endlessly. Just because it's building material doesn't mean it needs to arrive in shambles.

This comment made me smile in light of the news from yesterday that [Atom's development is coming to an end](https://github.blog/2022-06-08-sunsetting-atom/). Clearly being as modern as Atom is no recipe for long-term success. And yeah - the defaults can always be better and they typically get a bit better with each new Emacs release. It's a slow process, but it's already taking place.

> And maybe. The fact that emacs isn't modern isn't a feature. It's an indication that it hasn't kept up with the times, that bugs and slowness persist over decades, that there is truly a rotten broken core inside emacs that requires serious changes.

I think that once again people conflate being unfamiliar/different with being familiar/modern. I can think of no editor with Emacs's long history that has "kept up with the times" and ended up being more or less the same as VS Code. Yeah, a lot of problems in Emacs have lingered, but also a lot of problems have been addressed. Emacs is much better in many ways than it used to be just 10 years ago. People tend to ignore small improvements (because they quickly get used to them), but if you compare Emacs 22 with Emacs 28 I think you'll agree that the difference between them is night and day.

> I feel like many of the folks offering opinions here are outsiders to emacs. That is not a bad thing as we're discussing defaults and its impacts on usage of emacs. It might be good to view these criticisms as perceptions on the barriers to entry instead of from an experiential basis, however.

Spot on. People are constantly conflating concepts like "newcomer experience", "barrier to entry", "simplicity", "familiarity" and "modernity". It's quite possible for a tool to be modern in a way, yet so foreign to you that you'd consider it complex and dated. It's also quite possible for a tool to have great onboarding experience and still feel challenging and foreign.

> I've used emacs for about 4 years now so I have a bit of a different perspective than the folks that have been using it for 20+ years, but I also feel like I've invested enough time in the community and ecosystem to have a better vantage point than that of a new or curious user.
>
> I've seen a lot of progress in emacs and its ecosystem in my time using it. Emacs has gotten faster and many of the core features of popular packages have been incorporated into the main distribution (tabs, projects, advanced completion, etc). NonGNU Elpa has lowered some barriers for package developers to contribute to an official repository. I've seen a renewed focus on rediscovering and leveraging built-in functionality within emacs to simplify packages and benefit from the integration that provides. This is opposed to some of the more heavyweight packages that would largely re-implement built-in fuctionality. Take a look at packages like vertico, corfu, orderless, embark, consult, modus-themes, eglot, etc. The authors of these packages seem to be the next generation carrying the emacs torch forward. They have a great respect for what already exists, but are incorporating functionality that is influenced by modern UX and capabilities.
>
> I do not feel like emacs is declining. Since I've used emacs, I actually feel like it has become more popular. I think Doom emacs has had a big impact on bringing in new users. I personally use vanilla emacs so I do not necessarily believe that a big configuration package like Doom or Spacemacs is required to enjoy emacs. That said, I do feel like configuration packages are very useful and I would like to see more minimal versions. If a configuration could be based on emacs 28 or greater, I believe that a very capable and polished emacs configuration could be created with minimal or no extra packages beyond those that are built-in. Maybe some config(s) could catch on so much that it would make sense to provide them as an official option sometime in the future?

Yes! One thing I've been thinking for a while now is the idea of bundling some "configuration profiles" with Emacs, so that people can select one when they start Emacs for the first time. This seems to me like the most realistic way to appease the major groups demanding some changes in Emacs (e.g. you can have "traditional profile", "modern profile", "vim profile", etc). With most popular packages available in the official Emacs repos these days this should be fairly easy to achieve. Some mechanism to access 3rd party configurations (e.g. Doom & Prelude) directly would be a nice addition as well.

## Epilogue

Broadly speaking, all the feedback my original article received was in one of 3 categories:

- Agreement (mostly from experienced Emacs users)
- Partial agreement (mostly from experienced Emacs users)
- Strong disagreement (mostly from people who (I guess) don't use Emacs)

I've noticed that the people in the second and the third group have a very
different idea about what needs to be modernized. The second group cares mostly
about improvements to Emacs's internals (make it faster, better handling of long
lines, better garbage collector, fix some bugs, etc), while the third group
mostly wants Emacs to offer defaults and behaviors more aligned with what they
consider to be "modern editors".

I definitely felt that many people completely missed the message I was trying to convey in the original article. Many readers started to dissect the
random examples of "points of modernization" that I had listed, or argued that Emacs has to be more newcomer-friendly, which for me is something completely orthogonal
to Emacs being "modern". I still have to find an editor with a better built-in tutorial for newcomers (`C-h t`) or better built-in documentation (`C-h r`).
They are great, but they still require people to go through them and learn something new and it seems for many people that's a very tall order.

There were plenty of strange (to me) comments I got like this one:

> The fact that emacs is in such a sorry state doesn't help anyone. It's bad for new users that need to do so much work. It's bad for existing users because it drives so many people away and makes the community smaller.
>

"Sorry state" is obviously quite subjective, but I really doubt we're losing much of our existing userbase because of some lacking "modern" features from other editors.
Funny enough just a month ago there was a popular discussion titled ["Are we living in the Golden Age of Emacs?"](https://www.reddit.com/r/emacs/comments/ucldkz/are_we_living_in_the_golden_age_of_emacs/) and there a ton of people were praising the recent advancements that Emacs has made.

I think Emacs has never been better and the recent pace of improvements and innovation makes me very optimistic about its future. The amount of Emacs-related packages & activity on GitHub has been constantly growing for at least a decade. A few new Emacs blogs are born every year. The number of Emacs distros tailored for pretty much every taste keeps growing and growing. Emacs is not perfect, but it's definitely not some dying editor with a rotten core. It's the One True Editor and it keeps getting better and better every day.

So are we living in the Dark Age of Emacs or the Golden Age of Emacs? I know the answer for myself. `M-x` forever!

**P.S.** Btw, I'm never giving up [Meta, Super and Hyper](https://emacsredux.com/blog/2022/06/01/the-space-cadet-keyboard/)!

[^1]: I'm never tired of comparisons with `vim`, though.
