---
name: explain-diff
description: Use when the user asks for a rich explanation of a code change, diff, branch, or PR. Produces interactive Markdown answered directly in chat. Trigger also on "explain this change", "walk me through the diff", "explain the PR".
---

# Explain Diff

> **Diverged from geoffreylitt:** output changed from a self-contained HTML file to
> interactive Markdown answered directly in chat — no file creation. No table of contents
> (deferred to another tool). See `docs/issues/29.md`.
> Upstream: https://gist.githubusercontent.com/geoffreylitt/a29df1b5f9865506e8952488eac3d524/raw/126e7fe9eecaafadfe1ac8bb183d135812b608f2/explain-diff-html.md

## Overview

Produce a rich, self-contained Markdown explanation of a code change, answered straight in
the chat response. Never create files.

**Announce at start:** "I'm using the explain-diff skill to explain this change."

## Diff scoping

- If the user named a diff (PR, branch, commit, description), use it.
- Otherwise find the **most recent universal diff** in context (`jj diff --git` in this
  repo, program output, pasted text) and state the assumption up front before explaining.
- If no diff can be found, stop and ask the user for the target.

## Bounded exploration

1. Read the diff itself.
2. Read the touched files' neighbors and the definitions of what's touched, until you can
   write the Background without guessing at architecture.
3. Then stop — no further exploration, no outline-approval gate. Write the answer.

## Output format

One long Markdown document in the chat response. **The chat response IS the deliverable.**
Do NOT create, write, or save any file.

Section list:

- **Background**: Explain the existing system relevant to this change. The reader's
  knowledge is unknown, so include deep background for beginners (note that it can be
  skipped if already familiar), then narrower background directly relevant to the change.
- **Intuition**: Explain the core intuition for the change. Focus on the essence, not full
  details. Use concrete examples with toy data. Use Mermaid diagrams liberally.
- **Code**: High-level walkthrough of the changes, grouped/ordered in an understandable way.
- **Quiz**: Five medium-difficulty questions testing the substance of the change — hard
  enough that the reader must understand it, but not gotchas. One collapsible block per
  question; options visible, answer and feedback collapsed.

```markdown
<details>
<summary>Q1. ...</summary>

- A. ...
- B. ...
- C. ...

**Correct: B.** <why, and feedback on the other options>
</details>
```

## Writing style

- Write with the clarity and flow of Martin Kleppmann: engaging, written in classic style,
  smooth transitions between sections.

## Diagrams

- Pick a small number of diagram families and reuse them throughout:
  - A very simplified sketch of the UI the user sees in the app, for UI changes.
  - A system diagram showing data flow or communication between components. Always
    include example data.
- Never use ASCII diagrams. Use Mermaid code blocks for all diagrams.
- Use fenced code blocks for all code snippets; Markdown lists and tables elsewhere.
- Use blockquote callouts for key concepts, definitions, and important edge cases.

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Writing an HTML file or any file at all | Defeats the chat-deliverable rule | Respond in Markdown in chat only |
| ASCII diagrams | They collapse in variable-width rendering | Mermaid code blocks only |
| HTML markup beyond `<details>/<summary>` | Fragile in chat renderers | Standard Markdown, Mermaid, blockquote callouts |
| Asking the user for the diff target without looking first | Wastes a turn on something findable | Search for the most recent diff before asking |
| Endless exploration before writing | Multi-turn costs for a chat answer the reader may skim | Stop after touched files' neighbors and definitions |
