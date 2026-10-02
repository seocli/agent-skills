last_verified: 2026-10-02
volatile: true

# Google Ads: recommendations, optimization score, change history, diagnostic flows

## Optimization score

- It is an estimate of how well the account is set to perform, built from statistics, settings,
  available recommendations and ecosystem trends; applying or dismissing recommendations changes
  it `[G]` gads-optscore. The API exposes it at customer and campaign level and the account-level
  weight to combine accounts `[G]` gads-recs.
- Stance `[H]`: a hygiene signal, not a goal. A full score with a poor account is common; a 60%
  score with excellent return is fine. Never present it to clients as a KPI.

## Triage table (default action by recommendation family)

Recommendation type names come from the API enum and change; map by meaning `[U]`.

| Recommendation | Default | Reason |
|---|---|---|
| Fix conversion tracking, enhanced conversions, consent mode | ACCEPT | foundational |
| Fix disapproved ads, feed or policy; repair broken URLs | ACCEPT | direct loss |
| Add or improve RSA assets, sitelinks, callouts, images | ACCEPT after review | cheap; check brand and legal wording |
| Negative keyword suggestions | REVIEW, then accept | confirm no converting terms |
| Switch to Maximise Conversions / Value or target CPA / ROAS | ACCEPT only with enough conversions and verified tracking | else REJECT |
| Raise target CPA or lower target ROAS | REVIEW | volume over efficiency |
| Raise budgets | ACCEPT only if profitable and budget-limited | else REJECT |
| Broad match / "use broad match" | REVIEW; accept with Smart Bidding and strong negatives | REJECT with manual bidding |
| New keywords from search terms | REVIEW as exact or phrase | only if converting |
| Search Partners / Display expansion | REJECT by default | test by segment |
| Auto-apply | REJECT (disable) | loss of control |
| Create Performance Max | REJECT unless planned | brand cannibalisation |
| Remove redundant or low-performing keywords | REVIEW; usually REJECT | harmless |
| Responsive display or dynamic search additions | REVIEW | variable quality |
| Audiences, customer match, remarketing | ACCEPT if consent and lists are ready | |
| Generate assets to raise ad strength | REVIEW text for accuracy and legal | |
| Move budget between campaigns | REVIEW | often matches goals |
| New campaign types (Demand Gen, app) | REJECT unless in the strategy | |
| Competitor, brand or AI Max bidding | EXPERIMENT only | |

Defaults are `[H]`. Accept when it fixes a defect, is cheap and reversible, or has a clear leading
indicator tied to a documented goal; reject when it raises spend without efficiency evidence.
Google's estimate is potential minus base metrics: discount it by 50% and compute the incremental
cost per conversion before accepting spend-raising items `[H]`. Dismiss with a reason in the change
log; review monthly.

## Change history

- `change_event`: last 30 days, date filter required, LIMIT at most 10,000 `[G]` gads-change. It records
  time, resource type, user, client type (web, API, automated rule, recommendations, subscription,
  scripts), operation and changed fields. Resource-level change status has a longer horizon but less
  detail `[U]` (about 90 days in the research notes).
- Use: line up cost or conversion anomalies with changes to strategy, budget, targets, conversion
  actions, URLs, negatives and auto-applied recommendations. Red `[H]`: more than 3 strategic changes
  in 14 days on one campaign; any change by the recommendations-subscription client type.
- Anomaly bands: see `SKILL.md` Heuristics.

## Diagnostic flow: "conversions fell 35% week over week"

1. Tracking: conversion volume by source; tag change, consent banner update, site release.
2. Serving: primary status reasons, disapprovals, budget exhausted early, billing.
3. Demand: impressions, impression share, seasonality, competitors.
4. Efficiency: conversion rate vs clicks, device split, landing page, stock.
5. Changes: strategy, targets, accidental negatives, auto-applied items.
6. Output: ranked hypotheses, each with evidence and a falsification test.

## Diagnostic flow: "return below target on Performance Max"

Brand share of conversions (search categories); new-customer mix; asset group results; product
winners and losers; feed price gap (own account only, D2); URL expansion pages; then exclude brand,
split asset groups by margin tier, move the target in steps of at most 15% and wait two weeks `[H]`.

## Sources

See `sources.md` (ids gads-optscore, gads-recs, gads-change).
