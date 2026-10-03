last_verified: 2026-10-02
volatile: true

# Search Console analyses

Thresholds are `[H]` defaults. Fetch with `seocli:get_search_console_data` (E7.4) once it exists;
cannibalisation has a planned tool, `seocli:find_cannibalization` (E7.4). Until then, ask for an export.

## Striking distance

(query, page) rows over the last 90 days, position 8-20, impressions at or above 100 (30 for small
sites), brand removed, best page still the right intent. Estimated gain = impressions x (CTR at target
position - actual CTR), target position 3 and 5 as conservative and optimistic scenarios. Diagnose why
it is stuck: intent mismatch, thin content vs the top results, title and snippet, weak internal links,
weak external links, features above. If daily position has a standard deviation above 8, the query is
unstable, not "page 2".

## Low CTR at the top

Position 3 or better, enough impressions, CTR under 0.6 x the site's expected value. Check features
above first, then snippet: title mismatch or truncation, no benefit or brand, no date.

## CTR curves

Build from the client's own data: per rounded position bucket, CTR = sum clicks / sum impressions over
non-brand queries, desktop and mobile separately. Require 1,000 or more impressions per bucket or merge
buckets; interval = CTR +/- 1.96 x sqrt(CTR(1-CTR)/impressions). Brand inflates position-1 CTR, so
exclude it. Refit every quarter. Published industry curves are dataset-specific and shift with AI
answers: sanity check only, never a default for a client (DESIGN 9.3: no primary source; `[H]`).
Bucketing by an averaged position blurs the curve; prefer exact ranks from `seocli:check_serp` when available.

## Cannibalisation

Candidate: a query with 100 or more impressions where two or more URLs each hold at least 20% of the
impressions over 90 days. Signature: weekly share flip-flops between URLs. Check the SERP: a mixed
informational/transactional SERP can justify two URLs; a double listing can be healthy. Impact =
combined clicks vs the best single URL. Actions: consolidate with 301 and updated internal links,
differentiate intents, canonicalise duplicates, fix anchors, or accept. Query rows are truncated and
partly hidden, so some competition is invisible.

## Decay

Weekly clicks per page for 13 months or more (our stored history beyond 16). Flag when trailing
12-week clicks are under 70% of the prior 12 weeks, year over year is below -20%, and impressions are
stable or falling. Diagnose with impressions, position, CTR:

- impressions down, position flat: demand fell or features took visibility;
- position down, impressions flat: lost ranking (competitors, staleness, technical, update);
- CTR down, position flat: snippet or feature change;
- everything flat but clicks down: tracking or data issue, check the totals.

Actions: refresh with new data, add missing subtopics, relink, fix technical, merge, or redirect.
Recoverable clicks = prior minus current.

## Seasonality

At least two full years. index_m = mean clicks in month m / mean across months, per year, then
averaged; detrend first when growth is strong. Use it to time refreshes 8-12 weeks before peaks, to
avoid reading dips as decay and to place experiments off-peak. 16 months give only about 1.3 cycles.

## Brand and non-brand

Use the Performance report's branded-queries filter where available (Rule 9) or a versioned regex
(RE2, negative match for exclusion); review the top 200 queries by hand. Classes: brand,
brand plus modifier, generic, competitor. Report non-brand clicks, impressions, share, weighted
position and CTR by bucket separately. Hidden queries make non-brand under-observed: report the hidden share.

## Other cuts

Query-intent mix; question queries; new vs lost queries; country and device skew (wrong-country
ranking suggests an hreflang issue); page groups by URL template (basis for split tests);
indexed pages with zero impressions.

## Update correlation

List dated updates from the Search Status Dashboard in the window (Rule 12). Compare 14 days before
with the period after completion plus a week, by query class, page type, device and country. Overlap
is a hypothesis; look for own changes, seasonality and the logging-error window (Rule 8) first.

## Anomaly checklist (traffic moved)

1. Data issue (totals, GA4 ratio, tracking, preliminary days, logging error)? 2. Seasonal (year over year)?
3. Own change (deploy, migration, robots, canonical)? 4. Indexing or crawl? 5. SERP or competitor?
6. Google update? 7. Demand (the impressions/position/CTR table above)? 8. Brand or PR? Report evidence
for and against each, ordered by likelihood.

## Sources

CORE-UPD https://developers.google.com/search/docs/appearance/core-updates; STATUS-DASH
https://status.search.google.com/summary; GSC-ANOM https://support.google.com/webmasters/answer/6211453;
GSC-BRANDED https://developers.google.com/search/blog/2025/11/search-console-branded-filter;
GSC-REGEX https://developers.google.com/search/blog/2021/06/regex-negative-match (all accessed 2026-10-02).
Thresholds are practitioner defaults, not Google figures.
