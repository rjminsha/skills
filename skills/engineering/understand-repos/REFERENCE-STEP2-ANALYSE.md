# Step 2 — Per-Repository Sub-Agents & Completeness Loop

Read `temp/inventory.md` created in Step 1 to retrieve the full list of repositories ($N$ total).

For each repository in `temp/inventory.md`, spawn a **sub-agent** with the instructions below. Sub-agents run in parallel where possible.

**Before spawning**, confirm sub-agents can read the repository path and write to `temp/`. If the path is outside the agent's workspace and sub-agents cannot access it, do the analysis directly in the coordinator (one repo at a time) instead of spawning. Never fabricate analysis for a repo that could not be read.

## Sub-agent instructions

The sub-agent receives the repository path, output root, whether this is a re-run, and the refresh outcome from Step 1 (default branch and `updated` / `already current` / `fetched only` / `skipped` / `failed`). **Sub-agents are read-only with respect to the repository — never fetch, pull, switch branches, or edit files in it.**

Analyse the default branch. If the repo is on the default branch, read it as normal. If it is not (refresh was skipped or fetch-only), use the remote ref instead: `git log origin/<default>` for history, and `git show origin/<default>:<path>` for file contents where it matters. Note in the analysis when the working tree is not the default branch or is behind, so readers know the basis of the findings.

1. **Check for existing top-level docs** — if this is a re-run or incremental update, `DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, and `SUMMARY.md` may already exist at the output root. If they do, read them before writing temp files so your output stays consistent with what's already there and doesn't introduce contradictions or duplicates. Skip this check if the files don't exist yet.

2. **Analyse the repository**. Inspect:

   | Signal | What to look at |
   |---|---|
   | Repository URL | `git remote get-url origin`; if `gh` is available, `gh repo view --json url` for the canonical web URL. If the remote is SSH (`git@host:org/repo.git` or `ssh://git@host/org/repo`), convert it to the `https://host/org/repo` form — the output files are read by people who need a clickable link. |
   | Fork / upstream | Is this repo a fork, and of what? If `gh` is available, `gh repo view --json isFork,parent` gives the upstream owner/name (works for GitHub Enterprise when authenticated). Otherwise check `git remote -v` for an `upstream` remote, and the README for "forked from" wording. Record the upstream's canonical web URL. If you cannot determine either way, record `unknown` — do not assume "not a fork". |
   | Purpose | README.md, description fields, AGENTS.md, CLAUDE.md |
   | Languages | file extensions; tool config files (`.nvmrc`, `go.mod`, `pyproject.toml`, `Cargo.toml`, `pom.xml`, etc.) |
   | Frameworks | dependency manifests (`package.json`, `requirements.txt`, `go.mod`, `Gemfile`, etc.) |
   | Configuration | env files (`.env.example`), config dirs (`config/`), Helm values, `*.yaml`/`*.json` config |
   | Secrets | env var names in config; references to vault/KMS/secret managers |
   | Type | classify as one of: `code`, `documentation`, `scripts/utilities`, `mixed` |
   | Deployment | look for `Dockerfile`, `helm/`, `k8s/`, `.github/workflows/`, Tekton pipelines, `deploy/` |
   | License | `LICENSE` file or `license` field in manifests |
   | Activity | `git log --oneline -20`; last commit date; PR open/close rate; issue open/close rate (if `gh` is available) |
   | Status | classify using the criteria table below |

3. **Classify status** using the following criteria. Apply the decision logic top-to-bottom; use the first match.

   | Status | Criteria |
   |---|---|
   | `archived` | Explicitly marked archived (GitHub archived flag, README says archived/deprecated/unmaintained), or zero activity for 12+ months with no issue or PR flow |
   | `new` | No commits at all (git reports no HEAD), or only an initial commit containing nothing but a bare scaffold (e.g. template README, LICENSE, `.gitignore`). A single commit that contains real content (scripts, docs, code) is **not** `new` — classify it by the other rows. Record Activity as "No commits" or "Initial commit only". |
   | `stale` | No commits in 90+ days; open issues or PRs exist but nothing is being resolved |
   | `experimental` | Direct commits to main (no PR discipline); no stable release or tags; README or repo name signals WIP/prototype (`-poc`, `-spike`, `-wip`) |
   | `very-active` | Commits to main most days; PRs being opened **and** closed regularly; issues being opened **and** closed |
   | `active` | At least weekly commits; PRs used for merging to main; some issue flow; ongoing development |
   | `stable` | Infrequent commits; merges via PRs (not direct commits to main); issues and PRs rare but do get resolved |
   | `stable` *(default)* | Insufficient signal to classify otherwise |

   **Key distinctions:**
   - `new` vs `experimental` — new is an empty repo or bare scaffold with no real content; experimental has real content (even in one commit) and signals WIP/prototype.
   - `experimental` vs `stable` — experimental pushes directly to main; stable gates changes through PRs.
   - `stale` vs `archived` — stale is neglected; archived is intentionally retired.
   - `stale` vs `stable` — stable repos still have issues/PRs resolved; stale ones do not.

4. **Extract domain terms** — the vocabulary someone needs to follow this organisation's READMEs, specs and user stories. Look in the README first, then doc headings and code comments.

   **Include** a term if a newcomer to *this organisation or platform* would need it explained, even if it is an IBM-wide or vendor term:
   - platform, service and product names (e.g. Auditree, Global Catalog, Partner Center)
   - internal project, team and environment names, and the names of specific accounts or instances (e.g. an account called "Provider Petra" — the name, never its ID)
   - acronyms and resource kinds the docs assume (e.g. S2S auth, secret group, ROKS)
   - naming conventions for the org's resources

   **Exclude** a term if it is any of:
   - a file or directory name (those belong in `MAP.md` / `DETAILS.md`)
   - universal software-engineering vocabulary or developer tooling: CI/CD, PR gates, scanners and linters, Helm chart, milestone, decision record, meeting notes
   - an ordinary-word phrase for an activity (batch analysis, compliance check, evidence collection)
   - about this skill or its output (coordinator, sub-agent, refresh, waypoint, the four generated files)
   - a value rather than a name: IDs, URLs, counts

   **Style:** at most 8 terms per repo; one plain sentence of 20 words or fewer each, no lists or examples. Test: *"Would a smart newcomer to this platform be unable to guess it?"* If not, leave it out.

5. **Write temp files** to `temp/[repository-name]-analysis.md` and `temp/[repository-name]-domain.md` using the templates below. Writing these structured files (rather than returning results inline) is what lets the coordinator agent merge multiple repos reliably in Step 3.

## `temp/[repo]-analysis.md` template

```markdown
# [repo-name] Analysis

## Repository URL
https://github.com/org/repo-name

## Fork
Fork: yes | no | unknown
Upstream: https://github.com/owner/upstream-repo   <!-- only if Fork is yes -->

## Purpose
One or two sentences.

## Status
very-active | active | stable | new | experimental | stale | archived

## Activity
Last commit: YYYY-MM-DD. [Commit count last 30 days if available.]
Branch analysed: <default-branch> — refresh: updated | already current | fetched only | skipped: <reason> | failed: <reason>

## Type
code | documentation | scripts/utilities | mixed

## Languages
- Language (primary/secondary)

## Frameworks & Libraries
- framework@version — purpose

## Configuration
- Config file / env var pattern → purpose

## Secrets
- ENV_VAR_NAME — what it holds (names and locations only; never actual values, tokens, or account credentials)

## Deployment
- Method: container | helm chart | not deployed | CI/CD only
- Notes: [Dockerfile path, chart path, registry, etc.]

## License
SPDX identifier or "Unknown"

## Key directories / entry points
- `path/` — what it contains

## Details
<!-- Omit this section for stale and archived repositories. -->

< 500 words. Describe what the repository does in enough depth for a new team member to orient themselves. Cover:

- **What it does** — expand on Purpose with concrete specifics.
- **Key directories** — for each significant directory, one sentence on what it contains and why it matters. Call out the most important individual files within it (e.g. entry points, primary config, main module).
- **How the pieces fit together** — a short narrative (2–4 sentences) on how the major components relate: e.g. which module calls which, how data flows, what the CLI/API surface is.
```

## `temp/[repo]-domain.md` template

```markdown
# [repo-name] Domain Terms

| Term | Definition |
|---|---|
| TermName | What it means in this codebase. |
```

## Completeness verification loop

After sub-agents finish, the coordinator **must verify 100% repository coverage**:

1. **Audit temp files**: Read `temp/inventory.md`. For every repository listed, check that **both** `temp/[repo]-analysis.md` and `temp/[repo]-domain.md` exist and contain non-empty content.
2. **Detect missing analysis**:
   - Compare the list of completed analysis files against the inventory ($N$ total).
   - If any repositories are missing, partially written, or failed:
     > "Analysis missing for K of N repositories: [missing-repo-1, missing-repo-2, ...]. Running analysis for missing repositories..."
     - Run/spawn analysis specifically for those missing repositories.
3. **Loop until complete**:
   - Repeat the audit and analysis until `completed_count == total_inventory_count`.
   - Never proceed to Step 3 until **all** $N$ repositories in `temp/inventory.md` have their analysis files in `temp/`.

Proceed to [Step 3 — Coordinator review and merge](./REFERENCE-STEP3-REVIEW.md) once all repositories are verified complete.
