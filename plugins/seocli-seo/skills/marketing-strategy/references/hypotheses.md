last_verified: 2026-10-02
volatile: false

# Hypotheses, decision log, cadence

## Hypothesis card

```
ID: H-07
Belief: We believe <action> for <audience> will cause <effect>
Because: <first-principles reasoning, evidence level G|A|H|U>
Metric: <primary> (leading: <x>, lagging: <y>)
Baseline: <value, date> ; Target: <value> by <date> ; Minimum detectable: <value>
Test design: A/B | geo holdout | before-after with control | no test (judgement)
Sample or time needed: <n or weeks>
Kill rule: if <metric> < <threshold> after <time or spend>, stop and reallocate
Scale rule: if <metric> >= <threshold>, increase <budget or effort> by <x%>
Dependencies: ...
Owner / review date: ...
Result: confirmed | refuted | inconclusive ; Learning:
```

The card is the `methodology` four fields in one block: Because = observation, Dependencies =
dependency, Kill rule = falsifier, Metric's leading part = leading.

## Rules

1. State in advance what would refute it.
2. Fix thresholds before looking at data; kill and scale rules are never edited after the fact.
3. Minimum duration: one full business cycle and out of the learning phase (ads 2-4 weeks, SEO
   leading indicators 8-16 weeks).
4. One major change per channel per test window.
5. "Inconclusive" is a valid result: extend, redesign or drop, do not spin.
6. A refuted hypothesis is not re-proposed without new evidence.
7. Prioritise with impact x confidence x ease, weighted by the cost of being wrong and the learning
   value.
8. Small samples are noisy; see `measurement.md`.

## Decision log

One line per decision, kept in the workspace: date, decision, alternatives rejected, evidence ids,
expected outcome range, kill or scale rule, review date, outcome (filled at review), who decided.
Disagreements between specialists are recorded with the reason the decision went one way.

## Review cadence

| Frequency | What | Who |
|---|---|---|
| Daily or weekly | Anomaly alerts, pacing, tracking health | Specialists |
| Fortnightly | Sprint review, test status | Specialists and strategist |
| Monthly | Report, hypothesis table, budget moves | Strategist and client |
| Quarterly | Re-diagnose, update plan, larger reallocation, renewal case | Strategist and client |
| Yearly | Positioning check, creation investment review | Strategist |

## Sources

- Repo method; hypothesis card and cadence adapted from marketing/strategy §11 (`[H]`, practitioner).
