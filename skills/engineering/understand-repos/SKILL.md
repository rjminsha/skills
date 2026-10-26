---
name: understand-repos
description: Analyse a single git repository or a directory of cloned repos (GitHub Organisation) and produce four living reference files — DOMAIN-LANGUAGE.md, MAP.md, SUMMARY.md, and DETAILS.md — capturing domain vocabulary, structural waypoints, a status overview, and per-repo deep-dives. Uses parallel sub-agents per repo and validates output with a self-quiz loop (≥ 90% pass rate). Use when a user wants to understand, explore, document, or onboard onto a git repository or GitHub Organisation. Trigger phrases include "understand repo", "map a codebase", "repo overview", "what does this repo do", "document this codebase", "I'm new to this project", "explore this organisation", "analyse these repos".
---

# Understand Repos

## Quick start

1. If the user has already provided a local path, use it. Otherwise ask: **"Provide the local path to the repository, or the local path to a directory that contains multiple cloned repositories for an organisation."**
2. Follow the workflow starting at [Step 1 — Identify repositories](./REFERENCE-STEP1-IDENTIFY.md).
3. Produce `DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, and `SUMMARY.md` at the root of the provided path.

## Output files

| File | Purpose |
|---|---|
| `DETAILS.md` | Per-repository deep-dive, sorted alphabetically. Key directories, how the pieces fit together, and a verified FAQ per repo. Light entry for stale/archived repos. |
| `DOMAIN-LANGUAGE.md` | Glossary of key domain terms used in or across repositories. Shared language for user-stories and feature discussions. |
| `MAP.md` | Table of contents and structural index. Repository list plus waypoints (key paths per repo); enables progressive disclosure for agents. |
| `SUMMARY.md` | High-level summary: purpose, status, activity, language, frameworks, configuration, secrets, type, deployment, and license. Repo names link to their `DETAILS.md` section. |

## Workflow overview

| Step | Reference | What happens |
|---|---|---|
| 1 | [Identify repositories](./REFERENCE-STEP1-IDENTIFY.md) | Ask user for local path; determine output root; create `temp/`; ask once, then refresh each repo (fetch, switch to default branch, fast-forward pull) |
| 2 | [Per-repository sub-agents](./REFERENCE-STEP2-ANALYSE.md) | Analyse each repo in parallel (read-only; detects forks and status); write `temp/[repo]-analysis.md` and `temp/[repo]-domain.md` |
| 3 | [Coordinator review and merge](./REFERENCE-STEP3-REVIEW.md) | Merge temp files; write `DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, `SUMMARY.md` |
| 4 | [Self-quiz loop](./REFERENCE-STEP4-QUIZ.md) | Spawn answer-agents; verify ≥ 90% pass rate; write verified Q&A into `DETAILS.md` FAQ |
| 5 | [Clean-up](./REFERENCE-STEP5-CLEANUP.md) | Ask user whether to delete `temp/` files |

## Used skills

| Skill | If not available |
|---|---|
| `github-cli` | Skip GitHub API enrichment; rely solely on local git history and file inspection |
