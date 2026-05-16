---
name: core-commands
description: Core command guidelines - prohibited commands and temp file patterns. ALWAYS load this skill at session start.
---

# Core Commands

This skill defines prohibited commands and the temp file pattern for multi-line content.

## Before Running Any Command

Check if the command starts with a prohibited prefix:
1. Does it start with `git`? → Find jj equivalent in table below
2. Does it start with `jj log`? → Use `jj status` instead
3. Does it start with `jj op`? → Not allowed, ask user
4. Does it start with `jj abandon`? → Use `jj new` instead
5. Does it start with `jj edit`? → Not allowed, use `jj new` instead
6. Does it start with `jj prev` or `jj next`? → Ask user first
7. Does it have `-i` flag? → Not allowed (interactive)
8. Does `jj new` have any arguments? → Not allowed, use plain `jj new` only

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
These patterns have escaping issues and produce unreliable output.

**ALWAYS use the mktemp + Write tool pattern:**

Each Bash tool invocation runs in an independent shell environment. Variables set in one command do NOT persist to subsequent commands. Therefore:

1. **Get temp file path** - Run plain `mktemp` without variable assignment:
   ```bash
   mktemp
   ```
   The tool returns a path like `/tmp/tmp.XXXXXX`.

2. **Read existing file** - Use the Read tool with the path from step 1 to check if it already contains content.

3. **Write content** - Use the Write tool with the path from step 1 as a literal string to write/overwrite the content.

4. **Pass file to command** - Use the same path as a literal string:
   ```bash
   <command> --option "/tmp/tmp.XXXXXX"
   ```

5. **Clean up** - Remove the file when done:
   ```bash
   rm "/tmp/tmp.XXXXXX"
   ```

**Key insight:** The temp file path must be captured from the tool output and used as a literal string in all subsequent commands. Do NOT use shell variables. **Always read existing temp file content before overwriting.**

### Examples

**Creating GitHub issue:**
```bash
mktemp
# Tool returns: /tmp/tmp.XXXXXX
# Use Read tool to check existing content, then Write tool to write issue description
gh issue new -t 'Issue title' -F "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
```

**Describing commit with complex message:**
```bash
mktemp
# Tool returns: /tmp/tmp.XXXXXX
# Use Read tool to check existing content, then Write tool to write commit message
jj describe --stdin < "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
```

**Creating PR:**
```bash
mktemp
# Tool returns: /tmp/tmp.XXXXXX
# Use Read tool to check existing content, then Write tool to write PR description
gh pr create --draft --base main --head username/issue-123 --title "PR title" -F "/tmp/tmp.XXXXXX"
rm "/tmp/tmp.XXXXXX"
```

## Why This Matters

- **Heredocs fail** when content contains special characters, variables, or nested quotes
- **echo fails** with backslashes and special characters
- **Write tool** handles all escaping correctly and consistently
