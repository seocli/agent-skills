last_verified: 2026-10-02
volatile: false

# Decision record (`verdict.md`)

The moderator drafts it from frame, pack and rounds. It adds no position. The skeptic fills the grades.

```
# Decision record D-<YYYYMMDD>-<slug>
Status: proposed | accepted | edited | rejected (by the user, <date>)
Question: <one line>          Options: O1 <...>  O2 <...>
Evidence: pack <path>, facts F1..Fn, collected <dates>, provenance mix
Seats: <seat (model)> ...      Rounds: R1, R2[, R3]      Mode: debate|panel|team|quick

## Positions
| Seat | R1 | R2 | Confidence R1 -> R2 |

## Decision
Chosen: <option>   Because: <2-3 lines with fact ids>
Not doing: <explicit list>

## Actions (each with the 4 fields)
| R-id | Action | Observation | Dependency | Falsifier (metric, threshold, window, action on failure) | Leading indicator | Owner | Effort | Credits | Skeptic grade |

## Objections
Survived: <objection, seat, why still open>
Refuted: <objection, refuted by fact ids>
Dissent: <seat, view, condition under which it would win>

## Open questions and data needs
| Need | seocli tool (availability) | Estimated credits | Decides what |

Review date: <date>   Kill rule: <condition>   Scale rule: <condition>
Skeptic overall grade: SURVIVES | WEAKENED | UNTESTABLE
Result (filled at review): confirmed | refuted | inconclusive; learning: <...>
```

## Conflict rules

- Factual: becomes a NEEDS line. One bounded gap-fill only with explicit user approval and within the
  confirmation threshold; new facts get new ids and every seat sees them in R2.
- Value or priority: arbitrated by the strategist's rule, incremental gross profit per euro and time to
  first signal. The losing view is kept as dissent.
- Untestable: cannot be a recommendation; listed as an open question with what would make it testable.
- Process: seat off-remit or schema broken is rejected once; a repeated R1 in R2 ends the round.
- At the review date reopen the record, fill Result; a refuted decision is not re-recommended without new evidence.
