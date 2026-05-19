# rjminsha Skills

My agent skills for real engineering work — not tied to any single AI agent.

## Supported agents

Skills work across multiple coding agents, including:

- [IBM Bob](https://bob.ibm.com/)
- [Pi](https://pi.dev/)
- Cursor, Windsurf, and others supported by the [skills CLI](https://github.com/vercel-labs/skills)

## Quickstart

Install the [skills CLI](https://github.com/vercel-labs/skills), then run:

```bash
npx skills@latest add rjminsha/skills
```

Pick the skills you want and which coding agents to install them on. Make sure you select `/setup-rjminsha-skills` before using the engineering workflow skills.

## Credits

- Skills structure and engineering patterns based on [mattpocock/skills](https://github.com/mattpocock/skills/tree/main)
- Installed via the [vercel-labs/skills](https://github.com/vercel-labs/skills) CLI

## Engineering

Skills for daily code work.

- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)** — Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates `CONTEXT.md` and ADRs inline.
- **[setup-rjminsha-skills](./skills/engineering/setup-rjminsha-skills/SKILL.md)** — Scaffold per-repo config (issue tracker, triage labels, domain doc layout). Run once per repo before using `tdd` or other engineering skills.
- **[tdd](./skills/engineering/tdd/SKILL.md)** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.

## Productivity

General workflow tools, not code-specific.

- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.
- **[handoff](./skills/productivity/handoff/SKILL.md)** — Compact the current conversation into a handoff document so another agent can continue the work.
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.md)** — Create new skills with proper structure, progressive disclosure, and bundled resources.
