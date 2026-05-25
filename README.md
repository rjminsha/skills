# rjminsha Skills

A personal set of agent skills that capture how I work — from the first vague requirement through to production. The objective is a simple, opinionated workflow encoded as skills any coding agent can follow.  My workflow is based upon "traditional" software engineering processes that incorporate chat harnesses and modern models.  The agent is simply part of the team, and my parnter in peer programing and design sessions; they are also a pretty compitent developer but need with goals and overall context of the project.  

Or perhaps it is bringing Enterprise complexity just to make sure no one has any fun or builds anything quickly.

## The workflow

```
┌─────────────────────────────────────────────────────────────────────────┐
│  /user-story                                         [fresh context]   │
│  in:  git issue or feature description                                  │
│  out: docs/user-stories/*.md                                            │
└──────────────────────┬──────────────────────────────────────────────────┘
                       │ user-story.md
                       ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  /grill-me-with-docs                                 [fresh context]   │
│  in:  codebase · LANGUAGE.md · MAP.md · decisions                      │
│  out: LANGUAGE.md · MAP.md · docs/decisions/                           │
└──────────────────────┬──────────────────────────────────────────────────┘
                       │ LANGUAGE.md · MAP.md · decisions/
                       ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  /requirements-interlock                             [fresh context]   │
│  in:  user-story · LANGUAGE.md · MAP.md · decisions                    │
│  out: docs/user-stories/*-TRI.md                                        │
└──────────────────────┬──────────────────────────────────────────────────┘
                       │ TRI.md
                       ▼
  ┄┄┄┄┄┄┄┄┄┄┄┄┄ human: read RULES.md before coding ┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
                       │ RULES.md
                       ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  /tdd                                                [fresh context]   │
│  in:  user-story · TRI.md · LANGUAGE.md · MAP.md · RULES.md            │
│                                                                         │
│  RED → GREEN → Refactor → (repeat per behaviour)                        │
│                                                                         │
│  out: code + tests                                                      │
└──────────────────────┬──────────────────────────────────────────────────┘
                       │ code + tests
                       ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  /review                                             [fresh context]   │
│  in:  code · user-story · TRI.md · RULES.md                            │
│  out: review report                                                     │
└─────────────────────────────────────────────────────────────────────────┘
                       │
                       ▼
  ─── deployment thinking (no skills yet) ─────────────────────────────────
  NFR → resilience → "Security by Design" → supply chain → SBOM
  CI/CD → staging → chaos testing → performance → red teaming
  security & architecture review → risk scoring → production
```

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

## Documentation workflow

The `grill-me-with-docs` skill builds up three living documents alongside your code. Each serves a distinct purpose for humans and agents working with limited context:

```
/
├── LANGUAGE.md          ← canonical domain terms; what things mean
├── MAP.md               ← sparse system map; where things live and how they connect
├── docs/
│   └── decisions/
│       └── 0001-*.md   ← hard-to-reverse, surprising, or trade-off decisions
└── src/
```

**`LANGUAGE.md`** is a glossary of domain terms — the Ubiquitous Language shared between developers and domain experts. It contains no implementation details.

**`MAP.md`** is a sparse reference to the whole system: seam-level components, user interfaces, and related systems in other repositories. Its primary purpose is to give any agent or developer with limited context a map of what already exists — preventing duplication and clarifying boundaries across a system that may be too large to fit in a single context window.

**`docs/decisions/`** records architectural decisions that are hard to reverse, surprising without context, or the result of a genuine trade-off.

## Engineering

Skills for daily code work.

- **[grill-me-with-docs](./skills/engineering/grill-me-with-docs/SKILL.md)** — Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates `LANGUAGE.md`, `MAP.md`, and decisions inline.
- **[requirements-interlock](./skills/engineering/requirements-interlock/SKILL.md)** — Takes user stories, `LANGUAGE.md`, `MAP.md`, and decisions as inputs; grills the design through a resilience lens; outputs a `TRI.md` (Technical Requirements and Implementation document).
- **[setup-rjminsha-skills](./skills/engineering/setup-rjminsha-skills/SKILL.md)** — Scaffold per-repo config (issue tracker, triage labels, domain doc layout). Run once per repo before using `tdd` or other engineering skills.
- **[review](./skills/engineering/review/SKILL.md)** — Post-TDD review: assess code against rules, verify requirements are met, check for MAP/LANGUAGE/decisions drift, and ask "could we do better?" against quality goals.
- **[tdd](./skills/engineering/tdd/SKILL.md)** — Test-driven development with a red-green-refactor loop. Builds features or fixes bugs one vertical slice at a time.
- **[user-story](./skills/engineering/user-story/SKILL.md)** — Create a user story and BDD scenarios from a git issue, feature description, or existing code. Outputs to `docs/user-stories/`.

## Productivity

General workflow tools, not code-specific.

- **[grill-me](./skills/productivity/grill-me/SKILL.md)** — Get relentlessly interviewed about a plan or design until every branch of the decision tree is resolved.
- **[handoff](./skills/productivity/handoff/SKILL.md)** — Compact the current conversation into a handoff document so another agent can continue the work.
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.md)** — Create new skills with proper structure, progressive disclosure, and bundled resources.

## Skill outputs

| Skill | Output | Target length |
|---|---|---|
| `grill-me` | None — conversation only | — |
| `handoff` | One handoff document | ~1-2 pages |
| `user-story` | `docs/user-stories/0001-*.md` | ~1 page — one story + 3-5 BDD scenarios |
| `grill-me-with-docs` | `LANGUAGE.md` + `MAP.md` + `docs/decisions/` | LANGUAGE.md: grows over time; MAP.md: sparse, 1-2 pages; each decision: 1 paragraph |
| `requirements-interlock` | `0001-*-TRI.md` + optional new decisions | TRI: concise, ~2-4 pages of new content only |
| `review` | Review report (conversation) | — |
| `tdd` | Code + tests | No doc output |
| `setup-rjminsha-skills` | Repo config scaffold | Minimal config files |
| `write-a-skill` | New skill directory + files | Mirrors the skill structure |

## Credits

- Skill structure and CLI by [vercel-labs/skills](https://github.com/vercel-labs/skills)
- Engineering patterns and sharing model from [mattpocock/skills](https://github.com/mattpocock/skills/tree/main)
- *"There are many agent harnesses, but this one is yours"* — Mario Zechner, [Pi](https://pi.dev/) / [pi.dev](https://pi.dev)
- Harold Hannon and his basement of agents
- The `LANGUAGE.md` practice is rooted in Eric Evans' *Domain-Driven Design* — specifically the concept of a [Ubiquitous Language](https://martinfowler.com/bliki/UbiquitousLanguage.html): a common, rigorous language shared between developers and domain experts that is used consistently in code, conversation, and documentation
- The BDD practice in `user-story` is inspired by content created by Dave Farley — [Modern Software Engineering](https://www.youtube.com/@ModernSoftwareEngineeringYT)
