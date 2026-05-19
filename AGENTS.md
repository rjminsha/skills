# rjminsha Skills

A collection of agent skills (slash commands and behaviours) for Claude Code.

## Scripts

```bash
# Link all skills into ~/.claude/skills/ for local use
./scripts/link-skills.sh

# List all SKILL.md paths in the repo
./scripts/list-skills.sh
```

## Skill Buckets

Skills live under `skills/` in bucket folders:

| Bucket | Purpose | Promoted (README + plugin.json)? |
|---|---|---|
| `engineering/` | Daily code work | Yes |
| `productivity/` | Non-code workflow tools | Yes |
| `deprecated/` | No longer used | No |

**Promoted** means the skill must appear in both the top-level `README.md` and `.claude-plugin/plugin.json`. Skills in non-promoted buckets must not appear in either.

## Skill Structure

Each skill is a directory containing at minimum a `SKILL.md`:

```
skill-name/
├── SKILL.md           # Required — frontmatter + instructions
├── REFERENCE.md       # Optional — detailed docs when SKILL.md would exceed ~100 lines
├── EXAMPLES.md        # Optional
└── scripts/           # Optional — deterministic helper scripts
```

`SKILL.md` frontmatter:
```yaml
---
name: skill-name
description: One-paragraph description (max 1024 chars). First sentence: what it does. Second: "Use when [specific triggers]."
---
```

The `description` field is what the agent reads to decide which skill to load — make it specific.

## README Linking Rules

- **Top-level `README.md`**: every promoted skill must be listed with its name linked to its `SKILL.md`
- **Each bucket `README.md`**: lists every skill in that bucket with a one-line description and the skill name linked to its `SKILL.md`

## Plugin Registration

`.claude-plugin/plugin.json` lists the paths of all promoted skills. When adding or removing a promoted skill, update this file.
