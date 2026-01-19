---
title: 'How to Vim: Proper Ways to Escape'
date: 2025-06-03 08:26 +0300
tags:
- Vim
---

`ESC` (Escape) is one of the most central key in the world of
Vim. It takes you from Insert mode to Normal mode and it
also serves to interrupt operations in progress in Vim.
You'll be using it a lot!

Vim has a very long [history](). It's a product of a different era
where the keyboard layouts were quite different as well. Below
is a picture of the legendary ADM-3A, which Bill Joy famously used
when he created vi:

![adm3a-keyboard.jpg](/assets/img/adm3a-keyboard.jpg) 

The problem with ESC, however, is that on many modern keyboards
it's not the easiest key to reach. Fortunately, you have plenty of
options to improve the situation on that front. Let's go over them
in order of increasing complexity (and price).

## Use `Control + [` instead

Pressing `Control + [` is exactly the same as pressing ESC as far
as most terminals are concerned, and for many people (me included)
that's a lot easier to press than reaching for ESC at the top-left
corner of my keyboard. This becomes even more convenient if you
remap your mostly useless Caps Lock key to Control.

There's also `Control + c` that you can consider using. When
invoked in insert mode it behaves almost like ESC, with a couple
of caveats that you need to be aware of:

> Quit insert mode, go back to Normal mode.  Do not check for
> abbreviations.  Does not trigger the `InsertLeave` autocommand
> event.

## Add an extra keybinding to insert mode

In many languages certain sequences of characters are quite
uncommon (e.g. `xx`), so those are something you can bind
in insert mode to serve as exit. `jj` is a classic, with other
popular options being `ii` and `jk`.

```viml
inoremap jj <ESC>

" or alternatively
inoremap jk <ESC>
inoremap kj <ESC>
```

Those certainly work well in English, but with other languages your mileage will vary.
On top of this - in other contexts you'll still need to press ESC, Control+[ or Control+c.
I kind of like an uniform approach to the Escape problem.

## Use a dual-function `Control` key

Continuing the line of reasoning from above, we can go a bit
further and actually use a keyboard remapping tool to
have our (left) Control key behave as Control when held down
and as ESC when tapped (pressed quickly). That's actually what
I'm doing and I think that's the best possible setup.

These days I'm mostly using macOS and the popular Karabiner Elements
keyboard remapper. For it you'll need to add something like the
snippet below to your `karabiner.json` configuration file:

```json
{
  "description": "Control as Escape when tapped, Control when held",
  "manipulators": [
    {
      "type": "basic",
      "from": {
        "key_code": "left_control",
        "modifiers": {
          "optional": ["any"]
        }
      },
      "to": [
        {
          "key_code": "left_control"
        }
      ],
      "to_if_alone": [
        {
          "key_code": "escape"
        }
      ]
    }
  ]
}
```

For optimal results - make Caps Lock your left Control. 

## Use the right keyboard

Some keyboards are more vim-friendly than others. By this
I mean they place ESC where `~` normally is, which makes
pressing ESC a lot easier. A couple of classic examples are:

- HHKB (my current keyboard)[^1]
- Leopold FC660 (my former keyboard)

On top of this, the HHKB replaces Caps Lock with Control directly, so there's
one less remapping you have to do yourself. The keyboards I've mentioned
are on the expensive side, but they are legendary in the programming
community for a reason.

I'm not a fan of this approach, though, as I'm extremely used to the
placement of the `~` and it's commonly used in many programming
languages.[^2] My Leopold offered a middle way by having the `~` on
a different layer, but I still preferred to have it be a `~` by
default.

Of course, your mileage here will vary, based on your personal habits
and preferences. Some people swear by their keyboards with "programmer layout"
and that's fine.

## Epilogue

And that's a wrap! You certainly have some options to consider,
but for me a dual-function Control/ESC, placed where Caps Lock normally is,
is the way to go.

That's all I have for you today. Keep hacking!  

[^1]: My recent purchase of the HHKB was part of my inspiration to play again with Vim. I think it's clear that the keyboard was designed with Vim users in mind.
[^2]: My main issue with non-standard layouts is that I still have to use laptop keyboards and they keyboards of some colleagues/friends from time to time.
