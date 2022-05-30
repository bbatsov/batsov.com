---
title: Having Fun with Custom Domains for Email
date: 2022-05-30 10:26 +0300
tags:
- Meta
- Email
---

A reader ask me recently how do I use custom domains for my email needs
and I promised to write a short blog post about this. Promised fulfilled!

So, why do you need a custom domain for email in general? There a few reasons:

- Most importantly a custom domain keeps you independent from any particular
email vendor (e.g. Gmail, Yahoo, etc). If you decide to change Gmail with
something else normally you'd spent a lot of time updating your email in various
services and you'll also learn that on some services you can't really change
your email (e.g. half the airlines I've ever used). You'll also be in quite a
few contact books with your legacy address. That sucks! But if you weren't using
a vendor specific email domain (like `gmail.com`) then such moves between email
vendors become trivial, as your email address (e.g. `john@smith.com`) always
stays the same. You just import your messages in the new vendor, update your DNS
settings and magic happens.
- Vanity. It just looks cool to have an email address like `firstname@lastname.net`. Or `somethingsmart@funnydomain.net`.
- Better inbox management - if you're involved in many projects/activities you might want to be using different emails for all them and then have some routing/sorting based on this.
- You get a fancy email login page like `https://mail.yourdomain.com`.
- You're running a business and you need to present yourself in a professional light. I guess this doesn't need any further explanation.

All those points were important to me to some extent. Here's how I make use of custom domains in general:

- Historically I've used the default domains of several email vendors I regretted this every time. To this day I'm using Gmail to some extent just because for some services
it's very painful/impossible to change my email. Yeah, obviously forwarding solves this to some degree, but I don't really want to be gracing Google with my data at all. And eventually I'll do something about this. These days I use pretty much everywhere my personal or project domains (e.g. `batsov.com`, `metaredux.com`, `nrepl.org`, etc).
- I'm often filing incoming mail based on the domain it was addressed to (I love to sort my email diligently in thematic folders)- e.g. I use `batsov.dev` for all programming-related services that I use and the custom domains of my projects for communication around them (e.g. inquiries related to Meta Redux use an email with the domain `metaredux.com`). Obviously this can be emulated to some extent even with a single address and plus aliases (e.g. `nemo+dev@gmail.com`), but I've never been fond of such an approach. Those addresses are fine for auto-filing email from services, but are not something I'd be giving out to humans as they simply look weird. Which brings me to my next point.
- For every custom domain I've got a bunch of aliases for even finer control over the filing of the emails that I receive - some of those are related to subscriptions to services, some are different purpose addresses for communication with humans (e.g. `feedback@project.net`, `bugs@project.net`, etc.).

Today I have several vanity domains for my name (e.g. `batsov.net` and `bozhidar.net`), my blogs (e.g. `metaredux.com`) and my OSS projects (e.g. `cider.mx`) that I use for email purposes as well. Probably I went overboard on that front, as I usually do, but that's fine.[^1]

All the custom domains that I use for email purposes are attached to my Fastmail account, that I share with some family members (e.g. my wife, my brother, etc) as I happen to be the admin of the "family domains". Fastmail also has a cool feature [for redirecting URLs associated with your custom domains](https://fastmail.blog/historical/custom-dns-and-url-redirection-for-your-domain/), that I use extensively. For instance:

- <https://batsov.dev> redirects to https://github.com/bbatsov
- <https://batsov.net> redirects to https://batsov.com
- <https://emacs.batsov.net> redirects to https://emacsredux.com
- <https://resume.batsov.net> redirects to my LinkedIn profile and so on

Saves me a bit of typing and helps with my vanity issues as well!

On Fastmail I don't use the `@fastmail.com` addresses for anything except [Masked Emails](https://www.fastmail.help/hc/en-us/articles/4406536368911-Masked-Email) for services I don't really care about (and I'm somewhat skeptical of who they'll sell my address to). I don't really need to do this, as I have access to unlimited aliases anyways, but I like the automated integration this feature has with 1Password (another tool that I use extensively). Pro tip - never give your real email address to a sketchy service!

I don't use custom domains for services like HEY and ProtonMail, as I think that their domain is half the value of the service (or rather what this domain stands for - e.g. security or a love for top-posting). People expect to see domains like `@hey.com` and `@proton.me` and I don't want to deny them seeing them.

And that's a wrap. I've promised a short article and a short article it is! As usual I'd love to hear your feedback and how you're using custom domains yourselves.
My inbox and my Twitter are always open!

[^1]: The fact that every year there are more and more top-level domains to choose from doesn't help either!
