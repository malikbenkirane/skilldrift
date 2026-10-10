---
name: writing-plans
description: Create detailed implementation plans from specs before coding. Trigger when user provides requirements, asks for a plan, says "how should I implement X", or needs to break down a multi-step feature. Use BEFORE touching any code.
---

# Writing Plans

> **Diverged from obra/superpowers:** added a complexity gate + multi-plan split workflow (directory with `00-index.md`, concurrency batches). See `docs/issues/26.md`.

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

## Prerequisites

**REQUIRED:** Load the `core-commands` skill for VCS operations (uses `jj`, not `git`). Load the `caveman-commit` skill for commit message descriptions. All commit steps in plans reference the `core-commands` commit sequence (mktemp + Read + Write + `jj describe --stdin`). Never inline a commit message into a bash one-liner.

## Complexity Gate

After exploration, **before writing any plan file**, estimate the plan's size using worst-case arithmetic: `worst-case lines per task × total numbered tasks` (each TDD task is typically 60-170 lines when it contains full code, test, and the commit sequence; never justify shortness with "similar to Task N" — placeholders are forbidden).

If the estimate exceeds **600 lines** OR the task count exceeds **15 tasks**, the plan is oversized. Do not write it as one file. Instead, in chat (going ahead without waiting if the user is hands-off):

1. Propose a split into sub-plans and the proposed boundaries (group tasks per sub-plan),
2. On approval (or a decline, recorded as below), proceed to **Multi-Plan Split** below.

If the user **declines** the split, note it: record "gate fired; user chose single file" in a `## Complexity` line in the plan header, and continue single-file. Do not re-ask.

**If a plan oversized while being written (Q5):** finish it as a deliberately small single-file plan, then propose the split in chat and convert in the next change. Never leave half-converted plans on disk.

**Boundary changes after execution starts (Q10):** finish the current sub-plan first, rewrite `00-index.md`, and get approval of the change before the next sub-plan.

## Multi-Plan Split

When the gate fires and the user approves, output is a **directory**, not one file:

- `docs/plans/<DATETIME>_<TITLE>/` — `00-index.md` first (`01-…` etc. written after), one sub-plan at a time, never in parallel writing.

**`00-index.md` contains (keep compact):**

```markdown
# <Title> — Index

Sub-plans (execution order):
- [ ] 01-<subtitle> — <one-line scope>
- [ ] 02-<subtitle> — <one-line scope>

Concurrent-eligible batches: `[01-x, 02-y], [03-z]`

Why this order and split: <short rationale>. Each batch is the safe unit for one concurrent session; same-file edits stay within a batch.
```

Each sub-plan is a normal single plan run through `supervised-plan-execution`; highlight independent sub-plans so they can run in concurrent sessions per the batches. When a sub-plan finishes, tick its checkbox in `00-index.md` in a **separate commit** (core-commands temp-file pattern), message: `docs(plans): mark <NN>-<subtitle> done in <title>` (e.g. `docs(plans): mark 02-sessions done in auth-refactor`).

If the gate fired but the user declined the split, write `00-index.md` anyway recording only: "gate fired; user chose single file" plus the single plan path.

## Rationalization Table

| Rationalization | Why it's wrong |
|---|---|
| "It's really just a few tasks" | Count them. The 600-line estimate is mandatory before writing. TDD + no-placeholder rules make tasks far longer than they feel. |
| "The user asked for a plan, not a split" | The gate is a measure-then-act rule, not a suggestion. A declined split is recorded once; re-asking is not required. |
| "We've already started, just keep going" (sunk cost) | Prior effort is not a size criterion. Finish small and propose the split — never a half-converted plan. |
| "It's one subsystem, so no gate" | The gate measures document size, not domain granularity. One subsystem can exceed the lines threshold. |

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- **"Commit with caveman-commit"** - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use supervised-plan-execution to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit with caveman-commit**

Use `caveman-commit` for the message content, then the commit sequence from `core-commands`:

1. `mktemp`
2. Read the temp file (required even though empty)
3. Write the commit message to the temp file
4. `jj describe --stdin < "/tmp/tmp.XXXXXX" && rm "/tmp/tmp.XXXXXX" && jj new`
```
````

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, TDD, frequent commits

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**Optional:** For independent verification, dispatch a reviewer subagent using the template in `plan-document-reviewer-prompt.md`.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Plan Save Location

**Save plans to:** `docs/plans/<slug-name>.md`

- `slug-name` = kebab-case feature description (e.g., `user-auth-flow.md`, `api-rate-limiting.md`)
- When the complexity gate fired and was approved: `docs/plans/<DATETIME>_<TITLE>/` directory with `00-index.md` and `01-<subtitle>.md`, `02-<subtitle>.md`, … (dashes, not underscores)
- User preferences override this default

## Execution Handoff

After saving the plan:

**"Plan saved to `docs/plans/<slug-name>.md`. Ready to execute with `supervised-plan-execution` — sequential implementation with two-stage self-review after each task. Proceed?"**
