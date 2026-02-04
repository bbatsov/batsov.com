---
title: A Basic Helix Setup for JavaScript
date: 2025-02-21 10:31 +0200
tags:
- Helix
- JavaScript
---

Lately I've been a bit bored with my tried and true development tools and I've been
playing with some alternatives. One of the most interesting tools I came across is
the [Helix](https://helix-editor.com/) editor. It's inspired by vim and you can think of it a simpler (and less configurable)
vim relative. I'll probably write more about Helix in the future, but today I simply want to
share how easy it is to set up Helix for JavaScript programming.

Helix has built-in support for all the modern goodies like LSP and TreeSitter, so all you need to do
is install the right tools and you're ready for action. I'd suggest starting with checking
the output of the `hx --health` command:

```console
$ hx --health javascript
Configured language servers:
  ✘ typescript-language-server: 'typescript-language-server' not found in $PATH
Configured debug adapter:
Binary for debug adapter: '' not found in $PATH
Configured formatter: None
Tree-sitter parser: ✓
Highlight queries: ✓
Textobject queries: ✓
Indent queries: ✓
```

Okay, it seems I need to install `typescript-language-server`. That as easy as running
the following command:

```shell
npm install typescript-language-server -g
```

Let's check `hx --health` again to see if that got the job done:

```console
$ hx --health javascript
Configured language servers:
  ✓ typescript-language-server: /Users/bbatsov/.nvm/versions/node/v22.13.0/bin/typescript-language-server
Configured debug adapter:
Binary for debug adapter: '' not found in $PATH
Configured formatter: None
Tree-sitter parser: ✓
Highlight queries: ✓
Textobject queries: ✓
Indent queries: ✓``` 
```

Seems we're in business! But when I tried editing JavaScript code in Helix for some reason
the LSP integration was not working. Fortunately for us, Helix has a great debugging facility
in the form of `helix -v` which writes a log file to `~/.cache/helix/helix.log`. All the
communication with the LSP servers gets logged there. You can
quickly access it from Helix with the `:log-open` command. There I quickly discovered the
problem:

> 2025-02-21T10:18:32.462 helix_lsp::transport [ERROR] typescript-language-server <- InternalError: Request initialize failed with message: Could not find a valid TypeScript installation. Please ensure that the "typescript" dependency is installed in the workspace or that a valid `tsserver.path` is specified. Exiting.

So, it seems `typescript-language-server` doesn't depend on `typescript`. Oh, well... let's install it as well:

```shell
npm install typescript -g
```

At this point things should start working properly for you and you'd getting the full power of
`typescript-language-lsp` in Helix.

I don't know about you, but for me it's really impressive you can setup an editor so
quickly, without touching the configuration at all. Perhaps most modern editors work this
way, but this has definitely not been my experience with my beloved Emacs or its arch nemesis (Neo)vim.

That's all I have for you today! I can heartily recommend playing a bit with Helix if you're
looking for a modern, fast and simple text editor that gets stuff done. Keep hacking!
