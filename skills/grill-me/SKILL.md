---
name: grill-me
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
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

## Pacing

Write for a reader with limited working memory: short bursts, visible shape.

- **One idea per paragraph, topic sentence first.** The idea, not a count,
  decides where a paragraph breaks. Paragraphs of uneven length are fine;
  sameness reads as a wall.
- **Favor short sentences.** Comprehension jumps as sentences shorten, so
  default to short, informative sentences that each carry one point. No
  word target; the feeling to chase is *readable in one glance*.
- **2 sentences is a warning, not a limit.** A paragraph reaching two
  sentences probably holds two ideas — split it, unless the idea genuinely
  needs them together.
- **Self-check before sending.** Re-read the finished round against these
  criteria and fix pacing before it reaches the reader.

## Round format

Open each round with an intro (one idea per paragraph, topic sentence first:
where the tree stands and what this round is for), then the recap branches,
then the question blocks. Heading
levels: round is `##`, question `###`, question stages `####`. Each question block
is visually self-contained; leave exactly one blank line between blocks.

```
## Round <n>

<Intro: where the tree stands, what this round is for. One idea per
paragraph, topic sentence first.>

Settled so far — one capped summary per branch (max 5 branches;
per-decision detail lives only in the closing legend):

### <Branch title>

<What this branch settled — joint summary. One idea per paragraph, topic
sentence first.>

Newly settled this round: [**<tag>**], [**<tag>**], ...

Remaining frontier: <k> questions (<j> served below; rest retained).

---

### Q<n>.m — <question title>

<Abstract: the frame as plain prose. One idea per paragraph, topic sentence
first; paragraphs follow the `Pacing` section. Content slots, however many
paragraphs they take:
1. Decided context being challenged (what was decided, tag if any).
2. Concrete failure or consequence if it stands.
3. Decision point — what must now be decided.
Order slots as listed; one slot per paragraph unless two slot sentences form
one idea.
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

(next question block after the blank line)
````

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

````
## Legend — <tree topic>

**Branch — <data of the branch>**

- [**<tag>**] <settled decision>, because <one reason>.

**Branch — ...**
````

Then wait for the user to confirm you have reached a shared understanding —
do not act on it until they do.
