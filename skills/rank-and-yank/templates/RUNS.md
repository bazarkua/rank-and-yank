# Run log: <project>

Append-only, scoped to this project. One line per delegated run, plus `MINT`, `PROMOTE`, and
`RETIRE` events.

Format:

```
YYYY-MM-DD | persona | tier | score | C# X# S# R# | bonus | task summary | note
```

- **score**: mean of the four dimensions to one decimal, plus any bonus, capped at 10. `-` only
  when the attribution rule fires and the agent did not catch the defect.
- **C** correctness, **X** completeness, **S** spec adherence, **R** report quality
- **bonus**: the challenge and amendment adjustments **summed**, or `-` if neither was raised.
  Challenge: `+1.0` damage-preventing, `+0.5` improvement, `0` evidenced but wrong, `-0.5` no
  evidence. Amendment: `+0.5` accepted, `0` declined, `-0.5` self-serving.
- **note**: `prompt ok`, or `PROMPT-DEFECT: <what the brief was missing>`, or
  `CHALLENGE <ruling>: <what they caught>`, or `PERSONA-AMENDMENT <ruling>: <the change>`, or
  the reason an Opus subagent was hired. Name each ruling separately when both fired.

A `PROMPT-DEFECT` run does not count against the agent's average, and the defect goes to a
`BRIEF-CHECKLIST.md` instead. But if the agent **caught** the defect rather than being tripped
by it, it gets a real score on the corrected work plus the bonus. Challenge, correction, and
resumed work are one run and one line.

Examples of the shape, not real runs:

```
2026-01-01 | candidate-scout | sonnet | 8.8 | C9 X9 S9 R8 | -    | map the pipeline entry points | prompt ok
2026-01-01 | MINT sonnet-01-scout from candidate-scout, founding 8.8
2026-01-02 | sonnet-01-scout | sonnet | -   | C- X- S- R- | -    | audit config loading | PROMPT-DEFECT: no output shape given, agent guessed
2026-01-04 | sonnet-01-scout | sonnet | 9.3 | C9 X8 S9 R9 | +1.0 | refactor the loader | CHALLENGE upheld, damage: brief targeted the deprecated copy under legacy/, real callers use core/. Corrected and resumed via SendMessage.
2026-01-05 | sonnet-02-smith | sonnet | 7.0 | C8 X8 S7 R7 | -0.5 | add retry to the client | CHALLENGE denied, no evidence: claimed the API was rate-limited, nothing in code or docs
2026-01-06 | sonnet-02-smith | sonnet | 9.0 | C9 X9 S8 R9 | +0.5 | extract the parser | PERSONA-AMENDMENT accepted: output shape called for a flat file:line list, 40 hits made it unreadable, grouped by module instead
2026-01-07 | haiku-standard  | haiku  | n/a | n/a         | n/a  | list every *.sql under migrations/ | mechanical
```

---

## Runs
