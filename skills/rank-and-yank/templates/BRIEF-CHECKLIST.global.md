# Brief checklist (global)

Read before writing any brief, together with the current project's own
`projects/<project>/BRIEF-CHECKLIST.md`. This file holds the baseline and the lessons that
transfer to **any** project. Project-specific defects live in that project's file.

This is the record of what **your own briefs** get wrong, so it grows every time the attribution
rule fires. It is the orchestrator's half of the self-improvement loop, separate from the
roster, which tracks the agents.

Copy this to `~/.claude/rank-and-yank/BRIEF-CHECKLIST.md` on first use and append to it there.

## Baseline, always check

- [ ] **Mission** stated in one sentence, so the agent knows when it is done
- [ ] **Exact paths.** Never "the config file" or "the relevant module". Absolute or
      repo-relative paths, enumerated.
- [ ] **Output shape** specified concretely enough that compliance is checkable without
      re-deriving the work. If the shape is unusual, include an example line.
- [ ] **Scope fence.** What not to touch, and an explicit "no unrequested extras". Agents that
      improve things you did not ask about create review work.
- [ ] **Every axis named.** An agent that stays inside the fence you drew will not report what
      lies outside it, and its silence will read as a clean verdict.
- [ ] **Prior findings** the agent needs, and what has already been tried and failed. An agent
      rediscovering a dead end is your cost, not theirs.
- [ ] **Claims labeled as claims.** Anything taken from a PR body, commit message, code
      comment, or another agent's summary is unverified until you check it. Either check it or
      say in the brief that it is unverified so the agent tests it rather than building on it.
- [ ] **House style** where the output is code or prose the user reads. Carry over whatever
      conventions the user has stated, and say them explicitly rather than assuming.
- [ ] **Persona attached**, plus the current standing block, plus notice that the agent may
      amend the persona before starting
- [ ] **Read-only or write?** State it. If the agent should not edit, say so explicitly rather
      than relying on it not occurring to them.
- [ ] **Verification.** How should the agent check its own work before reporting? A brief that
      does not say leaves you to catch everything in review.

## Transferable lessons

These shipped with the skill. Each was learned from a real `PROMPT-DEFECT` on a production
codebase, then generalized. Append your own below as you earn them.

- **A prior agent's summary is a pointer to the code, never a substitute for reading it.** Your
  own briefs are where a summary quietly becomes a specification.

- **Do not put literal code in a brief.** It transfers your bugs into the work with your
  authority attached, so the agent implements them instead of catching them. Give the intent and
  point at a reference implementation.

- **If a sibling function already solves the problem, say "mirror it" rather than writing
  fresh.** Your fresh version will omit something the existing one has carried for a reason.

- **"Preserve current behavior" is only sound for capabilities reachable today.** If a feature is
  unshipped, gated elsewhere, or enforced by another service, preserving it *grants* rather than
  preserves. Ask per item: can a user exercise this right now?

- **When a predicate exists so that "case X always passes", verify the thing that makes it pass
  exists at the moment the predicate first runs.** Trace the bootstrap and first-write path, not
  the steady state.

- **One control's rule is not the column's rule.** Whenever a brief maps actions to a target,
  enumerate every writer of that target from source before writing the rule.

- **When auditing one branch, state whether dependencies on other open branches are in scope.**
  In a repo with stacked or parallel work they almost always are, and an agent inside your fence
  will not raise them.

- **When a fix depends on state being refreshed, invalidated, or polled, require the agent to
  enumerate every writer of that state and name the trigger.** A comment claiming freshness is a
  claim, not a guarantee.

- **When a name or string is consumed outside the code you are changing** (another service, an
  edge function, a different repo), grep for it repo-wide before changing its meaning.

## Learned defects

Appended when a `PROMPT-DEFECT` is logged, whether you caught it in review or an agent
challenged you and turned out to be right. Each entry: what was missing, and the check that
prevents it recurring.

Defects surfaced by a verified challenge are the valuable ones. They are the defects you did not
catch yourself, which means nothing else in this system would have caught them either. Write
those down the moment you uphold the challenge, before the resumed work pulls your attention
back to the task.

- (none yet)
