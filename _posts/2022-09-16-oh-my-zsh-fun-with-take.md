---
title: 'Oh My Zsh: Fun with Take'
date: 2022-09-16 11:49 +0300
tags:
- Tips
- Zsh
---

Today I've noticed that [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh) provides
one really useful command (implemented as a shell function) - `take`. I guess with a name like this it's not immediately obvious what the command does, but we'll get the general idea really quickly. The function has a super simple definition:

```console
function take() {
  if [[ $1 =~ ^(https?|ftp).*\.tar\.(gz|bz2|xz)$ ]]; then
    takeurl "$1"
  elif [[ $1 =~ ^([A-Za-z0-9]\+@|https?|git|ssh|ftps?|rsync).*\.git/?$ ]]; then
    takegit "$1"
  else
    takedir "$@"
  fi
}
```

Basically, depending on its argument `take` does one of 3 things:

- if it's a folder it creates the folder and takes you there:

```console
$ take this/is/a/new/folder
```

Essentially it's a combination of `mkdir -p` and `cd`, as you can see from the
implementation:

```console
# mkcd is equivalent to takedir
function mkcd takedir() {
  mkdir -p $@ && cd ${@:$#}
}
```

As you can see `mkcd` is an alias for `takedir`, which is what `take` internally
ends up calling.

- if it's Git repo URL it clones the repo and `cd`s into it:

```console
$ take https://github.com/ohmyzsh/ohmyzsh.git
```

The underlying function `takegit` looks like this:

```console
function takegit() {
  git clone "$1"
  cd "$(basename ${1%%.git})"
}
```

- if it's a link to some archive `take` will fetch it and extract it:

```console
$ take https://git.kernel.org/torvalds/t/linux-6.0-rc5.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   162  100   162    0     0    920      0 --:--:-- --:--:-- --:--:--   925
100  208M    0  208M    0     0  3607k      0 --:--:--  0:00:59 --:--:-- 3866k
```

The underlying function `takeurl` looks like this:

```console
function takeurl() {
  local data thedir
  data="$(mktemp)"
  curl -L "$1" > "$data"
  tar xf "$data"
  thedir="$(tar tf "$data" | head -n 1)"
  rm "$data"
  cd "$thedir"
}
```

Notice that the original archive will be deleted after being extracted.

At the end of the day we can say that `take` takes you where you want to be. I
hope you'll agree that `take` is one very useful command to know. I'll certainly
be using it a lot going forward! Keep hacking!
