# Step 4 — Self-Quiz Loop

The coordinator generates a set of **10–15 questions** that a new team member would reasonably ask about the repository / organisation. Questions should cover:

- Purpose and ownership
- How to run or deploy the code
- Key domain terms
- Where to find configuration and secrets
- The relationship between repositories (if multiple)
- Which repositories are forks, and of what upstream
- Which repo to start with for a given task

## Loop

1. Spawn **answer-agents** (one or more) with the instruction:
   > _"Using only `DOMAIN-LANGUAGE.md`, `MAP.md`, `SUMMARY.md`, and `DETAILS.md` (ignore its `### FAQ` sections — they are written after this loop) — do not read source code — answer the following questions."_

   If sub-agents cannot be spawned, answer the questions yourself strictly from those documents and say so in the completion message.

2. Evaluate answers: mark each as **correct**, **partial**, or **incorrect**.

3. Calculate pass rate: `(correct + 0.5 × partial) / total`.

4. If pass rate **< 90%**:
   - Identify which questions failed.
   - Investigate the relevant repository source to find the missing information.
   - Update `DETAILS.md`, `DOMAIN-LANGUAGE.md`, `MAP.md`, or `SUMMARY.md` as needed.
   - Re-run the quiz (same questions plus any new ones surfaced during investigation).

5. Repeat until pass rate **≥ 90%** for two consecutive rounds. Two rounds (rather than one) guards against a fluke pass — if the documents genuinely cover the material, a second independent run should confirm it.

   **If the pass rate stalls** (no meaningful improvement after three total iterations, including any successful passes), stop iterating and add a note at the top of `SUMMARY.md` (above the Overview, and above any "Not fully refreshed" line) listing the questions that couldn't be answered from the documents, so the user knows where gaps remain.

## Writing verified Q&A into `DETAILS.md`

Once the quiz loop reaches ≥ 90% for two consecutive rounds (or stops due to stalling), write the verified answers into the `### FAQ` section of each repository's entry in `DETAILS.md`:

- Include questions marked **correct** or **partial** — these have verified answers.
- Omit questions marked **incorrect** or unanswered — do not write unverified content.
- Group questions by repository: include only questions that are answerable from that repository's own documentation (not org-wide questions about relationships between repos — those belong in `SUMMARY.md`).
- The four baseline questions (run locally, deploy, configuration, secrets) should always appear first if they were answered; repo-specific Q&A follows.
- Skip the FAQ section entirely for `stale` and `archived` repositories (light entry treatment).

Proceed to [Step 5 — Clean-up](./REFERENCE-STEP5-CLEANUP.md).
