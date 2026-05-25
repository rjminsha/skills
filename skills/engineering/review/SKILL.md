---
name: review
description: Post-TDD code review. Use when the TDD loop is complete and the user wants to: (1) assess code against rules, (2) verify user stories and requirements are met by the implementation, (3) check for unintended drift in MAP/LANGUAGE/decisions, and (4) ask "could we do better?" against quality goals — surface area, supply chain, existing modules, functionality, complexity, documentation clarity, and test clarity.
---

# Review

A structured review that runs after the TDD loop is complete. The goal is not to re-do the design — it is to catch drift, verify requirements are met, and ask a focused "could we do better?" before the work is considered done.

## Inputs

Preferred inputs — provide as many as exist:

- **`RULES.md`** — corporate and industry rules to assess the code against
- **`docs/user-stories/0001-*.md`** — user stories and BDD scenarios to verify
- **`docs/user-stories/0001-*-TRI.md`** — architecture, requirements, and testing strategy for this feature
- **`LANGUAGE.md`** — canonical domain terms; check for unintended drift
- **`MAP.md`** — system map; check for unintended drift
- **`docs/decisions/`** — existing decisions; check for unintended drift

## Process

### 1. Rules check

Read `RULES.md` and assess the implementation against each rule. Report:

- Which rules are satisfied
- Which rules are violated or at risk
- Any rules that are ambiguous in this context

### 2. Requirements check

Read the user stories and TRI.md. For each BDD scenario and stated requirement, assess:

- Is it implemented?
- Does the implementation match the intended behavior?
- Are there gaps, partial implementations, or mismatches?

### 3. Documentation drift check

Compare `LANGUAGE.md`, `MAP.md`, and `docs/decisions/` against the implementation:

- Are any domain terms used differently in code than in LANGUAGE.md?
- Does MAP.md still accurately reflect the system after these changes?
- Were any hard-to-reverse or surprising decisions made during TDD that should be recorded?

**At this point in the workflow, changes to MAP, LANGUAGE, and decisions should be rare.** Flag any drift — it typically indicates scope creep, an unresolved design question, or a decision made during coding that bypassed the design process. If updates are needed, record them before moving on.

### 4. Quality review

Ask: "Could anything be done better?" Evaluate against the following goals, in priority order:

| Goal | Question |
|---|---|
| **Limit surface area of changes** | Are changes confined to the expected components? Does anything touch more than it needs to? |
| **Limit supply chain risks** | Were any dependencies added during TDD that weren't in the TRI? Are they necessary? Are they trusted, maintained, and correctly licensed? (`/requirements-interlock` checks proposed dependencies at design time; this catches anything adopted during coding.) |
| **Work within existing modules and design** | Does new code fit the existing design, or does it introduce a pattern that conflicts with what is already there? |
| **Maintain existing functionality** | Is there any regression risk? Do existing tests still pass and still describe meaningful behavior? |
| **Reduce complexity, improve readability** | Is the new code simpler than the alternative? Are there opportunities to flatten, clarify, or remove indirection? |
| **Clarity of documentation** | Is documentation accurate, concise, and useful to the next developer or agent? |
| **Clarity of test cases** | Do tests read like specifications? Do they describe behavior, not implementation details? |

For each goal: satisfied, partially satisfied, or at risk — with specific examples where applicable.

## Output

A short review report with four sections:

1. **Rules** — pass / fail / risk per rule
2. **Requirements** — met / not met / partial per scenario
3. **Drift** — none, or specific updates needed to MAP / LANGUAGE / decisions
4. **Quality** — satisfied / at risk per goal, with specific examples and suggested improvements

End by asking the user which (if any) issues to address before moving on.
