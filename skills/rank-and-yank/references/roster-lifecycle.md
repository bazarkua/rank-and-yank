# Roster lifecycle

Read this file only when minting, promoting, retiring, or revising a persona. Ordinary
delegation does not need it.

**Everything here is scoped to one project.** `<project>` throughout means the resolved project
folder under `~/.claude/rank-and-yank/projects/`. The cap is 8 Sonnet slots **per project**,
and every path below sits inside `projects/<project>/`. Haiku is unranked and never gets a
persona. Opus subagents are never personas either, since they are a last resort and not staff.

---

## Mint a persona

**Trigger, all three required:**

1. A run scored **8.5 or higher**
2. The task shape will recur (not a one-off)
3. No current persona in **this project** owns that mission. Missions must not overlap. If an
   existing persona is close, revise that persona instead of minting a near-duplicate.

**Judgment overrides the score bar.** A verified damage-preventing challenge is mint-worthy
evidence on its own. If a `candidate-` run caught a real error in your brief, that judgment
earns a slot even when its raw execution score sits below 8.5. Execution quality is purchasable
at the Sonnet tier. An agent that tells you when you are wrong is the scarce thing, and the
roster should be built to select for it.

**Steps:**

1. Copy `templates/PERSONA.md` to `projects/<project>/personas/sonnet-NN-<slug>.md`. Numbers
   are sequential **within the project** and never reused there, including numbers freed by
   retirement. If this project retired `sonnet-04`, its next mint is `sonnet-07` even though
   only 6 personas exist. Numbering restarts at `01` in a different project.
2. Fill every section. The **Brief skeleton** section is the important one: capture the
   reusable parts of the brief that actually produced the 8.5, not a generic job description.
   The skeleton is what makes the persona worth having.
3. Add a row to `projects/<project>/STANDINGS.md` with the founding run as run 1.
4. Append a `MINT` line to `projects/<project>/RUNS.md`.
5. If this project's roster now stands at 9, run the retirement procedure below.

**Copying from another project.** A persona whose mission genuinely matches may be copied in as
a founding template. It takes a slot here, its score history starts empty, and its version log
records where it came from. Do not copy speculatively: an unproven persona occupying a slot is
worse than an open slot, because the slot is what forces the roster to keep improving.

---

## Promote to a real subagent type

**Trigger:** 3 or more runs at 8.0 or higher average, and still on the roster.

Lower the bar to 7.5 for a persona with two or more verified challenges on its record.
Promotion hands a persona to sessions where you are not watching it, so demonstrated
willingness to flag a bad instruction matters more there than a few tenths of execution score.

Promotion writes the persona to `~/.claude/agents/<project>-<persona-name>.md`, which makes it
a real `subagent_type` callable from any session or skill, not just this one.

`~/.claude/agents/` is one flat namespace shared across every project, so the project prefix is
mandatory. Without it, two projects that each mint a `sonnet-03` and promote it will silently
overwrite one another.

```markdown
---
name: acme-api-sonnet-03-migration-auditor
description: <one line, when to hire this persona>
model: sonnet
tools: <minimum set the mission actually needs>
---

<the persona's Mission, Hire when, Do not hire when, Brief skeleton,
Required output shape, and Known failure modes sections, inlined>
```

The `name:` field must match the filename exactly, prefix included.

Give the narrowest tool set the mission needs. A read-only scout with `Edit` is a scout that
will eventually edit something.

Then set `promoted: yes` in the persona file frontmatter and mark it in `STANDINGS.md`.

**Hiring a promoted persona:** call `Agent` with `subagent_type: "<project>-<persona-name>"`.
The agent file is static, so the standing block and the amendment notice still go in the prompt
every run, since ranks change between runs.

**Hiring an unpromoted persona:** call `Agent` with `model: "sonnet"` and inline the persona
body plus the run's brief plus the standing block into the prompt.

**Keeping them in sync:** the file under `projects/<project>/personas/` is canon. Any revision
to a promoted persona must be written to both files, or they drift and the promoted copy
silently goes stale.

---

## Retire

**Trigger:** minting a 9th persona **in this project**. The roster is a cap, not a target, and
the cut is relative. A persona averaging 8.5 can still be cut for ranking last in a strong
field.

**Protection:** a persona is exempt from the cut for its first 3 runs. Without this, a newly
minted persona could be cut before it ever ran a second time, and the roster would never turn
over.

Each verified damage-preventing challenge on a persona's record extends its protection by 3
further runs. A persona that has saved you from your own bad brief has earned the benefit of
the doubt during a slow stretch.

**Procedure:**

1. Rank this project's personas by average, descending. Tie-break: fewer runs ranks lower,
   since it is less proven.
2. Cut the lowest-ranked persona that is **past** its protection window.
3. If every persona below the cap line is still protected, defer the cut and record `OVER-CAP`
   in `STANDINGS.md`. Take the cut the moment one loses protection. Do not mint again while
   over cap.

**Steps:**

1. Move `projects/<project>/personas/sonnet-NN-<slug>.md` to
   `projects/<project>/personas/retired/`. Do not delete it. Its recorded failure modes are
   evidence for how future briefs should be written.
2. If promoted, delete `~/.claude/agents/<project>-<persona-name>.md`. It is no longer staff.
3. Move its `STANDINGS.md` row into the `Retired` section with the date and final average.
   That section is visible to every future agent, which is what makes the threat real.
4. Append a `RETIRE` line to `RUNS.md`.

---

## Revise

**Triggers:**

- The same failure mode shows up twice for one persona
- A run exposes a gap in its brief skeleton
- **You accept a `PERSONA-AMENDMENT`.** The agent proposed the change and worked under it, you
  ruled it an improvement, so write it in.

For a failure mode: add it to **Known failure modes**, stating what went wrong and what the
brief must now include to prevent it, then fold that prevention into the **Brief skeleton**.

For an accepted amendment: apply the change the agent actually proposed, not your
reinterpretation of it, and credit the agent by name in the version log. It saw the misfit from
inside the work, which is a vantage point you do not have.

Bump the version log either way. If promoted, mirror the change into `~/.claude/agents/`.

A revision is not a reset. The persona keeps its score history.

---

## What separates a good persona from a job title

A persona is only worth a slot if its file makes the next run better than an ad-hoc brief would
have been. That means the file has to carry knowledge that was expensive to acquire: the output
shape that worked, the constraint that had to be spelled out, the mistake it made the first
time. A file that just says "this agent scans codebases" is a job title, and it earns nothing.
Do not mint those.
