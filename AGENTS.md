# AGENTS.md

## What this repo is

A collection of agent skills (Markdown prompt documents, no executable source code). Each skill lives in `skills/<name>/` as a `SKILL.md` with YAML frontmatter (`name`, `description`) plus optional supporting prompt files (e.g. `skills/supervised-plan-execution/*-prompt.md`). Most skills are forks of upstream work (obra/superpowers, JuliusBrussee/caveman, szkocot/andrej-karpathy-skills, mattpocock/skills) — the README's Acknowledgments table maps each skill to its origin.

There is no build, test, or lint tooling here. The artifacts are prose; "verification" means reviewing the Markdown content.

## Critical: jj, not git

This repo uses `jj` exclusively — git commands are prohibited. `skills/core-commands/SKILL.md` is the authoritative rulebook and applies to any work in this repo. Non-obvious highlights:

- Prohibition is **prefix-based** against jj/git patterns; e.g. `jj log*`, `jj abandon*`, `jj squash*`, `jj split*`, `jj edit*`, `jj rebase*`, and `jj new` with arguments are all forbidden. Use `jj status` / `jj op log` instead of `jj log`, and plain `jj new` only.
- Committing is `jj describe`, and it uses the **temp file pattern**: `mktemp` → Read the empty temp file (required, skipping this is the main failure mode) → Write the message to it → `jj describe --stdin < "/tmp/tmp.XXXXXX"` → `rm` it. `jj describe -m`, heredocs, `echo`, and `printf > file` are all banned for multi-line content.
- Bash calls run in independent shells — never reference `$TMPFILE`, always the literal mktemp path.
- `jj describe` is only run when the user requests a commit; never push without confirmation.

## VCS context

The working copy is colocated with a shared jj repo (`.jj/repo` is a symlink into `~/Downloads/dev/karen/skilldrift/.jj`). Commits happen via `jj describe` then `jj new` for the next change. Use the `caveman-commit` skill's conventions for messages: Conventional Commits (`type(scope): subject`, imperative, ≤50 chars, why-over-what).

## docs/ conventions

- `docs/plans/` — implementation plan documents. Large plans are split into `docs/plans/<DATETIME>_<TITLE>/` directories with `00-index.md` plus `NN-<subtitle>.md` sub-plans (see `skills/writing-plans/SKILL.md` complexity gate: fires at >600 estimated lines or >15 tasks). Plan files use `- [ ]` checkbox task syntax and reference the `core-commands` commit sequence.
- `docs/issues/` — one Markdown file per issue with problem/decision tables (settled decisions Q#-numbered, resolved residuals, RED/GREEN verification notes from subagent pressure scenarios). Also holds a SQLite `STATE.db`.

## How skills are structured and changed

- Frontmatter `description` must state **triggering conditions** ("Use when..."), not a content summary or standing orders.
- Standard body sections: Overview, When to Use (incl. "When NOT to use"), then rules in tables where possible for token efficiency.
- Forked skills that diverge from upstream add a header note: `> Diverged from obra/superpowers: <what + pointer to docs/issues/N.md>`. Always cite upstream repos, never local paths.
- Skill edits follow the writing-skills discipline used in `docs/issues/26.md`: establish a failing baseline (RED) via a subagent pressure scenario before editing, then verify the edit turns it GREEN, and harvest rationalizations into a rationalization table. Write conditionals as observable predicates ("if >X", never "consider").

## Working style expected here

The skills in this repo double as the owner's working conventions; applying them (core-commands always, caveman-commit for commits, writing-plans/supervised-plan-execution for plan work, grill-me for spec stress-testing, karpathy-guidelines for code discipline) is expected rather than optional. Plans are executed task-by-task with two-stage self-review (spec compliance, then code quality) per task.
