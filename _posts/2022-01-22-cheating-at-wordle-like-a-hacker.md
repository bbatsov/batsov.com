---
title: Cheating at Wordle Like a Hacker
date: 2022-01-22 10:28 +0200
tags:
- Wordle
---

These day almost everyone on Twitter seems to be playing the word game [Wordle](https://www.powerlanguage.co.uk/wordle/), including me. You've probably seen many people around you sharing images like this one:

![wordle.png](/assets/images/wordle.png)

Most of the time the 5-letter words you have to guess are pretty easy, but occasionally there are words that are quite uncommon (at least for non-native speakers like me), that encourage to cheat in order to figure them out.

While there are all sorts of ways to cheat at Wordle, I think the most hackerish approach is to simply use a tool like `grep` on your English dictionary. Imagine that we're looking for words that start with `r` and end with `t`. Now we can quickly find all such words to come up with our next guess:

``` shellsession
$ grep ^r...t$ /usr/share/dict/words
react
rebut
refit
remit
reset
right
rivet
roast
robot
roost
```

That's some pretty basic `grep` usage here - we're basically using a regexp that will match all whole 5-letter words starting with `r` and ending with `t`.
And that's it.[^1] No more googling for queries like "5 letter words that end with t".
The Unix tools are the Way! Keep hacking!

[^1]: I think that dictionary file is installed by default on most Linux distros.
