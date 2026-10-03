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

**NEVER use heredocs (`cat <<'EOF'`), `echo`, or `printf` to write file content.** Commit messages, issue bodies, PR descriptions: all file content goes through the Write tool, no exceptions.

Each Bash tool invocation runs in an independent shell. Variables don't persist between calls. Reference the literal path returned by `mktemp` (e.g. `/tmp/tmp.AbCdEf`) — never `$TMPFILE`.

1. Run `mktemp` → note the returned path
2. Read the file with the Write tool's read-before-write requirement. **You MUST call Read even though the file is empty.** Skipping this step is what causes Write to fail, and a failed Write is NOT permission to fall back to shell redirection.
3. Write tool → write the content to that path
4. In a new Bash call, pass the literal path: `cmd --option "/tmp/tmp.XXXXXX"`
5. Clean up: `rm "/tmp/tmp.XXXXXX"`

### If the Write tool fails

Do NOT "work around" it with `printf`, `echo`, heredocs, or inline file content in the Bash call. That defeats the purpose of the pattern (escaping, quoting, and content integrity failures). Re-run Read on the file, then Write again. If Write still fails, stop and ask the user.

### Committing Changes (jj describe)

All commit descriptions go through this exact sequence. `jj describe -m` is prohibited.

1. `mktemp`
2. Read the temp file (read-before-write, even though empty)
3. Write the commit message to the temp file
4. `jj describe --stdin < "/tmp/tmp.XXXXXX" && rm "/tmp/tmp.XXXXXX"`

### Example

```bash
mktemp
# Tool returns: /tmp/tmp.AbCdEf
# Call Read on /tmp/tmp.AbCdEf (required, even though empty)
# Call Write with the issue description at /tmp/tmp.AbCdEf
gh issue new -t 'Issue title' -F "/tmp/tmp.AbCdEf"
rm "/tmp/tmp.AbCdEf"
```

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| `cat <<'EOF'`, `printf`, or `echo` for file content | Escaping issues, unreliable, bypasses Write tool | Use mktemp + Read + Write |
| `jj describe -m 'message'` | Bypasses temp file pattern | mktemp + Read + Write, then `jj describe --stdin < <path>` |
| `git status` | Wrong VCS | `jj status` |
| `jj log` | Incoherent output | `jj status` or `jj op log` |
| `jj new <args>` | Only plain `jj new` allowed | Run `jj new` without arguments |
| Skipping temp file cleanup | Pollutes /tmp | Always `rm` when done |
| Skipping Read because the file is empty | Write tool then fails, tempting shell fallback | Always Read first, even on an empty mktemp file |
| Switching to `printf > file` after a Write failure | Reintroduces the exact problem the pattern prevents | Re-Read, re-Write; stop and ask if it keeps failing |
