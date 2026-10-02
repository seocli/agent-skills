last_verified: 2026-10-02
volatile: false

# Forecasting, sizing, prioritisation, KPI tree, split tests

All numbers are `[H]` defaults unless a source id is given. A forecast is a range with assumptions.

## Baseline forecast

Weekly non-brand clicks (or per topic), two years if possible. Seasonal-trend decomposition or an
additive seasonal model, on log clicks when variance grows with level. Backtest on the last 12 weeks;
accept topic-level forecasts only with error under 20% (sMAPE), otherwise say "directional" and widen
intervals. Report 80% intervals. Exclude anomaly weeks (migration, outage, update, logging-error window) or model them.

## Uplift forecast

Per keyword or page group: incremental clicks = impressions x CTR(target position) - current clicks,
times the probability of reaching the target (from this site's own hit rate; priors 0.3-0.5 for a
refresh in striking distance, 0.1-0.25 for a new page on a competitive term). Subtract clicks of URLs
replaced; apply the feature correction. Time to effect, as assumptions only: refresh 4-12 weeks to a
first signal; new pages days to index, 3-6 months or more to mature; technical fixes weeks after
recrawl; links months. Never promise a date.

## Conversions and sizing

Incremental conversions = incremental clicks x landing-group conversion rate (shrink small samples:
(conv + k x CR_template) / (sessions + k), k about 100). Revenue = conversions x order value, or lead
value x close rate. Demand: sum of deduplicated group volumes (take the group maximum, not the sum),
serviceable (could rank), obtainable (achievable share). Always show clicks at positions 1, 3, 5.
Prospects without access: use SERP and volume estimates, label every number "modelled", and ask for
read access to replace estimates.

## Opportunity recipes

| Opportunity | Estimate |
|---|---|
| Striking distance | impressions per month x (CTR(3 or 5) - CTR now) |
| Low CTR at top | impressions x (expected CTR - actual CTR) |
| New page | group volume x CTR(target) x P(reach) |
| Refresh of decayed page | clicks prior - clicks now, times P(recover) |
| Cannibalisation fix | clicks at best single URL position - combined clicks now, if positive |
| Snippet rewrite | impressions x CTR delta from a test |

Net out overlaps (never add opportunities that touch the same query); show low, base, high; give time to effect.

## Prioritisation

Expected value = reach x impact x confidence x value per click; priority = EV / effort in person-days.
Impact comes from the recipes above, not from volume. Confidence: 1.0 prior test on this site, 0.8
strong mechanism with analogous wins, 0.5 plausible, 0.2 speculative. Respect dependencies (technical
fixes before content on the same template), capacity and seasonal windows. Portfolio about 70% known wins,
20% growth bets, 10% experiments. Re-score monthly from realised results. Tool "issue counts" are not impact.

## KPI tree

Outcome: non-brand organic conversions or revenue. Conversions = organic sessions x conversion rate.
Sessions ~ clicks x click-to-session ratio. Clicks = impressions x CTR. Levers: indexed useful pages,
intent coverage, rank distribution, snippets, feature wins, brand demand. Leading indicators: indexing
health, impressions on new pages, count of queries per band (1-3, 4-10, 11-20, 21-50, 51+), internal
link coverage, referring domains, Core Web Vitals pass rate. Separate brand from non-brand at the traffic
levels. Report YoY and scorecard status against pre-agreed thresholds (green at target, amber 80-99%, red under 80%).

## Split tests

Randomise across pages of one template, not visitors. At least about 100 pages per arm with real
traffic, matched by pre-period clicks, an A/A run first, parallel trends checked, 2-8 weeks, metric
and decision rule fixed in advance, no peeking. Analyse with difference-in-differences, a
page-and-time fixed-effects regression, or a Bayesian structural time-series counterfactual (Rule 13 `[A]`):
the control series must be unaffected by the change and its relationship to the treated series stable.
Ship when the interval for lift is above both zero and a minimum useful effect; else report
"no effect" or "inconclusive" with power. Single pages: synthetic controls from unaffected pages, low
confidence, an event-study plot. Five to ten percent lifts are hard to detect on noisy traffic.

## Sources

CAUSAL https://arxiv.org/abs/1506.00356 (accessed 2026-10-02) for the counterfactual method. Everything
else is practitioner default, not verified against a primary source.
