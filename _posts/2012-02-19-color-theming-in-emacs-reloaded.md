---
layout: single
title: "Color Theming in Emacs: Reloaded"
date: 2012-02-19 12:17
tags:
- Emacs
---

## Prelude

If there is something that people love as much as tweaking their
editing configurations it's probably the selection of color themes. A
good color theme can make your work much more pleasant and a bad one
that literally impair your vision. It's a fact of life that I'm a firm
supporter of low-contrast color themes with dark backgrounds - I find
them easy on the eyes and I feel that they don't strain the eyes as
much as most themes. I've even ported a couple of popular themes to
Emacs - [Zenburn](https://github.com/bbatsov/zenburn-emacs) and
[Solarized](https://github.com/bbatsov/solarized-emacs).

In this short article we'll see how color theming has changed in Emacs
24 and I'll share with you a few tips on theme creation and
distribution.

<!--more-->

## Color Theming in Emacs 24

Prior to Emacs 24 the most popular way to incorporate custom color
themes into Emacs was the
[color-theme package](http://www.emacswiki.org/emacs/ColorTheme). While
it usually got the job done it had some problems that I won't be
discussing here and more importantly - it's a third-party package,
that's not part of Emacs proper.

[Emacs 24]({% post_url 2011-08-19-a-peek-at-emacs24 %})
finally introduced a new standard way of dealing with color themes
(based on Emacs's built-in customize facility). While it doesn't have
a proper name (as far as I know) it's commonly referred to as the
`deftheme` facility, since `deftheme` is the name of the macro you'd
use to create such a theme. ( `deftheme` has actually been around
since Emacs 23, but it was heavily improved in Emacs 24 )

Emacs 24 comes with a selection of built-in themes that you can choose
from, so you're no longer bound to the default theme (which I find
quite ugly). To choose a new theme just do a `M-x load-theme` (tab
completion is available for the names of the available themes). At
this point you can give the command a try with the `tango` theme. If you
like a theme so much that you'd want to use it all the time you can
put in your Emacs configuration (`.emacs` or `init.el` for instance) like this:

``` elisp
(load-theme 'theme-name t)
```

If you'd like to return to the default-theme just do a `M-x disable-theme`.

How do you create a `deftheme` theme? Quite simply actually - just do
a "M-x customize-create-theme". You'll be presented with an UI
prompting you for a theme name, description and faces. After you save
the theme a file called `name-theme.el` will be written on your
filesystem. Here's its skeleton:

``` elisp
(deftheme demo
  "Demo theme")

(custom-theme-set-faces
 'demo
 ;;; list of custom faces
 )

(provide-theme 'demo)
```

There was also an online theme generator
[here](http://elpa.gnu.org/themes/), but it seems to be down at the
moment.

Personally I dislike customize a lot, so when I needed to create a
Emacs 24 theme for the first time I've just opened the source code of
the built-in tango theme and used it as a reference.

Once you've created the new theme you'll have to drop it in a folder
that's on the `custom-theme-load-path`. I'd suggest the following:

``` elisp
(add-to-list 'custom-theme-load-path "~/.emacs.d/themes")
```

If you're an [Emacs Prelude](https://github.com/bbatsov/prelude)
user you're already covered. This folder exists and is automatically
added to `custom-theme-load-path` by Prelude, so all you have to do is
drop there the themes you'd want to try out.

You may find the
[rainbow-mode](http://julien.danjou.info/software/rainbow-mode) useful
when developing color themes. If fontifies strings that represent
color codes according to those colors. The mode is known to be a great
addition to css-mode, but I find it very helpful with color theme
development as well. It's also included (and enabled) in Prelude by
default. Here you can see it in action.

![rainbow-mode](/assets/images/rainbow-mode.png)

The Emacs package manager `package.el` (formerly known as ELPA) is
gaining a lot of popularity lately and the community
[Marmalade](http://marmalade-repo.org/) repository already houses a few
Emacs 24 themes that you can install from there. If you're developing
a theme that you'd like to submit to Marmalade it's imperative that
the theme modifies the `custom-theme-load-path` in an `autoload` -
otherwise it won't be of much use. Add the following snippet (or
something similar) before the `provide-theme` line if your custom
theme:

``` elisp
;;;###autoload
(when load-file-name
  (add-to-list 'custom-theme-load-path
               (file-name-as-directory (file-name-directory load-file-name))))
```

I'd also advise you follow the proper naming convention
`name-theme.el` so that it's apparent that your theme is `deftheme`
compatible.

Oh, and one more thing - porting themes from color-theme to deftheme is
really simple (just have a look at the old and the new version of
Zenburn in its repo), so you should really consider porting all the
themes you maintain to `deftheme`.

# Epilogue

Color theming in Emacs has never been easier. It's time to kill
`color-theme` once and for all. If you've ever developed a color theme
for it I urge you to convert it to the `deftheme` format and upload it
to Marmalade.

And if you've never developed a color theme for Emacs because you were
afraid it was too hard - now is the time to do it.
