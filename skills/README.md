# Skills

Claude Code skills. Copy a folder into a project's `.claude/skills/` to enable it there, or
into `~/.claude/skills/` to have it everywhere.

| Skill | What it does |
| --- | --- |
| [caveman](caveman/) | Ultra-compressed replies. Cuts output tokens ~65% by dropping filler while keeping technical substance. Levels: lite, full, ultra, plus three 文言文 variants. |
| [grill-me](grill-me/) | Interviews you relentlessly about a plan or design, walking each branch of the decision tree until you reach shared understanding. |
| [ste-writing](ste-writing/) | Rewrites prose into ASD-STE100 Simplified Technical English. Strips "AI slop" from docs, READMEs, PR text, error messages, and system prompts. Two modes: strict (procedures/safety) and STE-flavored (general prose). |

## Install

One skill:

```bash
cp -r skills/ste-writing ~/.claude/skills/
```

All of them:

```bash
cp -r skills/*/ ~/.claude/skills/
```

Invoke with `/<name>`, or let Claude pick one up from its description.

## When to use which

- **caveman** — long sessions, token budget pressure, or you want answers without the padding.
- **grill-me** — before building. Stress-tests a plan while changing it is still cheap.
- **ste-writing** — after building. Cleans up what you wrote for other people to read.
