last_verified: 2026-10-02
volatile: true

# Sources and verification log (search-analytics)

Verification pass of DESIGN 9.3, access date 2026-10-02. "Confirmed" means the primary page was
re-fetched and said it. Anything not confirmed is tagged `[H]` or `[U]` where it is used.

## Sources

- GSC-METRICS: https://support.google.com/webmasters/answer/7042828 (accessed 2026-10-02). Confirmed: impression, click, CTR, position (topmost, averaged), "a link must get an impression for its position to be recorded".
- GSC-PERF: https://support.google.com/webmasters/answer/7576553 (accessed 2026-10-02). Confirmed: property vs page aggregation, preliminary data, search types (web, multimodal, image, video, news). The companion page https://support.google.com/webmasters/answer/17011364 (accessed 2026-10-02) confirms preliminary data only; it says nothing on anonymisation or retention.
- GSC-API: https://developers.google.com/webmaster-tools/v1/how-tos/all-your-data (accessed 2026-10-02). Confirmed: 50K rows/day/search type, 25,000 rows per request, dropped data with page/query dimensions, 2-3 day availability, searchAppearance two steps.
- GSC-RETENTION: https://support.google.com/analytics/answer/10737381 (accessed 2026-10-02). Confirmed quote: "Search Console keeps data for the last 16 months".
- GSC-ANOM: https://support.google.com/webmasters/answer/6211453 (accessed 2026-10-02). Confirmed: impressions logging error 2025-05-13..2026-04-27 (impressions, CTR, position; clicks unaffected); generative AI logging error 2026-08-13..17; Discover and other 2026 logging errors.
- GSC-GENAI: https://support.google.com/webmasters/answer/16984139 (accessed 2026-10-02) and announcement https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports (title and date seen; body not retrievable). Confirmed from the help page: impressions for AI Overviews and AI Mode, dimensions, worldwide rollout as of 2026-08-31, export button. API support not confirmed.
- GSC-REGEX: https://developers.google.com/search/blog/2021/06/regex-negative-match (accessed 2026-10-02). Confirmed: RE2, negative match.
- GSC-BRANDED: https://developers.google.com/search/blog/2025/11/search-console-branded-filter (accessed 2026-10-02). Confirmed: post exists (November 2025). Details below come from a search summary of the help pages, so they are `[U]`: classification is by an internal AI-assisted system, not a regex; top-level (domain or origin) properties only; sites need enough query volume; open to all eligible sites from 2026-03-11.
- GA4-ENGAGE: https://support.google.com/analytics/answer/12195621 (accessed 2026-10-02).
- GA4-THRESH: https://support.google.com/analytics/answer/9383630 (accessed 2026-10-02). Confirmed: thresholds for demographic data and search-query rows; Google signals not exported to BigQuery.
- GA4-BQ: https://developers.google.com/analytics/blog/2023/bigquery-vs-ui (accessed 2026-10-02).
- AI-FEATURES: https://developers.google.com/search/docs/appearance/ai-features (accessed 2026-10-02). Confirmed: AI feature traffic is counted in the Web search type; no additional requirements.
- CORE-UPD: https://developers.google.com/search/docs/appearance/core-updates (accessed 2026-10-02).
- STATUS-DASH: https://status.search.google.com/summary (accessed 2026-10-02). 2026 incidents: March spam 24 Mar; March core 27 Mar, 12 d; May core 21 May, 11 d 21 h; June spam 24 Jun; August spam 18 Aug; September spam from 24 Sep (US/Pacific time).
- CAUSAL: https://arxiv.org/abs/1506.00356 (accessed 2026-10-02). Brodersen, Gallusser, Koehler, Remy, Scott, 2015.

## DESIGN 9.3 items settled here

- **Search Console logging error 2025-05-13..2026-04-27: confirmed** by GSC-ANOM. It is no longer lore; it is a rule with the caveat that clicks are unaffected.
- **Generative AI performance report: confirmed** as a report with impressions and a worldwide rollout (GSC-GENAI). Still reported/unconfirmed: any Search Console API access, and the absence of clicks (the help page lists impressions as the metric but does not say clicks are impossible). Say "check the property before promising it".
- **Published CTR-by-position curves: no primary source.** Industry studies are dataset-specific and drift with AI answers; they stay `[H]`, never a default; fit on the client's data.
- **Not verified (kept `[H]`):** Broder (2002) and Jansen intent studies were not re-fetched (the paper URL returned 404 and the publisher page 403); the 4-class labels, overlap thresholds, hidden-query share of 30-50%, decay and forecast thresholds are practitioner defaults.
