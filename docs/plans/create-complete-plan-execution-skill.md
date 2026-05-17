# Merge executing-plans and subagent-driven-development Skills

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create unified SKILL.md that merges sequential plan execution with two-stage self-review (no subagent dispatching)

**Architecture:** Combine executing-plans workflow with subagent-driven-development's review gates. SKILL.md references existing prompt templates (implementer-prompt.md, spec-review-prompt.md, code-quality-reviewer-prompt.md) which become self-review checklists instead of subagent dispatch instructions.

**Tech Stack:** Markdown skill file

## Table of Contents

<!-- toc -->
- [Task 1: Create Merged SKILL.md](#task-1-create-merged-skillmd)
  - [Write Frontmatter and Overview](#write-frontmatter-and-overview)
  - [Add Workflow Diagram](#add-workflow-diagram)
  - [Add Detailed Process Steps](#add-detailed-process-steps)
  - [Add Self-Review Section](#add-self-review-section)
  - [Add When to Stop Section](#add-when-to-stop-section)
  - [Add Red Flags and Remember Sections](#add-red-flags-and-remember-sections)
  - [Add Integration Section](#add-integration-section)
  - [Commit](#commit)
- [Task 2: Update Template Files for Self-Review Context](#task-2-update-template-files-for-self-review-context)
  - [Update Implementer Prompt](#update-implementer-prompt)
  - [Update Spec Review Prompt](#update-spec-review-prompt)
  - [Update Code Quality Reviewer Prompt](#update-code-quality-reviewer-prompt)
  - [Commit](#commit-1)
- [Task 3: Delete Old Skill Files](#task-3-delete-old-skill-files)
  - [Commit](#commit-2)
- [Verification](#verification)
<!-- /toc -->

<!-- --- -->

## Task 1: Create Merged SKILL.md

**Files:**

- Create: `skills/complete-plan-execution/SKILL.md`
- Delete: `skills/complete-plan-execution/executing-plans.md`
- Delete: `skills/complete-plan-execution/subagent-driven-development.md`
- Keep: `skills/complete-plan-execution/implementer-prompt.md` (update for self-review)
- Keep: `skills/complete-plan-execution/spec-review-prompt.md` (update for self-review)
- Keep: `skills/complete-plan-execution/code-quality-reviewer-prompt.md` (update for self-review)

**Design Decisions:**

- One agent executes entire plan sequentially
- Two-stage self-review after each task (spec compliance → code quality)
- SKILL.md references prompt templates (not inline) - templates remain as separate files
- Keep the workflow diagrams from subagent-driven-development but simplify for single-agent flow
- Preserve the "continuous execution" principle - no stopping between tasks unless blocked

<!-- --- -->

### Write Frontmatter and Overview

- [ ] **Step 1: Write frontmatter and overview**

```markdown
---
name: complete-plan-execution
description: Execute implementation plans sequentially with two-stage self-review per task. TRIGGER when you have a written implementation plan to execute, when asked to "execute this plan", "implement this plan", or when starting work on a plan file with checkbox tasks. Use this skill whenever you see a plan.md or similar file with `- [ ]` task syntax, even if the user doesn't explicitly say "execute the plan."
---

# Complete Plan Execution

## Overview

Load plan, execute all tasks sequentially with two-stage self-review after each, report when complete.

**Announce at start:** "I'm using the complete-plan-execution skill to implement this plan."

## Why This Approach Works

**Why two-stage review?** Spec compliance catches wrong builds (you built the wrong thing). Code quality catches bad builds (you built the right thing poorly). Both matter — spec compliance first because there's no point polishing code that solves the wrong problem.

**Why continuous execution?** The user asked you to execute the plan. Stopping to ask "should I continue?" after each task wastes their time. Trust the plan — execute it. Stop only when genuinely blocked or done.

**Why self-review instead of subagents?** You have full context from implementing the task. Use it — read your own code critically, compare to the spec, catch issues early. You're more efficient than spawning fresh agents who need context loaded.

**Core principle:** One agent, sequential execution, two-stage self-review per task = high quality implementation without context switching
```

### Add Workflow Diagram

- [ ] **Step 2: Add workflow diagram**

Simplify the subagent-driven-development diagram to show single-agent flow:

```markdown
## The Process

```dot
digraph process {
    rankdir=TB;

    subgraph cluster_per_task {
        label="Per Task";
        "Implement task (follow steps exactly)" [shape=box];
        "Run verifications" [shape=box];
        "Self-review: spec compliance checklist" [shape=box];
        "Spec compliant?" [shape=diamond];
        "Fix spec gaps" [shape=box];
        "Self-review: code quality checklist" [shape=box];
        "Code quality approved?" [shape=diamond];
        "Fix quality issues" [shape=box];
        "Mark task complete" [shape=box];
    }

    "Read plan, extract all tasks, create TodoWrite" [shape=box];
    "More tasks remain?" [shape=diamond];
    "Final review of entire implementation" [shape=box];
    "Use superpowers:finishing-a-development-branch" [shape=box style=filled fillcolor=lightgreen];

    "Read plan, extract all tasks, create TodoWrite" -> "Implement task (follow steps exactly)";
    "Implement task (follow steps exactly)" -> "Run verifications";
    "Run verifications" -> "Self-review: spec compliance checklist";
    "Self-review: spec compliance checklist" -> "Spec compliant?";
    "Spec compliant?" -> "Fix spec gaps" [label="no"];
    "Fix spec gaps" -> "Self-review: spec compliance checklist";
    "Spec compliant?" -> "Self-review: code quality checklist" [label="yes"];
    "Self-review: code quality checklist" -> "Code quality approved?";
    "Code quality approved?" -> "Fix quality issues" [label="no"];
    "Fix quality issues" -> "Self-review: code quality checklist";
    "Code quality approved?" -> "Mark task complete" [label="yes"];
    "Mark task complete" -> "More tasks remain?";
    "More tasks remain?" -> "Implement task (follow steps exactly)" [label="yes"];
    "More tasks remain?" -> "Final review of entire implementation" [label="no"];
    "Final review of entire implementation" -> "Use superpowers:finishing-a-development-branch";
}
```

### Add Detailed Process Steps

- [ ] **Step 3: Add detailed process steps**

```markdown
### Step 1: Load and Extract Plan

1. Read plan file once
2. Extract ALL tasks with their full text (don't re-read file per task)
3. Note the context and how tasks connect
4. Create TodoWrite with all tasks
5. Review critically - identify any questions or concerns
6. If concerns: Raise with your human partner before starting
7. If no concerns: Proceed to execution

### Step 2: Execute Tasks

For each task:

1. **Mark as in_progress**

2. **Implement** - Follow each step exactly as written

3. **Run verifications** - Execute commands specified in task steps

4. **Self-review: Spec Compliance**
   - Read your own implementation code
   - Compare against task requirements line by line
   - Check: Missing requirements? Extra unrequested work? Misunderstandings?
   - If issues: Fix them, then re-review
   - Do NOT proceed until spec compliance passes

5. **Self-review: Code Quality**
   - Review for: clean code, good names, proper tests, YAGNI
   - Check: Single responsibility per file? Following plan's file structure?
   - If issues: Fix them, then re-review
   - Do NOT proceed until code quality passes

6. **Commit** - Use temp file pattern from core-commands:
   
   mktemp
   # Returns: /tmp/tmp.XXXXXX
   # Use Write tool to save commit message to temp file
   jj describe --stdin < "/tmp/tmp.XXXXXX"
   rm "/tmp/tmp.XXXXXX"
   jj new

7. **Mark as completed**

### Step 3: Final Review and Complete

After all tasks complete, review entire implementation for coherence
```

### Add Self-Review Section

- [ ] **Step 4: Add self-review section referencing templates**

```markdown
## Self-Review

After implementing each task, perform two-stage self-review. This is where quality happens — don't skip it.

### Implementation Guidance

Before starting each task, review `./implementer-prompt.md` for:
- Code organization principles (one responsibility per file, clear interfaces)
- When to stop and ask for help (architectural decisions, unclear requirements)
- Self-review checklist (completeness, quality, discipline)

### Stage 1: Spec Compliance

Use the checklist in `./spec-review-prompt.md` to verify:
- Everything requested was implemented
- Nothing extra was built
- No misunderstandings

**Critical:** DO NOT trust your own claims about what you built. Read your actual code. Compare it to the task requirements line by line. It's easy to think you implemented something when you actually didn't.

### Stage 2: Code Quality

After spec compliance passes, use `./code-quality-reviewer-prompt.md` to verify:
- Clean, maintainable code
- Good names, proper tests
- YAGNI, single responsibility

**Why this order?** There's no point polishing code that solves the wrong problem. Spec compliance first, then quality.

**Fix issues before committing.** Not "later" — now. Fresh context makes debugging easier.

### Commit After Each Task

Once both reviews pass, commit your work using the temp file pattern from core-commands:

   mktemp
   # Returns: /tmp/tmp.XXXXXX
   # Use Write tool to save commit message to temp file
   jj describe --stdin < "/tmp/tmp.XXXXXX"
   rm "/tmp/tmp.XXXXXX"
   jj new

**Why commit after each task?** Atomic commits make review easier, enable rollback, and document your progress.
```

### Add When to Stop Section

- [ ] **Step 5: Add when to stop section**

```markdown
## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly after genuine attempts to fix
- You've tried multiple approaches and nothing works

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to plan review when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking
- You discover the plan has a structural flaw

**Don't force through blockers** - stop and ask.
```

### Add Red Flags and Remember Sections

- [ ] **Step 6: Add red flags and remember sections**

```markdown
## Red Flags

**Never:**
- Skip self-reviews (spec compliance AND code quality)
- Skip commits after completing a task
- Proceed with unfixed issues from self-review
- Mark task complete while either review has open issues
- Accept "close enough" on spec compliance
- Skip re-review after fixing issues
- Over-build beyond what was requested
- Add "nice to have" features not in spec

## Remember

- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Stop when blocked, don't guess
- Self-review requires honesty - be critical of your own work
- Fix issues immediately, not "later"
- Two-stage review: spec compliance FIRST, then code quality
- Commit after each task (atomic commits = easier review + rollback)
- Use temp file pattern from core-commands for multi-line content
```

### Add Integration Section

- [ ] **Step 7: Add integration section**

```markdown
## Integration

**Required workflow skills:**
- **core-commands** - VCS operations and commit patterns
- **writing-plans** - Creates the plan this skill executes
- **karpathy-guidelines** - Behavioral guidelines for quality implementation
```

### Commit

- [ ] **Step 8: Commit SKILL.md creation**

```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```

Use Write tool to save commit message to temp file:
```
feat(skills): create complete-plan-execution skill

Merges executing-plans and subagent-driven-development into unified
skill with sequential execution and two-stage self-review.
```

Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 2: Update Template Files for Self-Review Context

**Files:**

- Modify: `skills/complete-plan-execution/implementer-prompt.md`
- Modify: `skills/complete-plan-execution/spec-review-prompt.md`
- Modify: `skills/complete-plan-execution/code-quality-reviewer-prompt.md`

The templates currently use "the implementer" and "dispatch subagent" language. Update them to work as self-review checklists where the agent reviews its own work.

### Update Implementer Prompt

- [ ] **Step 1: Update implementer-prompt.md**

Change:

- "You are implementing Task N" → "Self-review before and during implementation"
- Remove "Report Format" section (no dispatching)
- Keep the guidance: code organization, when to escalate, self-review checklist
- Change "Dispatch implementer subagent" header to "Implementation Self-Guidance"

### Update Spec Review Prompt

- [ ] **Step 2: Update spec-review-prompt.md**

Change:

- "The implementer finished suspiciously quickly" → "You finished implementing. Now verify."
- "DO NOT trust the report" → "DO NOT trust your own claims"
- Remove subagent dispatch template wrapper
- Keep the checklist content

### Update Code Quality Reviewer Prompt

- [ ] **Step 3: Update code-quality-reviewer-prompt.md**

Change:

- Remove reference to `requesting-code-review/code-reviewer.md` (inline the relevant parts)
- Keep the quality checklist
- Remove "Task tool (general-purpose)" dispatch wrapper

### Commit

- [ ] **Step 4: Commit template updates**

```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```

Use Write tool to save commit message to temp file:
```
refactor(skills): update templates for self-review context

Remove dispatch language, change to self-guidance format.
```

Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 3: Delete Old Skill Files

**Files:**
- Delete: `skills/complete-plan-execution/executing-plans.md`
- Delete: `skills/complete-plan-execution/subagent-driven-development.md`

- [ ] **Step 1: Delete executing-plans.md**

```bash
rm skills/complete-plan-execution/executing-plans.md
```

- [ ] **Step 2: Delete subagent-driven-development.md**

```bash
rm skills/complete-plan-execution/subagent-driven-development.md
```

- [ ] **Step 3: Verify only 4 files remain**

Run: `ls skills/complete-plan-execution/`
Expected: `SKILL.md`, `implementer-prompt.md`, `spec-review-prompt.md`, `code-quality-reviewer-prompt.md`

### Commit

- [ ] **Step 4: Commit file cleanup**

```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```

Use Write tool to save commit message to temp file:
```
chore(skills): remove merged skill files

Delete executing-plans.md and subagent-driven-development.md,
replaced by unified complete-plan-execution skill.
```

Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Verification

1. Read `skills/complete-plan-execution/SKILL.md`:
   - Description includes trigger phrases (not just what it does)
   - "Why This Approach Works" section explains reasoning
   - Self-review section references all 3 template files
   - No references to dispatching subagents

2. Verify template files updated for self-review:
   - `implementer-prompt.md` - no "dispatch" language, reads as self-guidance
   - `spec-review-prompt.md` - "you" not "the implementer", no subagent wrapper
   - `code-quality-reviewer-prompt.md` - no external references, standalone checklist

3. Run `ls skills/complete-plan-execution/` - expect exactly 4 files:
   - SKILL.md
   - implementer-prompt.md
   - spec-review-prompt.md
   - code-quality-reviewer-prompt.md

4. Grep all 4 files for "subagent" or "dispatch" - should find zero matches

5. Confirm skill reads as coherent: a single agent executing a plan with self-review gates
