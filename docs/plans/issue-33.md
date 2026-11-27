# PR: issue-linked plan naming (`issue-<NN>` infix)

**Base:** dev
**Head:** issue-33
**References:** docs/issues/33.md

## Summary

Plans addressing a filed issue were indistinguishable by name from any other plan: `docs/plans/<slug-name>.md` carried no pointer back to the motivating issue, so locating "the plan that fixed issue N" required opening/grepping every plan file. This PR adds an `issue-<NN>` infix, applied only when the plan request explicitly references an existing `docs/issues/NN.md` file.

## Changes

- `skills/writing-plans/SKILL.md § Plan Save Location` — new rule, written as an observable predicate:
  - Explicit issue reference **and** `docs/issues/NN.md` exists → `docs/plans/<DATETIME>_issue-<NN>_<fitted_name>.md` (single file) plus an `**Issue:** docs/issues/NN.md` header line
  - Same condition with the complexity gate fired and split approved → `docs/plans/<DATETIME>_issue-<NN>_<TITLE>/`
  - Issue file missing → plain pattern, no infix (avoids broken links)
  - No issue referenced → existing patterns unchanged; no globbing for "may relate to" issues
- `skills/writing-plans/SKILL.md § Rationalization Table` — 3 rows harvested from the pressure scenario: "close enough to create the issue" (existence required), "body mention suffices" (infix + header link are the rule), "I'll guess which issue fits" (explicit reference only).
- `docs/issues/33.md` — open questions resolved as Q4–Q6 (existence check, header link, no execution-skill changes); RED/GREEN verification notes recorded.
- `docs/plans/issue-33.md` — this document.

## Verification (issue-26 discipline)

- **RED** (pre-edit subagent scenario, "user: write a plan to address issue 7"): followed the plain rule verbatim, no issue linkage, invented an unsupported `issue-7-` slug prefix.
- **GREEN** (same scenario post-edit): confirmed `docs/issues/7.md` existence, applied the infix rule verbatim, produced the `**Issue:** docs/issues/7.md` header line.

## Test plan

- [x] RED baseline recorded before the edit
- [x] GREEN verified after the edit
- [x] Rationalizations harvested into the skill's table
- [x] Unrelated plans unaffected (plain pattern retained in the rule)
