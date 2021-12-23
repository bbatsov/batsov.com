---
title: 'New Laptop: Lenovo Yoga Slim 7'
date: 2021-12-23 10:48 +0200
tags:
- Hardware
- Windows
- WSL
- Laptops
---

A couple of weeks ago I got myself a new laptop to replace my old [MacBook
12-inch from 2017]({% post_url 2021-11-02-the-macbook-redux %}).  As my
followers might remember I was planning to buy either an M1-powered MacBook Air
or the brand new MacBook Pro 14-inch with an M1 Pro. After much deliberation,
however, in the end I decided to go in a completely different direction and went
with Lenovo Yoga Slim 7, which is essentially my first non-Apple laptop since
2011.[^1]

What made me change my mind? As usual there were several factors at play:

* I hate International ISO keyboards (the ones with the short `Enter`), and by default all
MacBooks in Bulgaria are sold with those. A BTO configuration with an US ANSI keyboard usually takes
1-2 months to be delivered here.
* There are rumors that the MBA will be updated soon in a massive way, and it was
my front-runner. The MBPs are an overkill for my current needs and are a bit
heavy for my taste. Not to mention they are quite expensive, even by Apple's standards!
* In recent years I've been quite disappointed with the direction of macOS (more restrictive, more similar to iOS) and I've really enjoyed working on Windows 10 and WSL. One can argue that today Windows is a good enough Linux.
* I've never been too fond of Apple's keyboards, even the good ones. Also - they have no respect for the right Control key. :-)
* I've never had an AMD-powered laptop and I've always been a huge AMD fan. Everyone likes the underdog, right?
* I like playing with different gadgets and I'm definitely bored of MacBooks.

So, I decided to look for a Windows laptop and I had the following requirements for it:

* Thin and light, ideally under 1.3 kg (the weight of a MacBook Air)
* HiDPI (Retina) display with 16:10 or 3:2 aspect ratio
* Metal/Carbon chassis
* 1TB+ of storage, ideally user-upgradable
* 16GB+ of memory, ideally user-upgradable
* USB-C charger
* Comfortable keyboard with a right Control key and an US ANSI layout
* Good touchpad with Microsoft Precision drivers support
* Cool and quiet operation (my MacBook was getting extremely hot very quickly)
* Decent battery life (my MacBook could last for only 2 hours max at the end)
* Ryzen 5000-series CPU
* Price around 1000 EUR

I found many great Windows laptops during my search process, but I disqualified
most of them on either high price (e.g. most ThinkPads), 1080p-only display
(e.g. Asus Zenbook 13/14) or no AMD CPU option. In the end the Lenovo Yoga Slim
7 was as close as I could get to my dream machine (within my budget), so I went with it.

Here are the tech specs of the laptop:

| CPU | AMD Ryzen 7 5800U |
| GPU | AMD Radeon Vega 8 |
| Storage | 1TB SSD |
| RAM | 16GB LPDDR4x-4266 (soldered) |
| Display | 13.3 inch, QHD (2560x1600), 16:10 , IPS, Glossy, 300 nits |
| PSU | 65W USB-C |
| Weight | 1.2 kg |
| OS | Windows 11 Home |

You can find the detailed tech specs [here](https://psref.lenovo.com/syspool/Sys/PDF/Yoga/Yoga_Slim_7_13ACN5/Yoga_Slim_7_13ACN5_Spec.pdf).
The price I paid for the machine was 1100 EUR.

Some of you might have noticed that the Yoga Slim 7 is pretty similar to a MacBook
Air - it has an aluminum body, the same screen size and aspect ratio, and even
exactly the same resolution as the MBA. It has a lot smaller screen bezels, though, which
has allowed Lenovo to make a slightly lighter computer (1.2kg vs 1.3kg for the
MBA). All of those similarities were quite appealing to me, given my fondness for the MBA's
form factor and build quality.

I don't want to write a detailed review of the Lenovo Yoga, but having used it extensively for 2 weeks I'll share a few thoughts on it.
TLDR - it's a really great laptop, especially given its low price.

### Good Stuff

* Super fast (compared my old laptop at least) - that 8-core Ryzen CPU is a beast!
* Gorgeous display, although I would have preferred a non-glossy (matte) version of it.
* Relatively cool and quiet under normal workloads. It's no M1, but it's definitely coolest laptop I've ever owned. The laptop has a couple of power profiles you can choose from and in "battery saver" mode it's totally quiet and pretty cool.
* Excellent build quality - it feels almost as premium as a MacBook!
* Best laptop keyboard I've had in at least 10 years! Obviously it's still a laptop keyboard, but it has much better travel and feedback than Apple's new/old keyboards. I also love how the keycaps are shaped.
* Great trackpad - almost as good as Apple's trackpads.
* The charger is super light.
* I got a free USB-C to HDMI converter with the laptop.
* The webcam has support for Windows Hello (something like Face ID).

### Bad Stuff

* The battery life is just 6-7 hours, when doing my usual work (browsing, Slack,
  Zoom meetings, note taking, light programming in Emacs). It's still a big
  improvement for me, but I was hoping for 8+ hours of battery life. At least it
  supports fast-charging, so that's not a big deal.
* The fans can get somewhat noisy under heavy workloads (most of the time I cannot hear them, though).
* There was some bloatware preinstalled on the computer (e.g. McAfee and some mostly useless Lenovo apps).
* The laptop has only 3 USB-C ports and a headphone jack. No Thunderbolt support, but I don't really care about this.
* No privacy shutter for the webcam.
* The built-in speakers are so-so.

### Epilogue

I'm well aware that any M1-powered laptop will blow away my modest Lenovo Yoga, but I don't really care about this either. I still plan to get
some MacBook down the road, but I'm not in a rush and I'll likely wait for M2 to come out. My experience with first-gen Apple devices has never
been very good.

For me it's very important to have a bit of fun and diversity when it comes to computers and operating systems. I've been using Windows 10/11 and WSL2
for the past 15 months (on my desktop workstation) and I totally love my experience with them. It's amazing how far Microsoft have come from the days when they were trying to destroy Linux and
no respectable developer would consider Windows as their primary development platform. Well done, Satya Nadella!

I've learned that today developers definitely have some decent options if they
are looking for alternatives of Apple's walled garden (or running Linux on the
bare metal). Yeah, it'd be even better if we had good options for native Linux,
but I've pretty much lost all hope on that front and at this point I don't
really care. In many ways Windows + WSL is exactly the type of Linux desktop
experience that I always dreamed of. I no longer have to worry about driver and hardware compatibility, and I
have access to all the Linux tools that I need. The level of integration between Windows 11 and WSL is insane!

As usual, I'm writing this article from my
Emacs 29 running on WSL + Wayland and it's gorgeous! My Emacs experience today is much better than what I used to have
on macOS and it's pretty much the same as I what I had on Linux itself. I never saw this coming! (I doubt anyone saw this coming)

I also realized my dream of owning an AMD-powered laptop! As irrational as it gets, but that's the kind of person who am I. I'm definitely
pleased the with performance and thermal profile of the Ryzen 5800U and I cannot wait to see what AMD have in store for us with the upcoming
Zen 4 and the new 5nm fabrication process. Perhaps they'll be able to give Apple a run for their money?

My last non-Apple laptop was a ThinkPad T520 in 2011. Some issues with it prompted me to write my [infamous Linux rant]({% post_url 2011-06-11-linux-desktop-experience-killing-linux-on-the-desktop %}) and to switch to Macs. 10 years later I once again have a Lenovo laptop. Coincidence or providence? Time will tell!

[^1]: Admittedly, I briefly owned a HP Spectre x360 5 years ago. I love it as hardware, but back then WSL was way too immature for my needs and Linux didn't support well the Spectre's touch-screen.
