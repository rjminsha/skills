# Step 5 — Clean-up

1. **Compose the completion message** (below) first — it reads `temp/refresh-log.md`, which is deleted in the next step.
2. Ask the user:

   > "Sub-agent temp files are in `temp/`. Would you like to delete them?"

   - If **yes**: delete all files in `temp/` (i.e. `temp/*-analysis.md`, `temp/*-domain.md`, and `temp/refresh-log.md`).
   - If **no**: leave them in place and include their location in the completion message.
3. **Post the completion message.**

## Completion message

The completion message includes:

- Number of repositories analysed.
- Repositories that were **not** fully refreshed (skipped, fetch only, or failed) with the reason — their analysis may be out of date. Source: `temp/refresh-log.md`.
- Repositories whose branch was switched to the default branch, with the original branch name, so the user can switch back. Source: `temp/refresh-log.md`.
- Repositories whose fork status was `unknown`.
- Final quiz pass rate (from Step 4).
- Paths of the four output files (`DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, `SUMMARY.md`).
- Any known gaps — questions the quiz loop could not answer (as listed in `SUMMARY.md` if the pass rate stalled).

## File layout after completion

```
<output-root>/
├── DETAILS.md            ← per-repository deep-dive, alphabetical (created/updated)
├── DOMAIN-LANGUAGE.md    ← domain glossary (created/updated)
├── MAP.md                ← structural index (created/updated)
├── SUMMARY.md            ← high-level summary with links to DETAILS.md (created/updated)
└── temp/                 ← temporary files (deleted if user agrees)
    ├── refresh-log.md
    ├── [repo]-analysis.md
    └── [repo]-domain.md
```
