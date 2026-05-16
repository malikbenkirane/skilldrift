# core-commands Skill Restructure Plan

<!-- toc -->
- [Files](#files)
- [Task 1: Fix YAML Frontmatter Description](#task-1-fix-yaml-frontmatter-description)
- [Task 2: Add Missing Overview Section](#task-2-add-missing-overview-section)
- [Task 3: Consolidate Prohibited Commands Section](#task-3-consolidate-prohibited-commands-section)
- [Task 4: Consolidate Command Alternatives Table](#task-4-consolidate-command-alternatives-table)
- [Task 5: Streamline GitHub Commands Table](#task-5-streamline-github-commands-table)
- [Task 6: Refactor Temp File Pattern Section](#task-6-refactor-temp-file-pattern-section)
- [Task 7: Add Common Mistakes Section](#task-7-add-common-mistakes-section)
- [Task 8: Final Verification](#task-8-final-verification)
- [Verification](#verification)
<!-- /toc -->

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restructure core-commands skill to follow writing-skills guidelines, reduce tokens, and improve discoverability.

**Architecture:** Single-file restructure with improved CSO (description), consolidated sections, and token-efficient formatting. Target <100 lines for frequently-loaded skill.

**Tech Stack:** Markdown, YAML frontmatter

<!-- --- -->

## Files

**Modify:**
- `skills/core-commands/SKILL.md` - Complete restructure

<!-- --- -->

## Task 1: Fix YAML Frontmatter Description

**Files:**
- Modify: `skills/core-commands/SKILL.md:1-4`

- [ ] **Step 1: Replace description with CSO-compliant version**

Change from:
```yaml
---
name: core-commands
description: Core command guidelines - prohibited commands and temp file patterns. ALWAYS load this skill at session start.
---
```

To:
```yaml
---
name: core-commands
description: Use when running any shell command, especially jj/git operations, or creating multi-line content via temp files
---
```

**Why:** Description now describes triggering conditions (when to use) rather than summarizing content. Removes workflow instruction ("ALWAYS load...") that belongs in skill body.

- [ ] **Step 2: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
fix(core-commands): CSO-compliant description

Change description to triggering conditions only.
Remove workflow instruction from frontmatter.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 2: Add Missing Overview Section

**Files:**
- Modify: `skills/core-commands/SKILL.md:6`

- [ ] **Step 1: Add Overview section after frontmatter**

Insert after frontmatter:
```markdown
# Core Commands

## Overview

This repo uses `jj` exclusively. Git commands are prohibited. Multi-line content must use mktemp + Write tool pattern.

## When to Use

- Before running any shell command
- Working with jj/git operations
- Creating temp files for multi-line content
- **When NOT to use:** Simple single-line commands with no temp files
```

**Why:** Standard SKILL.md structure requires Overview (core principle) and When to Use sections.

- [ ] **Step 2: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
docs(core-commands): add Overview and When to Use sections

Standard SKILL.md structure for skill discoverability.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 3: Consolidate Prohibited Commands Section

**Files:**
- Modify: `skills/core-commands/SKILL.md:10-50` (current "Before Running Any Command" and "Prohibited Commands" sections)

- [ ] **Step 1: Remove redundant "Before Running Any Command" checklist**

Delete lines 10-21 (the numbered checklist). This information is covered by the prohibited commands table.

- [ ] **Step 2: Retain "Prohibited Commands" intro and table**

Keep:
```markdown
## Prohibited Commands

NEVER use git commands. This repo uses `jj` exclusively.

**Prohibition is prefix-based**: Commands matching the start of a prohibited pattern are forbidden (e.g., `jj log -r 'all()'` matches `jj log*`).

### Prohibited jj Commands (Prefix Patterns)
[Keep existing table lines 29-40]
```

- [ ] **Step 3: Retain "Prohibited Actions" list**

Keep lines 42-49 unchanged.

**Why:** Removes redundancy. The table and actions list provide the same information more efficiently.

- [ ] **Step 4: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
refactor(core-commands): remove redundant checklist

Prohibited commands table covers same information.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 4: Consolidate Command Alternatives Table

**Files:**
- Modify: `skills/core-commands/SKILL.md:51-66`

- [ ] **Step 1: Keep Command Alternatives table unchanged**

This table is already efficient and scannable. No changes needed.

**Why:** Table format is optimal for quick reference. Token-efficient.

- [ ] **Step 2: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
docs(core-commands): clarify temp file pattern for gh commands
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 5: Streamline GitHub Commands Table

**Files:**
- Modify: `skills/core-commands/SKILL.md:68-79`

- [ ] **Step 1: Add header note about temp file pattern**

Before the table, add:
```markdown
## GitHub (`gh`) Commands

All multi-line content (issue bodies, PR descriptions, comments) uses the temp file pattern below.
```

- [ ] **Step 2: Keep table unchanged**

Table is already efficient.

**Why:** Clarifies that temp file pattern applies to gh commands without repeating the workflow.

- [ ] **Step 3: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
docs(core-commands): clarify temp file pattern for gh commands
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 6: Refactor Temp File Pattern Section

**Files:**
- Modify: `skills/core-commands/SKILL.md:81-145`

- [ ] **Step 1: Condense the 5-step workflow**

Replace verbose explanation with:
```markdown
## Temp File Pattern for Multi-Line Content

**NEVER use heredocs (`cat <<'EOF'`) or `echo` for multi-line content.**

Each Bash tool invocation runs in an independent shell. Variables don't persist. Use this pattern:

1. `mktemp` → returns path like `/tmp/tmp.XXXXXX`
2. Read tool → check existing content
3. Write tool → write content to that path
4. Pass path to command: `cmd --option "/tmp/tmp.XXXXXX"`
5. Clean up: `rm "/tmp/tmp.XXXXXX"`
```

- [ ] **Step 2: Consolidate to ONE example**

Replace all three examples with one complete example:
```markdown
### Example: Creating GitHub Issue

```bash
mktemp
# Tool returns: /tmp/tmp.AbCdEf

# Use Read tool to check existing content (if any)
# Use Write tool to write issue description to /tmp/tmp.AbCdEf

gh issue new -t 'Issue title' -F "/tmp/tmp.AbCdEf"
rm "/tmp/tmp.AbCdEf"
```
```

- [ ] **Step 3: Remove "Why This Matters" section**

The "Why This Matters" section (lines 141-145) is redundant with the opening prohibition statement.

**Why:** Reduces from ~60 lines to ~25 lines. One complete example is sufficient (agents can adapt).

- [ ] **Step 4: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
refactor(core-commands): consolidate temp file pattern

Reduce from ~60 to ~25 lines. One complete example.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 7: Add Common Mistakes Section

**Files:**
- Modify: `skills/core-commands/SKILL.md` (after temp file section)

- [ ] **Step 1: Add Common Mistakes section**

```markdown
## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| `cat <<'EOF'` for multi-line | Escaping issues, unreliable | Use mktemp + Write tool |
| `echo` for multi-line | Backslash/special char failures | Use mktemp + Write tool |
| `git status` | Wrong VCS | `jj status` |
| `jj log` | Incoherent output | `jj status` or `jj op log` |
| `jj new <args>` | Only plain `jj new` allowed | Run `jj new` without arguments |
| Skipping temp file cleanup | Pollutes /tmp | Always `rm` when done |
```

**Why:** Standard SKILL.md section. Provides quick fixes for common violations.

- [ ] **Step 2: Commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
docs(core-commands): add Common Mistakes section

Quick fixes for common violations.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Task 8: Final Verification

**Files:**
- `skills/core-commands/SKILL.md`

- [ ] **Step 1: Count total lines**

Run: `wc -l skills/core-commands/SKILL.md`

Expected: <100 lines (target for frequently-loaded skill)

- [ ] **Step 2: Verify all required sections present**

Check for:
- [x] Overview
- [x] When to Use
- [x] Prohibited Commands (table)
- [x] Command Alternatives (table)
- [x] Temp File Pattern
- [x] Common Mistakes

- [ ] **Step 3: Verify description is CSO-compliant**

Description should:
- Start with "Use when..."
- Describe triggering conditions only
- Not summarize workflow
- Be third-person

- [ ] **Step 4: Test skill loads correctly**

The skill should be discoverable when:
- Running shell commands
- Working with jj/git
- Creating temp files

- [ ] **Step 5: Final commit**

Use temp file pattern:
```bash
mktemp
# Returns: /tmp/tmp.XXXXXX
```
Use Write tool to save commit message:
```
chore(core-commands): final verification

Confirm <100 lines, all sections present, CSO-compliant.
```
Then:
```bash
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
jj new
```

<!-- --- -->

## Verification

**How to test:**

1. Verify line count: `wc -l skills/core-commands/SKILL.md`
2. Verify structure: scan for all required sections
3. Verify CSO: check description follows "Use when..." pattern
4. Test discovery: skill should trigger on shell commands, jj/git operations, temp file creation

**Expected outcome:**

- <100 lines (down from ~145)
- All required sections present
- CSO-compliant description
- No redundant content
- One complete temp file example
