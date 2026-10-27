# Step 1 — Identify Repositories

Ask the user:

> "Provide the local path to the repository, or the local path to a directory containing multiple cloned repositories for an organisation."

- **Single repo**: path points directly to a git repo (contains a `.git/` directory).
- **Organisation**: path is a parent directory containing multiple cloned repos as sub-directories.

Determine the root output directory:
- Single repo → output files at the root of that repo.
- Organisation → output files at the parent directory provided by the user.

**Re-run detection**: if any of `DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, or `SUMMARY.md` already exist at the output root, this is a re-run or incremental update. Read those files before creating any new temp files so the updated output stays consistent and doesn't introduce contradictions or duplicate terms. Tell sub-agents in Step 2 that this is a re-run.

Create a `temp/` directory inside the output root if it does not already exist.

## Check for un-cloned organisation repositories

When analysing an organisation directory (containing multiple repositories):

1. **Identify the GitHub organisation**:
   - Inspect the remote origin URL of existing local repositories (`git remote get-url origin`) to determine the GitHub organisation name/owner (e.g. `github.com/<org>/<repo>` or `git@github.com:<org>/<repo>.git`).
   - If it cannot be determined automatically or no repositories are cloned yet, ask the user for the GitHub organisation name.

2. **Check for remote repositories**:
   - If `gh` CLI (or GitHub API) is available, list all repositories belonging to the GitHub organisation:
     `gh repo list <org> --limit 1000 --json name,sshUrl,url`
   - Compare the remote repository list against the sub-directories already present locally (directories containing `.git/`).

3. **Prompt and clone**:
   - If there are repositories in the GitHub organisation not cloned locally, list them and ask the user:
     > "Found N repositories in the `<org>` GitHub organisation that are not cloned locally: [list of repos]. Would you like to clone them? Options: **(1)** clone all, **(2)** select specific repos to clone, **(3)** skip — analyse only currently cloned repos."
   - If the user chooses to clone (all or selected):
     - Clone each chosen repository into the organisation directory using the git CLI: `git clone <repo-url>` (using the preferred protocol/URL matching existing repos or user preference).
   - If `gh` CLI is not available or the user skips cloning, proceed with only the currently cloned local repositories.

## Build repository inventory (`temp/inventory.md`)

To guarantee that **no repository is missed or skipped**, the coordinator must construct an exhaustive inventory file:

1. **Enumerate all repositories**:
   - **Single repo**: exactly one entry (the repo itself).
   - **Organisation**: perform a full enumeration of all sub-directories under the organisation path that contain a `.git/` directory. Do not use pagination-limited listings or assume a partial scan is complete.
2. **Write `temp/inventory.md`**:
   Write the complete list of discovered repositories with their paths and total count:

   ```markdown
   # Repository Inventory

   Total: N

   | Repository | Path |
   |---|---|
   | repo-1 | ./repo-1 |
   | repo-2 | ./repo-2 |
   ```

3. **Log discovery**: Announce the inventory to the user:
   > "Discovered N repositories for analysis: [repo-1, repo-2, ..., repo-N]."

## Refresh repositories

Local clones are often behind or sitting on a feature branch, which skews the activity, status, and content analysis. Bring each repo up to date with its default branch before analysis. This changes the user's working copies, so **ask once for the whole run**:

> "Before analysing, I'd like to update each repository: `git fetch`, then switch to its default branch and fast-forward pull. Repositories with uncommitted changes to tracked files are left untouched. Options: **(1)** update and pull, **(2)** fetch only (no checkout changes), **(3)** skip — analyse as-is."

Do the refresh in the coordinator for **every repository listed in `temp/inventory.md`**, once per repo, **before spawning sub-agents** (sub-agents must not modify repos). For each repo:

1. **Find the default branch**: `git symbolic-ref --short refs/remotes/origin/HEAD` (gives `origin/main`). If unset, try `git remote set-head origin --auto`; if that fails, use `main`, then `master`.
2. **Fetch**: `git fetch origin --prune`. If there is no remote, or fetch fails (offline, auth), record the failure and continue with local state — do not abort the run.
3. **Update** (option 1 only):
   - If `git status --porcelain --untracked-files=no` is non-empty → **skip**, record "skipped: uncommitted changes".
   - If the current branch (or detached HEAD) is not the default branch → `git switch <default>`. Record the original branch.
   - `git pull --ff-only`. If it cannot fast-forward (diverged), record "pull failed: diverged" and continue.
4. **Record the outcome** per repo: default branch, one of `updated`, `already current`, `fetched only`, `skipped: <reason>`, `failed: <reason>`, and the original branch if it was switched. Write all repos' outcomes to `temp/refresh-log.md` (one line per repo, e.g. `repo-name | main | updated | switched from feature-x`) so Steps 3 and 5 can use them even if earlier context is lost. Pass each repo's outcome to its Step 2 sub-agent.

Do not restore the original branch automatically; list repos that were switched in the Step 5 completion message so the user can switch back.

Proceed to [Step 2 — Per-repository sub-agents](./REFERENCE-STEP2-ANALYSE.md).
