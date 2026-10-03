---
name: search-analytics
description: How to read and combine Search Console, Analytics and SERP data without misreading it - metric semantics, reconciliation, keyword and intent method, SERP overlap and features, striking distance, CTR curves, cannibalisation, decay, seasonality, brand split, forecasting, sizing, prioritisation, KPI tree, split tests and update correlation. Knowledge for the analyst; not a command.
user-invocable: false
---

# search-analytics

## When to use

Load it before interpreting any performance number: clicks, impressions, position, sessions, rank
changes, traffic drops, opportunity lists, forecasts or priorities. It tells you what the data does
and does not say and which analysis to run. Use `methodology` for the finding and recommendation
format, `seocli-tools` for how to fetch data and what it costs, `technical-seo` for crawl and
indexing causes, `content-quality` for page-level quality and page-type fit, `geo-visibility` for AI
answers. Data tools named here are planned: `seocli:get_search_console_data` (E7.4),
`seocli:get_analytics_data` (E7.5), `seocli:find_cannibalization` (E7.4), `seocli:check_serp`,
`seocli:research_keywords` (E5.1), `seocli:select_keywords` (E5.2). Never promise one that the live
tool list does not contain.

## Rules

Source ids are defined in `references/sources.md` (URL and access date 2026-10-02).

1. **Metric definitions.** CTR is clicks divided by impressions. Position is Google Search only; per
   query it is the topmost position the site held, then averaged. A position is recorded only when
   the link got an impression. [G] GSC-METRICS
2. **Totals follow the grouping.** Data grouped by query, country, device or date is aggregated by
   property; grouped by page or search appearance it is aggregated by page, so chart and table totals
   can differ. Label every table with its grouping. [G] GSC-PERF
3. **Fresh data is preliminary** and can change within hours; the API needs 2-3 days before data is
   typically available, so keep the last days out of trend comparisons. [G] GSC-PERF, GSC-API
4. **API limits.** At most 50K rows per day per search type, sorted by clicks; a request returns at
   most 25,000 rows (paginate with `startRow`); adding page or query dimensions can drop data, so take
   totals from a request without them. [G] GSC-API
5. **Search appearance needs two steps:** list the appearance values alone, then query each one
   filtered. [G] GSC-API
6. **Retention is 16 months.** Year-over-year beyond month 16 needs data stored by us. [G] GSC-RETENTION
7. **AI features are inside the Web search type** of the Performance report; a separate Search
   generative AI performance report exists, documents impressions for AI Overviews and AI Mode
   (excluding Discover), by page, country, date, device and search type, and was announced as
   available to all websites worldwide from 2026-08-31, with some properties possibly not yet
   enabled. [G] AI-FEATURES, GSC-GENAI
8. **Logged impressions were wrong for a documented window.** Google documents a logging error that
   affected impressions, CTR and average position from 2025-05-13 to 2026-04-27, with clicks
   unaffected; a generative AI logging error lowered impressions on 2026-08-13..17. Any impressions,
   CTR or position comparison touching those windows carries that caveat. [G] GSC-ANOM
9. **Regex filters use RE2** and support negative match. A branded-queries filter exists in the
   Performance report (introduced November 2025). [G] GSC-REGEX, GSC-BRANDED
10. **GA4 engagement.** An engaged session lasts over 10 seconds, or has a key event, or has 2 or more
    page or screen views; bounce rate is the share of sessions that were not engaged. [G] GA4-ENGAGE
11. **GA4 withholds and differs.** Thresholding can withhold rows; the BigQuery export differs from
    the interface (sampling, user counting, up to 72 h of late updates, `(other)` bucketing, no
    modelled consent data, no Google signals). State which one a number came from. [G] GA4-THRESH, GA4-BQ
12. **Core update reading.** Wait at least a week after the update completes, compare with the week
    before it started, treat small position moves as normal, expect recovery to take months; dates
    come from the Search Status Dashboard. [G] CORE-UPD, STATUS-DASH
13. **Counterfactual method.** A Bayesian structural time-series model predicts what would have
    happened without an intervention when randomised experiments are not possible. [A] CAUSAL

## Heuristics

Defaults the client may override; thresholds are in `references/gsc-analyses.md`.

- Reconcile first: GA4 organic landing sessions over Search Console web clicks, weekly; a step change
  above about 20% means tracking, consent or tagging, not SEO `[H]`.
- Analyse position at (query, page) grain over 90 days; never average CTR or position, recompute from sums `[H]`.
- Intent: start from query words, let the SERP decide; label mixed SERPs "mixed" `[H]`.
- Same page for two keywords when roughly 3-4 of the top 10 URLs are shared `[H]`; confirm on 20 pairs.
- Striking distance: position 8-20, at least 100 impressions in 90 days, brand excluded `[H]`.
- Fit CTR by position on the client's own non-brand data; published curves are sanity checks only `[H]`.
- Seasonality needs two full years; compare year over year, not month over month `[H]`.
- Forecasts are ranges with a stated backtest; sizing shows positions 1, 3 and 5 `[H]`.
- Prioritise by expected value over effort, with confidence from this site's own results `[H]`.
- Report brand and non-brand separately; the north star is non-brand organic conversions `[H]`.

## Do not recommend

- Averaging row CTRs or positions, or reading a page-level position as a per-query rank.
- Reading the last 2-3 days as a drop, or a 28-day window not aligned to weekdays.
- Equating Search Console clicks with GA4 sessions.
- Any ranking date or traffic promise; opportunity sizing from search volume times position-1 CTR.
- A Domain Authority KPI, a vendor "difficulty" or "health" score as an outcome.
- Treating a published CTR-by-position curve as a default for a client.
- Calling decay or cannibalisation from one noisy window, or merging URLs that serve different intents.
- Presenting modelled traffic (prospect sizing) as measured.
- Using the unverified Search Console API support for the generative AI report in a promise.

## Italian market notes

- Seasonality: Ferragosto (mid-August trough for B2B), saldi (January and July-August), Black Friday and
  Natale. Compare year over year and align working days; Easter moves between years.
- Cookie banners and the Garante's consent expectations lower GA4 counts, so GA4 organic sessions run
  below Search Console clicks; watch the ratio trend, not the level.
- Italian long-tail volume is small and noisy: group by SERP overlap and use ranges, prefer Search Console impressions for owned queries.
- Local intent ("a Milano", "vicino a me") needs country and device filters; mobile dominates.
- Client reports in Italian: 1.234,5 formatting, euro, DD/MM/YYYY, year-over-year comparisons.

## References

- `references/gsc-ga4-semantics.md`: data foundations, reconciliation check, common data errors; read on demand.
- `references/keyword-intent-serp.md`: keyword method, intent, SERP overlap, SERP features, competitor gap; read on demand.
- `references/gsc-analyses.md`: striking distance, CTR curves, cannibalisation, decay, seasonality, brand split, update correlation; read on demand.
- `references/forecasting-prioritisation.md`: forecasting, sizing, prioritisation, KPI tree, split tests; read on demand.
- `references/sources.md`: source ids with URL, access date and verification log; read on demand.
