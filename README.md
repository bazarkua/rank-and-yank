# Rank and Yank

**Your best model runs the company. Everything else competes to keep its desk.**

A Claude Code skill that turns your most expensive model into an orchestrator and pushes
execution down to cheaper subagents. The subagents are not anonymous. They hold named personas
with recorded scores, they can see their own standing, and when the roster exceeds eight the
bottom-ranked one is retired.

Named after the forced-ranking practice, because that is precisely what it does.

---

## The problem

An expensive model's marginal value is judgment: framing a problem, choosing an approach,
catching what is wrong, noticing what is missing. That value is destroyed when you spend it
grepping a repo or typing out code whose every decision is already made.

The usual fix is "delegate more", which works once and then stops improving. You write a
throwaway prompt, an agent returns something plausible, you skim it, and nothing about the next
task is any easier.

## What this does instead

Four things, and the fourth is the one that compounds.

1. **The orchestrator only thinks.** Planning, decomposition, brief writing, review, synthesis.
   Never scanning, typing, or running.
2. **Execution goes to a tier that fits.** Sonnet for real work, Haiku for genuinely mechanical
   work, an expensive subagent only as a documented last resort.
3. **Every run is scored** on correctness, completeness, spec adherence, and report quality, and
   the score is written to disk under a named persona.
4. **Both sides of the loop improve.** Agents that perform get minted into reusable personas and
   promoted to real subagent types. Briefs that fail get their defect written into a checklist
   the orchestrator reads before writing the next one.

The result over time is a small roster of proven specialists per codebase, and an orchestrator
that stops repeating its own prompt mistakes.

---

## Install

**As a plugin:**

```
/plugin marketplace add bazarkua/rank-and-yank
/plugin install rank-and-yank@rank-and-yank
```

**As a plain skill:**

```bash
git clone https://github.com/bazarkua/rank-and-yank ~/.claude/skills/rank-and-yank
```

Either way the skill activates itself on substantive tasks. It is written as a standing
operating mode, not a command you invoke per task, though `/rank-and-yank` works if you want to
force it.

Nothing is written until you actually delegate something. On first delegation it creates
`~/.claude/rank-and-yank/` and a folder for the current project.

---

## How it works

### The roster is per project, and capped at eight

The specialists a codebase needs are specific to that codebase, so each project gets its own
company. The project is resolved from the git repository root name. Persona numbers restart at
`01` per project.

A persona is minted when a run scores 8.5 or higher on a task shape that will recur and that no
existing persona owns. Its file carries the parts of the brief that actually worked, the output
shape that proved usable, and every failure mode it has been caught in.

At three runs averaging 8.0 or better, a persona is promoted to `~/.claude/agents/` as a real
`subagent_type`, callable from any session.

When minting would make nine, the lowest-ranked persona past its protection window is retired.
The cut is relative, so a persona averaging 8.5 can still be cut for ranking last in a strong
field. New personas are protected for their first three runs so they are not cut before they
have run twice.

### Agents can see their standing, and they are told what it means

Every brief carries a block stating the persona's average, its rank, the size of the cap, and
who has already been retired. This is not decoration. The scores are real, they are written to
disk, and retirement actually removes the persona.

### Agents can push back, and it pays

The most valuable thing a subagent can do is tell you your instruction is wrong. So there is a
formal channel for it, with a real reward:

| Challenge ruling | Adjustment |
|---|---|
| Verified, prevented real damage | **+1.0** |
| Verified, improved the outcome | **+0.5** |
| Cited real evidence, drew the wrong conclusion | **0** |
| No evidence: speculation, hedging, out of scope, or a bid for points | **-0.5** |
| Nothing challenged | **0** |

Three constraints keep this from turning into noise:

- **Evidence bar.** A challenge is admissible only if it points at a file and line, actual file
  contents, real command output, or a line of the brief contradicting another. "This might be a
  problem" is a guess, and guesses are penalized. Confidence is not evidence; a model can feel
  certain and be wrong.
- **Scope fence.** The challenge must come out of the agent's own assignment. It does not go
  hunting through the wider codebase for something to object to, because that spends the exact
  tokens this skill exists to save.
- **Honest wrong calls are free.** Cite real evidence, reach the wrong conclusion, score zero
  rather than a penalty. Penalizing good-faith misses teaches agents to stay quiet precisely
  when you most need to hear from them.

When a challenge is upheld, the orchestrator rewrites the brief and resumes **the same agent**
via `SendMessage`, so the context it already paid to build is not thrown away. If sibling agents
are running under the same bad brief, the correction is pushed to all of them at once.

### Agents can amend their own persona

Each agent reads its persona before starting and decides whether it actually fits the task in
front of it. If part of it does not, the agent declares the amendment, works under its own
amended version, and the orchestrator rules at review.

| Amendment ruling | Adjustment |
|---|---|
| Accepted, measurably improves the persona | **+0.5** |
| Reasonable, but declined | **0** |
| Self-serving: loosens its own constraints or widens its own scope | **-0.5** |

Accepted amendments are written into the persona file crediting the agent. The persona was
written from past runs; the agent holding it is the first to see where it stopped fitting.

### Bad briefs are charged to the orchestrator, not the roster

Before writing any score, the orchestrator asks whether a competent agent could have gotten this
right from the brief alone. If not, the run is logged as a prompt defect, the agent's average is
untouched, and the missing element goes into a checklist that gets read before the next brief.

Without this rule the ranking measures the orchestrator's prompting rather than the agents' work,
and elimination starts cutting the wrong personas.

One exception carries most of the weight: if the agent **caught** the defect instead of being
tripped by it, it gets a real score on the corrected work plus the challenge bonus. Being
tripped is neutral. Catching it is the behavior being selected for.

---

## Layout

The skill is read-only. All state lives outside it, so a plugin update cannot destroy your
history.

```
~/.claude/rank-and-yank/
  BRIEF-CHECKLIST.md              your own recurring brief defects
  projects/<project>/
    STANDINGS.md                  the roster and its ranking
    RUNS.md                       append-only run log
    BRIEF-CHECKLIST.md            defects specific to this codebase
    personas/sonnet-01-*.md       up to 8
    personas/retired/
```

A run log line:

```
2026-01-04 | sonnet-01-scout | sonnet | 9.3 | C9 X8 S9 R9 | +1.0 | refactor the loader | CHALLENGE upheld, damage: brief targeted the deprecated copy under legacy/, real callers use core/. Corrected and resumed via SendMessage.
```

That is the entire per-run bookkeeping cost: one appended line, plus one row updated in
`STANDINGS.md`. Persona files are rewritten only on mint, promote, retire, or an accepted
amendment.

---

## What this is not

Being straight about the limits, because they matter before you adopt it.

- **It is prompt discipline, not enforced code.** Nothing validates the scores or stops the
  orchestrator from skipping the ledger. If it skips scoring, this degrades into ordinary
  delegation and stops compounding.
- **Scores are model judgments.** They are anchored to four named dimensions and to cited
  evidence, which makes them consistent enough to rank on, but they are not measurements.
- **Review costs orchestrator tokens.** The savings come from not executing, not from skipping
  review. Reviewing is the job.
- **It is worth nothing on trivial turns.** Spawning an agent to read one known file costs more
  than doing it. The skill says so explicitly.
- **The roster needs real runs to become useful.** Expect the first several tasks to go out as
  unnamed candidates while slots are still open.

## Tiers

The skill says Opus, Sonnet, and Haiku because those are what Claude Code's `Agent` tool takes.
Read them as roles: an expensive orchestrator, a mid-tier workhorse, and a cheap mechanical tier.
Substitute your own.

---

## License

MIT. See [LICENSE](LICENSE).

By [Adilbek Bazarkulov](https://github.com/bazarkua).
