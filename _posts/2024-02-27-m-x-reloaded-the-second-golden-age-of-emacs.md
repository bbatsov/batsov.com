---
title: 'M-x Reloaded: The Second Golden Age of Emacs'
date: 2024-02-27 09:08 +0100
tags:
- Emacs
---

> The people who live in a Golden Age usually go around complaining how yellow
> everything looks.
>
> -- Randall Jarell

Yesterday I wrote that I think Emacs is currently experiencing its (second)
Golden Age.[^1] Today I'll expand on this and I'll offer my perspective on
the reasons and factors that lead to it.

## The Road to Success

Yesterday someone mentioned on X the following:

> I think @emacs was kind of revived thanks to  VSCode (LSP) and Atom (tree-sitter).

While having access to shared editor infrastructure definitely helps, I think that's only a tiny part of the reasons for Emacs's "resurgence" in recent years.
I believe that numerous factors played a part in this and I'll try to list the ones I consider the most important.
I'll list those in no particular order in the sections that follow.

### GitHub

The creation of GitHub in 2008 was a revolution for OSS developers around the world.
It was a massive step forward from the days of SourceForge and EmacsWiki,
and a ton of Emacs projects were born on it.

I rarely contributed to OSS projects before the birth of GitHub, but drastically changed afterwards. GitHub was a big part of my OSS work and all of my projects are still hosted there. Its scale and reach allowed projects to connect with numerous potential contributors. Many of the most impactful Emacs packages
in recent history were born and popularized on GitHub.[^2]

I think it a bit ironic that a proprietary platform did so much for FOSS community, but I don't think anyone can argue with the results. I won't dwell much on this section, as I doubt that anything I can say about GitHub will be news to anyone.

### Clojure

Around the same time (in 2007) Clojure was created. The language generated a ton of interest in excitement in the programming
community when it was released and this translated into increased interest in Emacs. Why so?

Well, Emacs was the first editor to provide some decent support for Clojure programming - `clojure-mode` (a slightly modified version of `lisp-mode`)
and a Clojure adapter for SLIME (`swank-clojure`). CIDER (the successor of SLIME + `swank-clojure`) is still one of the most popular and
most powerful Clojure programming environments today.

I've noticed that many of the active contributors to the Emacs community were connected to Clojure in some way. Here are a few examples:

- Phil Hegelberg (a.k.a. `technomancy`), a prolific Emacs hacker. He had also made one of the most famous "intro to Emacs" back in the day - [Meet Emacs](https://www.pluralsight.com/courses/meet-emacs).
- Artur Malabarba of [Endless Parentheses](https://endlessparentheses.com/)
- Magnar Sveen (Emacs Rocks, `dash.el`, `multiple-cursors.el` and many others)
- Steve Purcell (author/maintainer of many Emacs packages, one of the driving forces behind MELPA)
- Me :-) Yeah, I was an Emacs user even before I got interested in Clojure, but much of passion for Emacs was coming from my passion for Lisps. Clojure gave me more reasons
to use spend time in Emacs and motivated to work harder on improving things in Emacs.

I'm reasonably sure that Clojure played a major role in the success of Emacs in recent years by attracting both new users and new contributors to the project.

There's more to the story, though. Clojure also had some impact on the core Emacs APIs, as macros like `if-let` and `thread-first` and libraries like `dash.el` and `seq.el` were influenced by it.

### MELPA

Sadly many Emacs packages were a total mess a few years ago. Many maintainers would never cut releases and users were just expected to use the latest version of the code.
This made the concept of a package repository that's distributing only "tagged" releases problematic. Not to mention that `package.el` was very new and wasn't popular enough
to encourage people to change their ways. Do you remember that many Emacs packages weren't using VCS and were distributed only on EmacsWiki? Fun times!

[MELPA](https://melpa.org) was a true revolution was it was released - a repo that was building snapshot release packages from a ton of sources (GitHub, person code repos, EmacsWiki). It was trivial
to add a package there and it was a "one and done" thing (unlike its predecessor Marmalade, when you had to upload each new release manually). You'd just submit a package recipe and MELPA will rebuild your package when needed.

Today MELPA hosts a whopping 6000 (!!!) Emacs packages and its' a true pillar of our community. For context - the official GNU ELPA and NonGNU ELPA repos are home to about 650 packages. I don't know about you, but I've discovered a lot of cool packages while browsing MELPA and I can't imagine the Emacs community without it.

### Killer "Apps"

Without going into much details:

- [org-mode](https://orgmode.org/)
- [Magit](https://magit.vc)
- [Auctex](https://www.gnu.org/software/auctex/) (probably the best way to author LaTeX documents)
- [SLIME](https://slime.common-lisp.dev/) (amazing Common Lisp programming environment)
- [Geiser](https://github.com/emacsmirror/geiser) (amazing Scheme programming environment)
- [CIDER](https://cider.mx) (Clojure development that rocks)

What would you add to this list?

### Spacemacs (and friends)

[Spacemacs](https://www.spacemacs.org/) showcased the full potential of Emacs to a lot of people and managed to convert many Vim users to Emacs. That's no small feat!
It wasn't the first Emacs distro by any means, but it was the most ambitious one at the time for sure. Its decision to leverage heavily `evil-mode` and to target
Vim users turned out to be a great choice![^3]

Emacs "Distributions" (a.k.a. "starter kits") in general were quite important to bridge the gap between the spartan default Emacs experience and what users of the other
editors would often expect. Today we have [many of them](https://github.com/emacs-tw/awesome-emacs?tab=readme-ov-file#starter-kit), but I still believe
that Spacemacs had the biggest impact on the community overall.

### Shared Editor Infrastructure (LSP, TreeSitter)

Emacs was getting a lot of criticism for lacking some of the advanced code analysis and refactoring capabilities of "modern" editors and IDEs.
Its adoption of the industry standards LSP (Language Server Protocol) and TreeSitter changes this and makes sure Emacs developers don't have to
invest time solving problems that are already solved elsewhere. I'm guessing that in the long run this will allow the Emacs maintainers to
focus more on the features that make Emacs unique and that's a great thing in my book.

The complete transition to LSP and TreeSitter won't happen overnight and we'll need years
to finish it. Still, the progress to date is nothing short of amazing. Exciting times ahead!

### Progressive Emacs maintainers

Richard Stallman stepped down as the head maintainer of Emacs in 2008, and was succeeded by a string of more progressive Emacs maintainers.
I think that Stefan Monnier, John Wiegly and Eli Zaretskii have helped create an environment that's collaborative and welcoming
to contributions.

Here we can highlight a few major milestones:

- Switching from CVS to Bazaar in 2008
- Switching to Git in 2014
- The adoption of `package.el` as the standard package manager (bundled with Emacs 24 in 2012)
- The creation of NonGNU ELPA (an official package repo with relaxed requirements for package inclusion)
- Native JSON support, native compilation, built-in LSP support, TreeSitter, etc

Probably there are other extremely important achievements resulting from the actions of the head maintainers that I've forgotten about. Feel free to mention those in the comments.

### People Looking for Something Different

> You can never be wrong by being yourself.

While the editors of the day (e.g. VS Code) get the job done they aren't necessary a great fit for everyone. There will always be those of us who are looking for something different for whatever reasons.

Given how similar most editors are today, those who are not happy with the status quo don't really have that many options. If you want an editor that's different from the mainstream and has a strong community - it's essentially a choice between Emacs and Vim.

And if you want to build an editor that's uniquely tailored to your preferences in every conceivable way - it's probably Emacs.

## Closing Thoughts

> Success occurs when opportunity meets preparation.
>
> -- Zig Ziglar

As you can see - that was quite the journey, that spans over 15 years. If I had pick one year that was the most important for what followed next it would
probably be 2008:

- The rise of GitHub and Clojure
- The change of the Emacs leadership
- The creation of Magit

What a year!

So there you have it - my take on the factors and the events that lead to Emacs's second Golden Age. Is there anything you'd disagree with?
Is there anything important that you think I've missed? Please, let me know in the comments!

## Epilogue

> You're here because you know something. What you know you can't explain, but
> you feel it. You've felt it your entire life, that there's something wrong
> with the world. You don't know what it is, but it's there, like a splinter in
> your mind, driving you mad. It is this feeling that has brought you to me.
>
> -- Morpheus ("The Matrix")

So, why should you try Emacs in 2024?

- You're a curious person and a constant tinkerer who likes playing with (insanely cool) vintage software.
- You want to experience life outside the mainstream.
- You've always wanted to learn a bit of Lisp.
- You want to build an editor that's uniquely tailored to your needs and preferences.
- You've got a lot of time to kill.

You're now ready to begin your life-long journey to Emacs mastery. Meta-x forever! In parentheses we trust!

[^1]: See <{{ site.url }}{% post_url 2024-02-26-emacs-dead-and-loving-it %}>
[^2]: I can only guess what the impact to Emacs would be if it's main development happened on a similar platform.
[^3]: Vim users should also check out [Doom Emacs](https://github.com/doomemacs/doomemacs).
