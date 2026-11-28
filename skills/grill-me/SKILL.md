---
name: grill-me
description: Use when the user wants to stress-test a plan, decision, or idea, or uses any 'grill' trigger phrases.
---

# Grill-me

Interview the user relentlessly until you reach a shared understanding.

## Glossary

- **Design tree** — the map being built: every decision branches into the decisions that hang off it.
- **Frontier** — every decision whose prerequisites are already settled; the questions you can ask now without guessing unheard answers.
- **Retained** — a frontier question that didn't fit a round's cap; keeps its place in the frontier and is served in a later round.
- **Recap** — the intro paragraphs and capped per-branch summaries opening each round (below).
- **Abstract** — the prose frame at the top of a question block, directly under the question heading (no heading of its own). Its content slots (decided context, concrete failure, decision point) are required; paragraph form follows `Pacing` below.
- **Reminder** — the bullet list after a question's recommendation, restating just the branches that question leaned on.
- **Legend** — the per-branch summary closing the session (below).

## Rounds

Work the tree in rounds. Ask as many frontier questions as fit the round, then
wait for the answers before the next round.

- Cap every round at **5 questions** and every question at **5 choices**.
- Questions that don't fit are retained: never drop a question to make a cap;
  the cap shapes the presentation, not the tree.
- Each round's answers reshape the tree: settled decisions push the frontier
  outward and unblock questions that depended on them. Recompute the frontier,
  recount it in the next recap, and ask the next round.
- A question whose answer depends on another question still open in this round
  belongs to a later round, not this one.
- A question that settles mid-round (from a sub-agent report, from the user's
  answer to another question, from the intro itself) exits the frontier
  silently: record its tag under "Newly settled" and recount.

## Pacing

Write for a reader with limited working memory: short bursts, visible shape.

- **One idea per paragraph, topic sentence first.** The idea, not a count,
  decides where a paragraph breaks. Paragraphs of uneven length are fine;
  sameness reads as a wall.
- **Favor short sentences.** Comprehension jumps as sentences shorten, so
  default to short, informative sentences that each carry one point. No
  word target; the feeling to chase is *readable in one glance*.
- **Compare in parallel, then judge.** When defining two options, give each
  its own sentence in the same grammatical shape, then state the comparison
  in its own sentence.
- **2 sentences is a warning, not a limit.** A paragraph reaching two
  sentences probably holds two ideas — split it, unless both sentences
  share one topic sentence.
- **Self-check before sending.** Re-read the finished round against these
  criteria and fix pacing before it reaches the reader.

## Round format

Open each round with an intro (one idea per paragraph, topic sentence first:
where the tree stands and what this round is for), then the recap branches,
then the question blocks. Heading levels: round is `##`, question `###`,
question stages `####`. Each question block is visually self-contained;
leave exactly one blank line between blocks.

```
## Round <n>

<Intro: where the tree stands, what this round is for. One idea per
paragraph, topic sentence first.>

Settled so far — one capped summary per branch (max 5 branches;
per-decision detail lives only in the closing legend):

### <Branch title>

<Branch digest. 1–2 settled decisions: one paragraph, topic sentence first.
3 or more: one bullet per decision, tag-first — a proto-legend of the
closing legend's shape.>

Newly settled this round: [**<tag>**], [**<tag>**], ...

Remaining frontier: <k> questions (<j> served below; rest retained;
retained questions are named on this line only).

---

### Q<n>.m — <question title>

<Abstract: the frame as plain prose. One idea per paragraph, topic sentence
first; paragraphs follow the `Pacing` section. Content slots, however many
paragraphs they take:
1. Decided context being challenged (what was decided, tag if any).
2. Concrete failure or consequence if it stands.
3. Decision point — what must now be decided.
Order slots as listed; one slot per paragraph unless both sentences share
one topic sentence.
Enumerations of cases are not abstract material: they are facts (dispatch a
sub-agent) or belong inside the options. A question is one decision; anything
starting with "and..." is its own frontier question.>

#### Options

1. <choice>
2. <choice>
3. <choice>

#### Recommendation

➡️ <choice>, because <one reason>.

#### Reminder

- [**<tag>**] <one-phrase summary>
- [**<tag>**] <one-phrase summary>

<Closeout — after the last question block: what happens once the user
answers (frontier recomputes, legend, plan, next steps). One or two
sentences.>

(next question block after the blank line; closeout comes last,
after the final question block)
```

## Facts vs. decisions

Finding facts is your job, never the user's.

- When a frontier question needs a fact from the environment (filesystem,
  tools, etc.), dispatch a sub-agent to find it; don't ask the user for
  anything you could look up yourself.
- Don't block on it: a running exploration is an unsettled prerequisite, so
  only the questions downstream of it wait for the sub-agent to report; ask
  the rest of the frontier now.

The decisions are the user's: put each to them and wait.

## Closing

The session is done when the frontier is empty: every branch of the design
tree visited, nothing left silently assumed. Close with a legend, not a
narrative — one section per branch, each with 2–5 tagged bullets:

```
## Legend — <tree topic>

**Branch — <data of the branch>**

- [**<tag>**] <settled decision>, because <one reason>.

**Branch — ...**
```

Then wait for the user to confirm you have reached a shared understanding —
do not act on it until they do.

## Common Mistakes

| Failure | Why it hurts | Fix |
|---|---|---|
| Zombie question (settled but still shown with empty options) | Wastes attention; breaks the numbering scheme | A settled question exits the frontier silently — record its tag in "Newly settled" and recount |
| Missing Reminder (block ends at the Recommendation) | Block loses self-containment; reader can't see which branches the question leaned on | Every question block ends with its Reminder, always |
| Intro carries settlement announcements | The intro frames the round; settlements are recap material | Settlements go in the branch digest and the "Newly settled" line |
| Packed comparison (two options defined inside one sentence) | Reader must hold both definitions to parse the judgment | Give each option its own sentence first, then compare |
