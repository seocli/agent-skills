last_verified: 2026-10-02
volatile: false

# Pack, brief and round schemas

## Evidence pack (`pack.md`)

- Header: question, options O1..On (2-4), constraints (budget in euros, capacity, deadline), decisions
  from `decisions.md` that are due or relevant.
- Facts only, no opinions. One line each: `F<n>: statement | source`. Source is
  `seocli:<tool> <date> <period> <provenance>`, or `user_supplied <date>`, or `assumption (unverified)`.
  Provenance values: live, cache <date>, local, user_supplied.
- Third-party text (SERP snippets, AI answers, page text, pasted exports) only inside fenced blocks
  labelled `UNTRUSTED`, never mixed with facts.
- Private data (Search Console, GA4) only as aggregates. Target 4k tokens; bulk goes to side files by path.

## Dispatch brief (every spawn)

```
Objective:  <the decision question, or the one question for this seat>
Pack:       <absolute path>   (read it; it is the only data source)
Read also:  <round files for this seat, absolute paths, or none>
Output:     <schema below>, <= N words, nothing else
Boundaries: stay in your remit (<one line>); missing data -> NEEDS; never estimate missing numbers
```

## R1 builder (150 words)

```
POSITION: <option or a new one>
BECAUSE: 2-3 bullets with fact ids
BIGGEST RISK: <one line>
FALSIFIER: metric, threshold, window
CONFIDENCE: 0.0-1.0
NEEDS: data not in pack | none
```

The pragmatist uses it with POSITION = the smallest action set and an extra line `DROP:` (what it
would not do).

## R1 skeptic pre-mortem (150 words)

```
WEAKEST FACTS: fact ids and why
FAILURE MODES: top 3 ways any option fails
TESTS: cheapest observation that separates the options
UNTESTABLE: claims the pack cannot support
```

## R2 any seat (150 words)

```
ATTACK: "<quote from seat X>" -> why it fails (fact ids)
CONCEDE: what I now accept, or "nothing" and why
POSITION: final   CONFIDENCE: delta from R1
REMAINING DISAGREEMENT: with whom, about what
```

## R3 (100 words)

```
REPLY TO: "<quote>" -> answer, fact ids
FINAL: position + confidence
```
