# Roster standings: <project>

This roster and its 8-slot cap belong to **this project only**. Other projects keep their own
under `projects/<project>/`, with their own numbering starting at `01`.

Roster cap: **8 Sonnet slots.** When a 9th persona is minted, the lowest-ranked persona past its
protection window is retired and its file is moved to `personas/retired/`. The cut is relative,
so a strong persona can still be cut for ranking last in a strong field.

Protection: a persona is exempt from the cut for its **first 3 runs**, plus 3 further runs for
each verified damage-preventing challenge on its record.

Rank by average, descending. Tie-break: fewer runs ranks lower, since it is less proven.

| Rank | Persona | Runs | Avg | Promoted | Status |
|------|---------|------|-----|----------|--------|
| | | | | | *roster empty* |

**0 of 8 slots filled. 8 open.** Personas are minted from runs that score 8.5 or higher on a
task shape that will recur. Until a slot is claimed for a given mission, Sonnet runs go out as
`candidate-<slug>` and are evaluated for a founding slot.

## Retired

None yet.

## Notes

Set `OVER-CAP` here if a mint pushes the roster to 9 while every eligible persona is still
inside its protection window. Take the deferred cut as soon as one loses protection, and do not
mint again while over cap.
