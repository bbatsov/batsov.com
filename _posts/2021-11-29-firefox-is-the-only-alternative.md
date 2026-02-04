---
title: Firefox is the Only Alternative
date: 2021-11-28 10:54 +0200
tags:
- Firefox
- Browsers
---

Supposedly today we have a lot of browsers to choose from - Google Chrome, Safari, Microsoft Edge, Firefox, Brave, Opera, Vivaldi, etc.
Having choices is a good thing, right? Nobody wants to relive the time of almost complete Internet Explorer domination again.
Unfortunately our choices are significantly fewer than they seem to be at first glance, as Chrome and Safari (thanks to the iPhone) totally dominate
the browser landscape in terms of usage and almost all browsers these days are built on top of [Chromium](https://www.chromium.org/Home), Google's open-source browser project.
Funny enough even Edge is built on top of Chromium today, despite the bitter rivalry between Google and Microsoft. What's also funny is that
Chrome and Safari control about 85% of the browser market share today, and Microsoft's Edge commands only about 4%:

<div id="all-browser-ww-monthly-202010-202110" width="600" height="400" style="width:600px; height: 400px;"></div><!-- You may change the values of width and height above to resize the chart --><p>Source: <a href="https://gs.statcounter.com/browser-market-share">StatCounter Global Stats - Browser Market Share</a></p><script type="text/javascript" src="https://www.statcounter.com/js/fusioncharts.js"></script><script type="text/javascript" src="https://gs.statcounter.com/chart.php?all-browser-ww-monthly-202010-202110&chartWidth=600"></script>

Of course, basing their products on Chromium makes a lot of sense for browser vendors - it's significantly cheaper for them not to have to invest in their own engine, participate
in discussions about web standards and not have to deal with compatibility complaints. A long time ago I was an Opera user for a while and despite loving
the browser overall, I was often frustrated that some sites were broken with it, simply because few developers would bother to support a niche browser.
Now when Opera is based on Chromium that's a problem that solves itself and Opera's team can focus solely on their browser's UI/UX. Let Google do all the
heavy lifting for them...

In the meantime every competitor that Google converts to Chromium strengthens
their position and increases the influence they have over the future of web
standards. Even today they can do pretty much everything they want to, but things
can get even worse if they get to 95%+ of Chromium market share. At the height of
its power (2002-2004) Internet Explorer held about 95% of the browser market. What a
"wonderful" time that was!

Here's where we are today:

| Browser       | Based on Chromium | Open-source | Market Share (desktop + mobile) |
| ------------- | ----------------- | ----------- | -------------|
| Chrome  | Yes      | No | 64.7% |
| Chromium | Yes | Yes | - |
| Edge  | Yes      | No | 4.0% |
| Brave | Yes | Yes | - |
| Vivaldi | Yes | No | - |
| Opera | Yes | No | 2.4% |
| Safari | No | No | 19.0% |
| Firefox | No | Yes | 3.7% |

I don't know about you, but I find the list above quite depressing. We're
essentially left with only two major open-source browsers (Chromium and
Firefox), and knowing that one of them is controlled by Google makes it clear
that it's not the typical bazaar-like OSS project. We've gotten to the point
that Chromium-based browsers are so common that developers just stopped to bother
supporting other browsers. Last week I saw one site that directly didn't support
Firefox (it displayed a message I should switch to Chrome) and another where the
sign in was broken on Firefox, but worked on Chrome-like browsers. Soon Google
are going to be in complete control of web standards, unless something
drastically changes.  Do you want the future of browsing to lie solely in the
hands of the biggest advertising business on Earth?  I'm pretty sure that I
don't.

One can argue that Safari is an alternative to Chrome, given the popularity of
Apple's devices and the massive resources of the company. I guess I can agree to
some extent, but from my perspective Apple is just another company that's more
focused on advancing their own agenda than the well-being of their users or open
web standards. I do give them a lot of credit for helping rid the world of
Flash, though.

For me Firefox is the only alternative to a complete Chrome hegemony in the sense that:

- it's open-source in the real sense (a project that's truly community-driven)
- it has a great track record of fighting for its users and for a better
  Internet. Chrome started with a great narrative when it was facing an uphill
  battle with Internet Explorer, but it has almost become the tyrant it sought
  to displace. I wonder if every revolution is doomed to finish like this.
- it's home to the last major rendering engine, that's not derived from WebKit (namely Gecko/Quantum)[^1]
- it's the only major browser that lobbies on behalf of regular users (people like you and me) when it comes
to implementing new web standards. Everyone should take a moment to visit [this page](https://mozilla.github.io/standards-positions/), detailing the position of Firefox's
developers on numerous specifications submitted to standards bodies like the IETF, W3C, and Ecma TC39. You'll notice that many of them are considered "harmful".

If you're using Chrome and you don't realize how gradually the hero has become the villain I don't blame you. I was one of those people myself. I've started using
Firefox in 2004 a bit before version 1.0 was released. I've switched to Chrome in 2009, right after the beta version for Linux was announced. Back then Firefox
was a huge resource hog and Google were still the company that "does no evil". It's almost surreal how things have changed... I never stopped using Firefox completely (it was always my "backup" browser), but for over a decade I was firmly in the Chrome camp. In recent years I've been bothered more and more that we're heading for browser duopoly and
this made me reconsider my life choices and make Firefox my main browser again. It's a decision I don't regret at all, despite the occasional broken sites that
I encounter. Enduring a bit of pain is the least that we can do to support a good cause. Reporting those breakages to the developers of the respective sites is not
a bad thing either.

Don't get me wrong, though - using Firefox is not painful at all. Quite the
contrary! Firefox has made a lot of progress in recent years, despite the
struggles of the Mozilla
Corporation and the layoffs there. [Quantum](https://blog.mozilla.org/en/mozilla/introducing-firefox-quantum/)
was a massive step forward in terms of performance, and recently Firefox also
got a significant (although somewhat controversial) facelift. Today the browser
is sleek, fast and resource-efficient. Problems like the ones I outlined above are
relatively rare. In fact I even had one case where a web application (Fastmail) would get
stuck on every Chromium browser after a few hours, but would work flawlessly on
Firefox.

Sadly, despite all of its improvements Firefox's share of the desktop browser
market has been slowly going down from 32% in 2009-2010 to only 8% today.[^2] Clearly, that
market share was lost solely to Chrome & co.[^3] I can attribute this decline to
various factors:

- Google's massive development resources and marketing machine.
- Most people not thinking about the long-term ramifications of ending up in a
  market with a single vendor in it.
- Firefox losing its status of a shiny new thing over the years.
- Mozilla's inability to capitalize on the popularity of Firefox in the past. I think almost all of their revenue came from a search deal with Google.

On top of this we have the rise of mobile computing where Google and Apple both
have their browsers firmly entrenched and competition is virtually
non-existing. Technically speaking alternatives do exist, but most people don't
care about them and simply use whatever default browser came with their
phone/tablet. I sometimes wonder whether the rise of smartphones contributed to
Firefox losing traction.  Given how convenient it is to sync data automatically
between all your browsers probably it did.[^4] In the past Microsoft was
eventually forced to ask people if they want to use a browser other than
Internet Explorer during Windows's setup.[^5] I'm not sure if this helped
Firefox and Chrome grow their market share, but I doubt it hurt. It'd be nice to
see something similar on the mobile front at some point, but I doubt that will
happen.

Notice that I haven't even mentioned privacy today. Obviously for a proprietary
browser it's pretty hard to assess how it (mis)handles your browsing data.  I'm
fairly certain that you can have a privacy-respecting browsing experience based
on Chromium (we have Brave), but for me that has never been the problem.  The
problem is having real competition and real alternatives. As it stands today we
have a browser duopoly (Chrome and Safari) and pretty much a monopoly outside of
Apple's walled garden. Firefox remains our only real alternative. Remember that
next time you decide you don't care about Chrome slowly eating the web.

[^1]: Chrome's Blink engine was forked from WebKit after Google has some falling out with Apple. Seems companies are really struggling to work together on such supposedly open-source projects.
[^2]: Its usage on mobile devices is insignificant today.
[^3]: See [this video](https://www.youtube.com/watch?v=s9pvB4N99sQ) that shows nicely the shift in the browser market sentiment.
[^4]: These days I'm using Firefox on my iOS devices and it works pretty well. Granted, it's not the real Gecko-based Firefox, but it works just as well as Safari and I find it more convenient to use.
[^5]: At least in the European Union. See <https://www.theguardian.com/technology/2010/mar/02/microsoft> for more details.
