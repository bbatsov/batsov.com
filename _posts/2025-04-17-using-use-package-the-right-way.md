---
title: Using use-package the right way
date: 2025-04-17 15:50 +0300
tags:
- Emacs
- use-package
---

I recently wrote that [Emacs startup time doesn't matter]({% post_url 2025-04-07-emacs-startup-time-does-not-matter %}) and I got quite a lot of heat
for it. I totally stand by everything I said there, but I acknowledge that different
people have different use-cases and perspectives when it comes to this.

That's why I've decided to share with you the #1 tip to speed up your Emacs - defer the load
time of your packages. There are many ways to achieve this, but probably the easiest and most
popular these days is to use `use-package` to organize your package configuration.

Unfortunately, using `use-package` the right way is not very obvious and there's plenty
of incorrect information about it all over the Internet. Here's classic example of
a problematic `use-package` usage:[^1]

```emacs-lisp
(use-package projectile
  :init
  (setq projectile-project-search-path '("~/projects/" "~/work/" "~/playground"))
  :config
  ;; I typically use this keymap prefix on macOS
  (define-key projectile-mode-map (kbd "s-p") 'projectile-command-map)
  ;; On Linux, however, I usually go with another one
  (define-key projectile-mode-map (kbd "C-c C-p") 'projectile-command-map)
  (global-set-key (kbd "C-c p") 'projectile-command-map)
  (projectile-mode +1))
```

While this is technically speaking correct, the use of `:init` and `:config` means that the
package will be loaded immediately.

You might be wondering at this point when to use `:preface`, `:config` and
`:init` and you would be right to. As usual, the best answer is in the [Emacs
manual](https://www.gnu.org/software/emacs/manual/html_node/use-package/Best-practices.html),
and I'll try to expand on it below.

Where possible, it is better to avoid `:preface`, `:config` and `:init`.
Instead, prefer autoloading keywords such as `:bind`, `:hook`, and `:mode`, as
they will take care of setting up autoloads for you without any need for
boilerplate code.  For example, consider the following declaration:

```emacs-lisp
(use-package foo
  :init
  (add-hook 'some-hook 'foo-mode))
```

This has two problems.  First, it will unconditionally load the package
`foo` on startup, which will make things slower.  You can fix this by
adding `:defer t`:

```emacs-lisp
(use-package foo
  :defer t
  :init
  (add-hook 'some-hook 'foo-mode))
```

This is better, as `foo` is now only loaded when it is actually needed
(that is, when the hook `some-hook` is run).

   The second problem is that there is a lot of boilerplate that you
have to write.  In this case, it might not be so bad, but avoiding that
was what `use-package` was made to allow.  The better option in this case
is therefore to use `:hook`, which also implies
`:defer t`.  The above is thereby reduced down to:

```emacs-lisp
(use-package foo
  :hook some-hook)
```

   Now `use-package` will set up autoloading for you, and your Emacs
startup time will not suffer one bit. Nice, ah?

So, let's return now to our original example and think how we can improve it.
Our first instinct is probably to do something like:

```emacs-lisp
(use-package projectile
  :defer t
  :init
  (setq projectile-project-search-path '("~/projects/" "~/work/" "~/playground"))
  :config
  ;; I typically use this keymap prefix on macOS
  (define-key projectile-mode-map (kbd "s-p") 'projectile-command-map)
  ;; On Linux, however, I usually go with another one
  (define-key projectile-mode-map (kbd "C-c C-p") 'projectile-command-map)
  (global-set-key (kbd "C-c p") 'projectile-command-map)
  (projectile-mode +1))
```

This is going to be useless, though, as `projectile-mode` will run at the end of the `:config` block
forcing the package to be loaded. We can make things a bit better if we instruct the mode to be loaded
only after Emacs's initialization has finished:

```emacs-lisp
(use-package projectile
  :defer t
  :init
  (setq projectile-project-search-path '("~/projects/" "~/work/" "~/playground"))
  :config
  ;; I typically use this keymap prefix on macOS
  (define-key projectile-mode-map (kbd "s-p") 'projectile-command-map)
  ;; On Linux, however, I usually go with another one
  (define-key projectile-mode-map (kbd "C-c C-p") 'projectile-command-map)
  (global-set-key (kbd "C-c p") 'projectile-command-map)
  :hook (after-init . projectile-mode)
```

But ideally we should get rid of `:defer`, `:init` and `:config`:

```emacs-lisp
(use-package projectile
  :custom (projectile-project-search-path '("~/projects/" "~/work/" "~/playground"))
  :bind-keymap (("C-c C-p" . projectile-command-map))
  :hook (after-init . projectile-mode)
```

Much better!

Of course, you can't always achieve this clean setup, but if you try you'll get there
90% of the time!

So, to recap:

- Avoid the use of `:init`, `:config` and `:mode` whenever possible
- Most of the time you don't need to use `:defer`
- Usually you should aim to activate minor modes only after Emacs's main initialization has finished (otherwse `:defer` is pointless)

I'll add here that less is more, even in Emacs. It's usually a good idea to review the list of packages in your `.init.el` every few months
and trim it from time to time. I used to be the type of guy who loads 100+ packages in their config, but these days I limit myself only
to packages really improve my workflows.

That's all I have for you today. Feel free to share other `use-package` tips in the comments!

[^1]: From my own `init.el` - after I all I told you I don't really care about the startup time. :D
