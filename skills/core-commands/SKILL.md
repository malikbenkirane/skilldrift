---
name: core-commands
description: Use when running any shell command, especially jj/git operations, or creating multi-line content via temp files
---

# Core Commands

## Overview

This repo uses `jj` exclusively. Git commands are prohibited. Multi-line content must use mktemp + Write tool pattern.

## When to Use

- Before running any shell command
- Working with jj/git operations
- Creating temp files for multi-line content
- **When NOT to use:** Simple single-line commands with no temp files

## Prohibited Commands

NEVER use git commands. This repo uses `jj` exclusively.

**Prohibition is prefix-based**: Commands matching the start of a prohibited pattern are forbidden (e.g., `jj log -r 'all()'` matches `jj log*`).

### Prohibited jj Commands (Prefix Patterns)
- `jj abandon*` - Do not abandon changes
- `jj describe` before changes are ready and reviewed
- `jj edit*` - Moves working copy unexpectedly, use `jj new` instead
- `jj git push` without explicit user confirmation
- `jj log*` - Produces incoherent output
- `jj new` with any arguments - Only plain `jj new` allowed
- `jj op*` - Operations commands not allowed
- `jj prev*` / `jj next*` - Navigation requires user confirmation
- `jj rebase*` - Can break change dependencies
- `jj squash*` - Violates atomic commit history
- `jj split*` - Breaks atomicity
- Interactive commands with `-i` flag

### Prohibited Actions
- Update git config
- Run destructive commands without explicit request
- Skip hooks without explicit request
- Commit without explicit request
- Push without explicit confirmation
- Commit secrets or credentials
- Expose or log secrets

## Command Alternatives

| Prohibited Pattern | Why | Use Instead |
|-------------------|-----|-------------|
| `jj log*` | Incoherent output | `jj status` or `jj op log` |
| `jj op*` | Not allowed | `jj status` or ask user |
| `jj abandon*` | Destructive | Create new change with `jj new` |
| `jj prev*` / `jj next*` | Needs confirmation | Ask user first |
| `git status` | Wrong VCS | `jj status` |
| `git branch*` | Wrong VCS | `jj bookmark list` |
| `git checkout*` | Wrong VCS | `jj new` |
| `git commit*` | Wrong VCS | `jj describe` |
| `git push*` | Wrong VCS | `jj git push` (with confirmation) |
| `git pull*` | Wrong VCS | `jj git fetch` |
| `git merge*` | Wrong VCS | `jj merge` or rebase approach |
| `git rebase*` | Wrong VCS | Use jj's automatic rebasing |

## GitHub (`gh`) Commands

All multi-line content (issue bodies, PR descriptions, comments) uses the temp file pattern below.

| Action | Command | Notes |
|--------|---------|-------|
| Create issue | `gh issue new -t '<title>' -F "$TMPFILE"` | Use temp file pattern for body |
| Create PR (draft) | `gh pr create --draft --base <base> --head <bookmark> --title "<title>" -F "$TMPFILE"` | Use temp file pattern |
| Comment on PR | `gh pr comment <number> --body-file "$TMPFILE"` | Use temp file pattern |
| View issue | `gh issue view <number>` | - |
| View PR | `gh pr view <number>` | - |
| View PR diff | `gh pr diff <number>` | - |
| List issues | `gh issue list` | - |
| List PRs | `gh pr list` | - |

## Temp File Pattern for Multi-Line Content

**NEVER use heredocs (`cat <<'EOF'`) or `echo` for multi-line content.**

Each Bash tool invocation runs in an independent shell. Variables don't persist. Use this pattern:

1. `mktemp` → returns path like `/tmp/tmp.XXXXXX`
2. Read tool → check existing content
3. Write tool → write content to that path
4. Pass path to command: `cmd --option "/tmp/tmp.XXXXXX"`
5. Clean up: `rm "/tmp/tmp.XXXXXX"`

### Example: Creating GitHub Issue

```bash
mktemp
# Tool returns: /tmp/tmp.AbCdEf

# Use Read tool to check existing content (if any)
# Use Write tool to write issue description to /tmp/tmp.AbCdEf

gh issue new -t 'Issue title' -F "/tmp/tmp.AbCdEf"
rm "/tmp/tmp.AbCdEf"
```
