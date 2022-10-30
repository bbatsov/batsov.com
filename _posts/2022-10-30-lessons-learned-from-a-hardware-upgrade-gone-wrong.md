---
layout: post
title: Lessons Learned from a Hardware Upgrade Gone Wrong
date: 2022-10-30 09:31 +0200
tags:
- Meta
- Hardware
---

Yesterday I had a very frustrating start of my day for the most
unexpected of reasons - a (supposedly) simple CPU cooler upgrade for my
desktop computer turned into a nightmare. It was also a very educational
experience and reminded me about a few valuable lessons, I've learned
over the years.

First, a bit of backstory. I bought my current desktop PC in July 2020 and I've
been using the stock AMD Wraith Prism cooler that came bundled with my CPU ever
since. The cooler had a cool AMD logo and RGBs - I totally loved it early on. But it was
also a pretty noisy cooler and as time passed I grew progressively more and more
frustrated with it. After a lot of procrastination I finally decided this
weekend that enough is enough and I went forward with replacing it.  I opted to
go for the Arctic Freezer A35 - a simple cooler with no bells and whistles
that's known for its great value, quiet operation and easy installation.

10 am, Saturday morning. Upgrade o'clock! When I opened my PC's case I thought
the whole upgrade process was going to take something like 10-20 minutes. Boy,
was I wrong...

The first step was to remove my old cooler, which was supposedly a trivial thing
to do. I removed the latches that were holding it to the motherboard and
tried to pull it out, but the damn thing would not budge. I applied a bit more
force and it finally came out... together with my CPU that got ripped out of the
AM4 socket, as it was glued pretty strongly to the heatsink. I've replaced a lot
of coolers over the years, and this was the first time something like this
happened to me. To make things even worse - when I looked at the CPU more
closely I noticed that I had managed to bend something like 30 pins in the
process of pulling out the cooler. That's often a death sentence for CPUs and
there was a very high chance I'd need to get a new CPU. But I had a more
immediate problem - how to detach the CPU from the damn cooler?

They were glued together extremely well. I tried prying them apart with a knife
to no avail and I knew I needed some help to make progress. What do you do in
situations like these? You turn to YouTube, of course![^1] I searched there for
"detaching CPU from heatsink" and I got the brilliant advice to use a hairdryer
to heat them up for a few minutes and try to pry them apart when the thermal
paste had softened. Surprisingly, this actually worked!

Then I searched YouTube for "bent CPU pins" and there I watched a few people
fixing the bent pins with toothpicks, tiny screwdrivers, vises on workbenches,
etc. Some ideas were definitely way more hardcore than others! I spent the next
two hours carefully adjusting the bent pins with a toothpick and one of my
wife's painting knifes (the same one that finally pried the CPU from the
heatsink). I was extremely frustrated at the time, yet I needed to be as calm as
possible to perform this relatively complex task. By the end I was sweating like
a pig and my eyes couldn't focus properly on the individual pins. At last the
pin grid looked more or less aligned and I carefully slided the CPU back into its
socket. It clicked in place. I finally had some cause for optimism.

**Lesson 1:** Even the simplest change can go wrong, regardless of your knowledge or
your preparation for the task at hand. Changes always carry some degree of risk.

**Lesson 2:** There's a ton of knowledge on the Internet. If you have enough spare
time and a bit of patience you can probably learn to fix anything.

So, the CPU is (maybe) fixed. Time to install the new cooler! That should be
easy, right? I took a look at the instruction manual and I noticed something
weird - I have to screw 4 screws into the motherboard for the cooler's base, but
there's nothing to put the screws in... Just 4 empty wholes. I thought, hmm
that's weird - why do all the pictures and installation videos look differently
from my setup? At this point I thought that probably there was something on the
back of the motherboard that fell when I detached the original cooler and I
shook my case to check. I was right! Something was sliding behind the MB, but
this was also a very bad news - I needed to detach my MB completely to recover
and re-attached the back plate! When it rains, it pours, right?

**Lesson 3**: There are often fun surprises "in production". All the tutorials
had assumed you were assembling a new PC, so no one bothered to mention this
damn back plate. I thought I knew what to do with the cooler, but not knowing
(actually, having forgotten) about the plate made my task a lot more painful
than it needed to be.

At this point I was extremely angry and frustrated and I was close to thrashing
the entire PC. My morning was completely ruined, I was late for lunch with some
dear friends, my wife thought I had lost my mind getting so upset over a
computer. In my anger I struggled to perform even truly trivial tasks like
attaching/detaching the cowl of my cooler or plugging back in my GPU. I think I
almost broke the PCI-E slot at one point.

**Lesson 4:** When problems are mounting so does our frustration. But solving
problems in anger rarely works out well. I wish I had taken a step back and
calmed myself down mid-way through this ordeal.

Finally, I managed to get everything properly re-assembled, I plugged my PC back
into the peripherals and the power outlet and I pressed the "Power"
button. After a few seconds I saw the BIOS logo and I felt a tremendous amount
of relief. The day was saved. I had won!

Still, I kept thinking about what had happened through much of the day. In
particular, I was thinking about how similar the problems I faced with this
hardware upgrade were to the problems I was facing daily as a software
engineer. All the small changes that went wrong. All the frustrating debugging
sessions that lead nowhere, only to find the right solution in 5 minutes on the
next morning. The incomplete docs. The impossible scenarios that turned out to
be possible. All the crazy (and often undocumented) dependencies.

**Lesson 5:** Everything is more complex than it appears to be. If you believe
that something is truly simple, you should double check your assumptions about
the thing in question.

One final thought on the subject I had was about practice - as long as you
practice something you tend to forget about much of its complexity, just because
you're dealing with it all the time. Back in high-school I used to assemble the
computers of many of my friends and I was constantly fiddling with my own
computer. Back then I was not afraid of doing any hardware upgrade, just because
I knew a lot of the things that could go wrong and I was careful to avoid them
(e.g. I knew it was a good idea to warm up your CPU before detaching its
heatsink). Now, however, I had forgotten my basic things and this ended up
causing me a lot of problems. And the mounting problems caused me a great deal
of stress, just because I was painfully aware of how much I didn't know.

Anyways, I'm writing this blog post from my beloved desktop PC, which is
alive and well right now. The new cooler is pretty awesome, by the way. Finally,
my work desk is completely silent. And I'm pretty sure I'll never make any of
the mistakes I made today again, although give me 5 years and I might surprise
myself again.

**Lesson 6:** Sometimes it's OK to simply leave things as they are. In hindsight
perhaps the old cooler wasn't so bad to justify all the action from yesterday.
I do not regret the experience, though, and I'm grateful for the learning
opportunities it provided me with.

**Lesson 7:** We learn best by doing. Practice, practice, practice!

That's all I have for you today. Always be upgrading!

[^1]: You can also Google, of course, but most likely you'll end up back to YouTube as the hardware geeks are really into videos.
