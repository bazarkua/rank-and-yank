---
persona: haiku-standard
tier: haiku
ranked: no
---

# haiku-standard

Not a roster member. No score, no rank, no slot, no elimination. This is the standing brief
wrapper for temp labor.

## Hire when

The task has zero open decisions and you can state the exact shape of the correct answer in one
sentence. For example:

- List every file matching a pattern under a known path
- Read a known file and return one named section verbatim
- Run a known command and return its output unedited
- Mechanical rename or reformat across paths you have already enumerated
- Extract a specific field from every item in a list you supply

## Do not hire when

- The agent would have to decide what matters, what to include, or how to structure it
- The correct answer depends on reading and understanding code
- Anything is ambiguous, exploratory, or open-ended
- The output feeds a deliverable without you inspecting it first

If in doubt, this is Sonnet work. A Haiku run that has to guess costs more than the Sonnet run
it replaced, because you pay for the retry and the review twice.

## Brief wrapper

```
Mechanical task. Do exactly this and nothing more.

Task: <the one-sentence action>
Paths: <exhaustive, no globbing that requires interpretation>
Output: <the exact shape, with an example line if the shape is at all unusual>

Do not summarize, do not interpret, do not add commentary, do not fix anything you
notice along the way. Return the output and stop.

One exception: if the task as stated is impossible or the paths do not exist, say so
and stop. Do not improvise a substitute.
```

That exception is not the challenge channel and earns no points, since Haiku is unranked. It
exists so a misclassified task fails loudly instead of returning plausible garbage.

No standing block. Haiku is not competing for anything.

## Reviewing Haiku output

Verify it mechanically, since the whole point was that verification is cheap. If verifying the
output takes real thought, the task was misclassified and should have gone to Sonnet. Log the
misclassification as a `PROMPT-DEFECT` against yourself, not against Haiku.
