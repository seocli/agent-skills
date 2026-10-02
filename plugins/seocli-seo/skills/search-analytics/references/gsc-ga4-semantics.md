last_verified: 2026-10-02
volatile: false

# Data foundations and reconciliation

Tags: `[G]` primary re-verified (ids in `sources.md`), `[H]` default to override. Every figure in a
deliverable carries source, date range, filter, row count and caveat; a number without them is not a finding.

## Search Console

- CTR, position, aggregation, preliminary data, API limits, 16-month retention: see Rules 1-6 `[G]`.
- Position is blended across queries and pages. A page at 3 for one query and 40 for another shows a
  position that describes neither; analyse at (query, page) grain `[H]`.
- Never average positions; use an impression-weighted mean, sum(position x impressions) / sum(impressions) `[H]`.
- Search types are separate universes (web, multimodal, image, video, news; Discover has its own
  report). Do not sum them without saying so `[G GSC-PERF]`.
- Hidden queries: rare queries are withheld from rows but counted in totals. Measure it per site,
  hidden_share = 1 - sum(clicks over query rows) / total clicks, and report it in every query-level
  analysis. Typical shares reported by practitioners are 30-50% `[U]`.
- Store daily raw rows ourselves (retention 16 months, 50K row cap): extracts are totals with no
  dimension, query x page x date, page x date, query x date, country x device x date, search appearance.
  Mark truncation. Derive weekly or monthly views; never overwrite raw rows `[H]`.
- Compare equal windows: 28 or 91 days (whole weeks), same weekdays, same search type, country, device.
  Drop the last 2-3 preliminary days `[H]`.

## GA4

- Engaged session, engagement rate, bounce rate: Rule 10 `[G]`. Bounce here is not the old
  Universal Analytics bounce.
- "Organic Search" channel includes every search engine; filter to source google, medium organic when
  comparing with Search Console `[H]`.
- Landing page plus session source/medium is the right pair for SEO. GA4 gives no organic keyword;
  queries come only from Search Console at page level `[H]`.
- Attribution is last non-direct click, so organic gets credit only as the last non-direct touch and
  often assists rather than closes. State this in every conversion statement `[H]`.
- Explorations can show `(other)` rows on high-cardinality dimensions and thresholded rows `[G GA4-THRESH, GA4-BQ]`.
- Event-level retention in Explorations is configurable and short by default; use the BigQuery export
  for long history `[U]` (not re-fetched).

## Reconciliation check (run before trusting either source)

1. Same period, same site, google/organic only.
2. ratio = GA4 organic landing sessions / Search Console web clicks, weekly.
3. A step change above +/-20% means tracking, consent, redirect or tagging changed, not SEO `[H]`.
4. Normalise landing URLs on both sides: strip query string and fragment, lowercase host, one
   trailing-slash policy, resolve to the canonical.
5. Expect the ratio to be below 1.0 (consent, bounces before load, in-app browsers); stability matters more than level `[H]`.

## Common data errors

- Averaging CTR or position; comparing clicks with sessions as equals.
- Page-grouped position read as per-query.
- Reading preliminary days as a drop; unaligned windows; mixed search types, countries or devices.
- Ignoring hidden queries, so "top queries" looks complete.
- Ignoring site events: migration, tracking change, property change (domain vs URL prefix), a dated
  Google update (Rule 12), the logging-error window (Rule 8).
- Counting a URL change as lost queries.

## Sources

GSC-METRICS https://support.google.com/webmasters/answer/7042828; GSC-PERF https://support.google.com/webmasters/answer/7576553;
GSC-API https://developers.google.com/webmaster-tools/v1/how-tos/all-your-data; GSC-RETENTION
https://support.google.com/analytics/answer/10737381; GA4-ENGAGE https://support.google.com/analytics/answer/12195621;
GA4-THRESH https://support.google.com/analytics/answer/9383630; GA4-BQ
https://developers.google.com/analytics/blog/2023/bigquery-vs-ui (all accessed 2026-10-02).
