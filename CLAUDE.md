# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of agent skills (slash commands) for Claude Code and other AI coding agents. Skills are Markdown-based instructions packaged for the [vercel-labs/skills CLI](https://github.com/vercel-labs/skills).

## Scripts

```bash
# Symlink all skills into ~/.claude/skills/ for local use
./scripts/link-skills.sh

# List all SKILL.md paths
./scripts/list-skills.sh
```

## Skill structure

Each skill is a directory under `skills/<bucket>/<skill-name>/`:

```
skill-name/
├── SKILL.md        # Required — frontmatter + instructions
├── REFERENCE.md    # Optional — overflow docs when SKILL.md > ~100 lines
├── EXAMPLES.md     # Optional
└── scripts/        # Optional — deterministic helper scripts
```

`SKILL.md` frontmatter:
```yaml
---
name: skill-name
description: What it does. Use when [specific triggers].
---
```

The `description` is the only thing an agent reads when deciding which skill to load — make it specific and include "Use when..." triggers. Max 1024 chars.

## Buckets

| Bucket | Purpose | Must appear in README + plugin.json? |
|---|---|---|
| `engineering/` | Daily code work | Yes |
| `productivity/` | Non-code workflow tools | Yes |
| `deprecated/` | No longer used | No |

## Invariants to maintain

When adding or removing a **promoted** skill (engineering or productivity bucket), you must update **both**:
1. Top-level `README.md` — list the skill name linked to its `SKILL.md`
2. `.claude-plugin/plugin.json` — add/remove the skill directory path

Skills in `deprecated/` must not appear in either.

## Authoring guidelines

- Keep `SKILL.md` under 100 lines; split overflow into `REFERENCE.md`
- Split into separate files when content has distinct domains or advanced features are rarely needed
- Add scripts only for deterministic, repeatable operations — they save tokens and improve reliability over generated code
- No time-sensitive information in skill files
