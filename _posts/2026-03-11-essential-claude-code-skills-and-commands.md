---
title: Essential Claude Code Skills and Commands
date: 2026-03-11 10:30 +0200
tags:
- AI
- Claude Code
---

I'll admit it: when I first started using [Claude
Code](https://docs.anthropic.com/en/docs/claude-code), I mostly ignored the
built-in skills. Everyone online was saying "go make your own skills," so
that's what I did. I wrote custom skills for all sorts of things and
I got plenty of things done with them.

It wasn't until I stumbled into `/review` and `/simplify` that I realized
I'd been overlooking some genuinely useful built-in functionality. And
recently, with additions like `/loop` and `/batch`, the built-in skill set
has gotten even more interesting.

This post covers what's available out of the box, how to use it effectively,
and the difference between skills and slash commands (which confused me
initially too).

<!--more-->

## Skills vs. Slash Commands: What's the Difference?

This distinction tripped me up early on, and I don't think I'm alone. Here's
the deal:

**Slash commands** are built-in fixed-logic operations. Things like `/clear`,
`/compact`, `/help`, `/model`, `/cost` -- these are hardcoded into the Claude
Code CLI. They do one specific thing, they don't involve AI reasoning, and you
can't customize them. Think of them as CLI commands that happen to start with
`/`.

**Skills** are prompt-based capabilities. When you invoke a skill, it loads a
set of instructions (a markdown file) into Claude's context, and Claude
executes them. Skills can spawn subagents, accept arguments, use specific
tools, and generally orchestrate complex multi-step workflows. The built-in
skills (`/simplify`, `/batch`, `/loop`, `/debug`, `/claude-api`) are shipped
with Claude Code, but the system is designed for you to create your own as
well.[^1]

The confusion comes from the fact that both are invoked with `/`, and
historically they were separate systems. Claude Code used to have "commands"
(`.claude/commands/*.md`) and "skills" (`.claude/skills/*/SKILL.md`) as
distinct concepts. These have since been **merged** -- files in either location
create the same `/slash-command` interface. The skills system is the
recommended approach going forward because it supports features that plain
commands don't:

- Supporting files (templates, examples, scripts alongside the skill)
- Frontmatter control (`disable-model-invocation`, `user-invocable`,
  `allowed-tools`, `context`, `agent`)
- Dynamic context injection via shell command output
- Subagent execution with `context: fork`

Your existing `.claude/commands/` files still work, but new ones should go
in `.claude/skills/`.

> You can list all available skills (both built-in and custom) with `/skills`.
{: .prompt-tip }

## The Built-in Skills

### /simplify -- Code Quality Review

This is the skill I use most often. After making changes to a codebase,
`/simplify` reviews your recently changed files for code reuse opportunities,
quality issues, and efficiency improvements -- and then fixes them
automatically.

What makes it effective is that it spawns **three parallel review agents**,
each looking at the changes from a different angle. You get a mini code review
without leaving your terminal.

```
/simplify
```

You can optionally pass a focus area:

```
/simplify memory efficiency
/simplify error handling
/simplify reduce duplication
```

This narrows the review to a specific concern, which is useful when you know
where the rough edges are but want AI help with the details.

I find `/simplify` most valuable right after a larger refactoring or after
accepting a batch of AI-generated code. It catches things like unused
imports, redundant variables, opportunities to extract shared logic, and
overly complex conditionals. Think of it as a second pass that keeps your
code from accumulating cruft.

### /batch -- Large-Scale Parallel Changes

`/batch` is the heavy hitter. It orchestrates large-scale changes across a
codebase by decomposing the work into 5-30 independent units and spawning
isolated worktree agents to handle them in parallel.

```
/batch migrate all test files from Jest to Vitest
/batch add TypeScript types to all files in src/utils/
/batch update all API endpoints to use the new auth middleware
```

Each agent works in its own git worktree, implements its unit of work, runs
tests, and can even open a pull request. This means you can kick off a
migration and review the results incrementally rather than waiting for one
monolithic change.

The key insight with `/batch` is that the instruction needs to describe a
**pattern** -- something that applies uniformly across multiple files or
components. It's not for "rewrite the authentication system" (that's a single
complex task), it's for "apply this specific change to every place that matches
this pattern."

### /loop -- Scheduled Recurring Prompts

`/loop` runs a prompt repeatedly on a schedule while your session stays open.
It parses a time interval and sets up a recurring task.

```
/loop 5m check if the dev server has any new errors in the log
/loop 10m run the test suite and report any failures
/loop 1h check deployment status and summarize metrics
```

The interval is optional and defaults to a reasonable period. This is useful
for monitoring tasks -- watching a long-running build, keeping an eye on a
staging deployment, or periodically checking for new issues in a log file.

I haven't used `/loop` as extensively as the other skills, but I can see it
being handy for longer coding sessions where you want background monitoring
without switching to another terminal.

### /debug -- Session Troubleshooting

When something goes wrong in your Claude Code session -- a tool call fails
silently, Claude seems confused about context, or behavior is just
inexplicably odd -- `/debug` reads the session debug log and helps you
figure out what happened.

```
/debug
/debug why did the last edit fail
/debug tool calls are timing out
```

This is a niche skill, but invaluable when you need it. It's essentially
"Claude, diagnose yourself."

### /claude-api -- API Reference Loader

If you're building applications with the Claude API or Anthropic SDK,
`/claude-api` loads the relevant API reference material for your project's
language (Python, TypeScript, Java, Go, Ruby, C#, PHP, or cURL) plus the
Agent SDK reference.

```
/claude-api
```

This skill also activates automatically when Claude detects code that imports
`anthropic`, `@anthropic-ai/sdk`, or `claude_agent_sdk`, so you may never
need to invoke it manually.

## Useful Slash Commands

While not "skills" in the technical sense, several built-in slash commands
are worth knowing about:

### /compact -- Context Management

When your conversation gets long and Claude starts losing track of earlier
context, `/compact` compresses the conversation history. You can optionally
give it focus instructions:

```
/compact focus on the database migration work
```

This is essential for long sessions. I use it proactively before starting a
new phase of work within the same session.[^2]

### /diff -- Review Changes

`/diff` opens an interactive diff viewer showing all changes Claude has made.
This is much better than mentally tracking what changed across multiple files.

A few practical tips:

- **Use it as a checkpoint.** After Claude makes a series of edits, run `/diff`
  before moving on. It's your chance to catch mistakes before they compound.
  Much easier to ask Claude to fix something now than three steps later.
- **Use it before committing.** I always run `/diff` right before asking Claude
  to commit. It's the equivalent of `git diff --staged` but more convenient --
  you see exactly what Claude changed, not what you manually staged.
- **Combine with /rewind.** If `/diff` reveals something you don't like, you
  can `/rewind` to undo the changes and try a different approach. The two
  commands pair naturally: review, then decide whether to keep or discard.

### /btw -- Side Questions

`/btw` lets you ask a side question without affecting the main conversation
context:

```
/btw what's the syntax for a Rust match guard again?
```

The answer comes back without polluting your working context -- handy when you
need a quick lookup mid-task.

### /rewind -- Undo Changes

`/rewind` is your safety net. It reverts both the conversation and any file
changes back to a previous point, effectively letting you say "let's pretend
that never happened." Claude creates implicit checkpoints as you work, so you
can step back through them.

This is especially useful when Claude goes down the wrong path -- maybe it
misunderstood your intent, or the approach it chose isn't working out. Instead
of manually reverting files and trying to explain what went wrong, just
`/rewind` and start that part of the conversation fresh with a clearer prompt.

### /usage, /cost, and /stats -- Keeping Track of Consumption

There are three commands for tracking usage, and which ones you'll see depends
on how you access Claude Code:

- **`/usage`** shows your **plan-level** limits and current rate limit status.
  Use it to check how much of your daily/monthly quota you've consumed and
  whether you're approaching a rate limit. Available to everyone.
- **`/cost`** shows token usage and estimated dollar cost for the **current
  session**. This is only relevant (and visible) if you're using Claude Code
  via the **API** -- subscription users (Pro/Max) won't see it since tokens
  are included in the plan.
- **`/stats`** visualizes daily usage patterns, session history, and model
  preferences. This is the subscription-friendly alternative to `/cost` --
  it shows you *how* you're using Claude Code over time rather than what
  it's costing per session.

In short: `/usage` is "how much runway do I have left?", `/cost` is "how much
did this session cost in dollars?" (API only), and `/stats` is "what do my
usage patterns look like?"

### /context -- Context Visualization

`/context` displays a colored grid showing how your context window is being
used. It helps you understand when you're approaching limits and what's
taking up space.

### /plan -- Plan Mode

`/plan` enters plan mode, where Claude designs an implementation strategy
without making any changes. This is useful when you want to think through an
approach before committing to it. You can also toggle plan mode with
`Shift+Tab` at any point during your conversation -- no need to type the
command.

Here's an example of how I'd use it:

```
/plan refactor the authentication module to support OAuth2 in addition to
the existing API key auth
```

Claude will analyze the codebase, identify the affected files, and propose a
step-by-step implementation plan -- without touching any code. You can discuss
the plan, ask questions, suggest alternatives, and iterate until you're happy.
Then exit plan mode (with `Shift+Tab` or `/plan` again) and tell Claude to
execute.

It's worth noting that Claude will sometimes enter plan mode on its own when
you give it a sufficiently complex task. If it determines that planning is
warranted before diving in, it'll switch to plan mode, present its approach,
and wait for your approval. This is generally a good thing -- it means Claude
is thinking before acting rather than charging ahead with a potentially
wrong approach.

## Skill Arguments and Parameters

All skills support arguments via the `$ARGUMENTS` placeholder (and indexed
variants like `$0`, `$1` for specific arguments). For the built-in skills:

| Skill | Arguments | Example |
|-------|-----------|---------|
| `/simplify` | Optional focus area | `/simplify error handling` |
| `/batch` | Required: change description | `/batch add logging to all API handlers` |
| `/loop` | Optional interval + required prompt | `/loop 5m check build status` |
| `/debug` | Optional issue description | `/debug why did the edit fail` |
| `/claude-api` | None | `/claude-api` |

When creating your own skills, the argument system is quite flexible. In
your `SKILL.md`:

```markdown
---
name: fix-issue
description: Fix a GitHub issue
argument-hint: <issue-number>
---

Fix GitHub issue $ARGUMENTS following our project's standards.
```

Invoked as `/fix-issue 123`, the `$ARGUMENTS` placeholder becomes `123`.

## Creating Your Own Skills

The built-in skills are great, but the real power is in creating your own.
Skills live in one of three locations:

| Scope | Path | Use case |
|-------|------|----------|
| Personal | `~/.claude/skills/<name>/SKILL.md` | Your workflows, all projects |
| Project | `.claude/skills/<name>/SKILL.md` | Team conventions, commit to git |
| Plugin | `<plugin>/skills/<name>/SKILL.md` | Shared via plugin system |

A skill is just a `SKILL.md` file with optional frontmatter:

```markdown
---
name: deploy
description: Deploy the current branch to staging
disable-model-invocation: true
allowed-tools: Bash
---

Deploy the current branch to staging:

1. Run the test suite first: `npm test`
2. Build the production bundle: `npm run build`
3. Deploy using: `./scripts/deploy.sh staging`
4. Verify the deployment by checking the health endpoint
```

Key frontmatter options:

- **`disable-model-invocation: true`** -- Only you can invoke this skill
  (Claude won't trigger it automatically). Use this for anything with side
  effects.
- **`user-invocable: false`** -- Only Claude can invoke it (background
  knowledge). Use this for conventions and guidelines you want Claude to follow
  automatically.
- **`allowed-tools`** -- Restrict which tools Claude can use. Useful for
  read-only research skills.
- **`context: fork`** -- Run in an isolated subagent. Good for research
  tasks that shouldn't pollute your main conversation.
- **`agent`** -- Which subagent type to use (`Explore`, `Plan`,
  `general-purpose`).

Skills can also include supporting files alongside `SKILL.md` -- templates,
reference docs, example code -- and inject dynamic context via shell commands
using the `` !`command` `` syntax:

```markdown
---
name: pr-review
description: Review the current PR
---

Here's the PR diff:
!`gh pr diff`

Changed files:
!`gh pr diff --name-only`

Review these changes for correctness, style, and potential issues.
```

## Workflow Tips

**Chain /simplify after AI-generated changes.** Whenever Claude writes a
significant chunk of code, run `/simplify` as a follow-up. AI-generated code
often has subtle redundancies or missed optimization opportunities that a
second pass catches.

**Use /batch for migrations, not redesigns.** `/batch` shines when the change
is repetitive and parallelizable. "Add error handling to all 47 API
endpoints" is perfect. "Redesign the API layer" is not -- that needs a
coherent plan, not parallel execution.

**Use /compact proactively.** Don't wait until Claude starts losing context.
When you finish one logical chunk of work and are about to start another,
compact first. Your future self will thank you.

**Start with /plan for complex tasks.** Before diving into implementation,
use `/plan` to get Claude to think through the approach. You can discuss and
refine the plan before a single line of code is written.

**Use /btw liberally.** Side questions are free (context-wise). Don't
pollute your main working context with "wait, how does X work again?"
tangents.

## Epilogue

Claude Code is a fast-moving target these days -- new built-in skills and
commands get added regularly, and the skill system itself keeps evolving. I'll
make an effort to update this article when something cool catches my eye, so
check back from time to time.

That's all I have for you today. Keep hacking!

[^1]: You can also install third-party skills via the plugin system. Run `/plugin` to manage plugins.
[^2]: If you don't use `/compact` yourself eventually Claude will do auto-compaction anyways.
