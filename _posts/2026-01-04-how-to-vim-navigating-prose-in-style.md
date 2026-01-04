---
title: 'How to Vim: Navigating Prose in Style'
date: 2026-01-04 13:53 +0200
tags:
- Vim
---

I don't know about you, but I'm not using Vim solely for programming.
I also write documentation in it, plus most of my blog posts (like this one).

When dealing with prose (regular text), it's good to know a couple of essential Vim
motions:

- `(` and `)` allow you to move backward/forward in sentences
- `{` and `}` allow you to move backward/forward in paragraphs

Vim's check for beginning/end of sentence is not very precise, but it mostly gets
the job done. And because paragraphs are just blocks of text surrounded by
blanks lines that's handy in programming contexts as well.

The forward sentence motion positions the cursor on the first character in the
next sentence, or on the line after the paragraph (if the sentence is the last in
a paragraph). The backward sentence operates similarly - it goes to the first
character in the previous (or current) sentence.

The paragraph motions will take you to the empty lines before or after a paragraph.
Due to the simple definition of a paragraph in Vim, those are quite reliable. 

I guess in the world of motions like the ones provided by `easymotion` and
`sneak` you might be wondering if learning the rudimentary motions is worth it
all. In my experience it's never a bad idea to be able to use someone else's setup,
and the built-in functionality is naturally the smallest common denominator.

That's all I have for you today. Keep hacking!
