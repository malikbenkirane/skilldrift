# grill-me rewrite testing notes (issue 28)

Working record for the grill-me question-shaping rewrite: RED evidence, failure
checklist, cap simulation, and future-refinement notes. Feed these back into the
SKILL.md edit before it is deployed.

## Failure checklist (what counts as wrong shape)

1. Compound question: one question bundling two or more decisions ("use jj, and who reviews…").
2. Frame buried: answer choices appear before or without the decision context.
3. Overloaded round: more than the cap of questions served at once.
4. No state recap: round opens without a settled-so-far / frontier line.
5. Prose-wall closing: shared-understanding summary as narrative instead of per-branch legend.

Depth checklist (what must NOT disappear — original grill-me behaviors):

- Frontier/rounds design-tree model, recomputed each round from answers.
- Facts are the interviewer's job (sub-agent dispatch), never the user's.
- A running exploration settles nothing downstream until it reports; the rest of the frontier is still asked now.
- Rounds continue until the frontier is empty; no silent assumptions.
- Nothing acted on until the user confirms shared understanding.

## RED baseline

Recorded against `docs/issues/28-original-grill-me.md` (verbatim copy of the pre-rewrite skill).

- **Scenario (a) — this issue** (28.md residual-question interview, 2026-10-15, session with repo owner): original grill-me ran 6 questions in one round (Q1–Q6, over the working-set comfort zone); each question was single-decision and frame-first (correct), but no per-round state recap, and no closing legend prescribed anywhere in the skill — the summary would be free-form.
- Scenarios (b) system design and (c) code architecture: simulated by the interviewer (see cap simulation below); original skill leaves compound question and closing-legend shapes entirely unguarded.

## Cap simulation (residual B)

Self-simulated multi-round series, interviewer-side rotation, on scenario (b) — vague plan "split the monolith into services". Design tree held 12 questions (stack choice, service granularity, data ownership, API contract style, deployment target, migration order, CI impact, team ownership, observability, feature flags, dashboard links, capacity plan).

- **Cap 5:** rounds serve 5 / 5 / 2; as answers landed, downstream questions came forward naturally and every retained question was served within at most two rounds. No inversions (a question answered only to find its prerequisite still open). No drops when tree re-shaped.
- **Cap 4:** same invariant holds, rounds 5 → slightly longer series; no further readability gain.
- **Cap 3:** invariant holds but 4+ rounds of 3 — the user re-contextualises more often than the per-round relief is worth.
- **Conclusion: keep 5** (questions per round, choices per question, bullets per branch in the closing legend). The retained-and-served-later rule, not the cap value, carries the no-drops invariant; confirmed across all three caps.

## GREEN checks (current draft skill, live)

- **Session (a), round 2 (2026-10-15)**: recap slot present; 3 questions served, single-decision, frame before choices with recommendation last. User feedback pending.
- **Extensive user test (2026-10-15)**: content/behavior compliant (cap, IDs, legend, depth); formatting feedback — temp draft "**lacks spacing and structure**". Fix agreed by grill (round A): (A1) fix the temp draft in `~/.config` only, carry into repo as-is at the step-1 write; (A2) section headings + prose-to-bullets where lists explain rules + short glossary of domain terms (design tree, frontier, retained, recap, legend) + larger round-template block; (A3) template stays inside a code fence per CommonMark (no `<br>`, renderer-independent). Fix applied same day; to be re-tested in the next session before the repo write.
- **Full-render check (2026-10-15, user-provided Round 3 output on a pragma/jj epic)**: template-compliant across recap (tags, retention accounting 5+2), divider, per-round IDs, numbered one-per-line choices, blank line before each recommendation. Earlier "violations" from a partial excerpt were paste artifacts, retracted.
- **Frame legibility fix (2026-10-15, round B)**: user found question frames hard to read — 100+-word single-paragraph walls burying the failure condition; frame used as container for case derivations ((a)/(b)/(c) enumerations). Amendment (B1): frame = 2–3 role-separated short paragraphs (settled context / concrete failure-or-consequence / one-sentence decision point), each **max 2 source lines of ≤ 80 chars, 1–2 sentences**; enumerations inside frames are either facts (sub-agent dispatch) or move into the choices. Checkable criterion chosen over "rendered lines" (CommonMark soft-wrap makes rendered width renderer-dependent) and over char-counting (fiddly mid-generation); two redundant rules (width, sentences) so either trips a split. Applied to the template; re-test before repo write.

## Future refinement notes

- Watch for: interviewers padding the recap slot with narrative instead of decisions settled/not settled.
- If rounds routinely fill the full cap in later rounds, consider recommending the cap drop to 3 for round 3+ (late tokens of a tree are usually leaves).
- Depth-vs-brevity regressions should be fixed first in the template, not by adding prose.
