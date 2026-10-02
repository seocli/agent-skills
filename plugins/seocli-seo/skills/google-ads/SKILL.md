---
name: google-ads
description: Google Ads and Merchant Center knowledge - account structure, campaign types, bidding and learning, measurement and consent first, feed quality, audit checklist with thresholds, recommendation triage, pacing, change history and the SEO-SEA link. Methodology only until the account data tools exist; not a command.
user-invocable: false
---

# google-ads

## When to use

Load it to review, plan or explain a Google Ads or Merchant Center account: structure, bidding,
conversion tracking, feed health, policies, pacing, Google's recommendations, reports. Use
`marketing-strategy` for unit economics and channel mix, `meta-ads` for Meta, `search-analytics` for
Search Console and GA4 semantics, `technical-seo` for landing-page speed and indexing, and
`methodology` for the finding and recommendation format.

**Data availability.** The account data tools (Google Ads, Merchant Center) are not available yet: a
post-launch server epic will add them, read-only. Today this skill gives method, checklists with
thresholds, triage tables and questions. Do not request CSV or other exports as a data source; numbers
the user states in conversation are `user_supplied`, unverified, and labelled so. Never invent a
tool name or an account figure; list what would be needed under NEEDS as a capability ("campaign
list with primary status and reasons") without a tool name. A full audit is therefore `partial`:
state what was not assessed.

## Rules

Platform facts, each with a source id defined in `references/sources.md`.

1. **Measurement first.** Audit conversion actions, values, deduplication and consent before any
   performance finding; bad tracking invalidates the rest (method, see `references/measurement-consent.md`).
2. **Smart Bidding evidence.** Google recommends judging Smart Bidding over a period with at least 30
   conversions, such as a month or longer, and 50 conversions for Target ROAS; these are evaluation
   recommendations, not activation requirements (gads-smartbid).
3. **Budget semantics.** For most campaigns daily spend can reach two times the average daily
   budget, while the monthly charge stays within 30.4 times the average daily budget (gads-budget).
4. **Optimization score is an estimate** of how well the account is set to perform, computed from
   settings, statistics and available recommendations; it is not Quality Score and not a result
   metric (gads-optscore).
5. **Auto-apply exists as a subscription per recommendation type** and is enabled per account; audit
   it explicitly (gads-recs).
6. **Responsive Search Ad limits:** 3-15 headlines of at most 30 characters, 2-4 descriptions of at
   most 90, two path fields of at most 15; pinning is not recommended for most advertisers and can
   lower ad strength (gads-rsa).
7. **Change history window.** `change_event` data reaches back 30 days only; queries need a date
   filter inside that window and a LIMIT of at most 10,000 rows (gads-change).
8. **Consent Mode v2** uses four signals: `ad_storage`, `analytics_storage`, `ad_user_data`,
   `ad_personalization`. Advanced mode loads tags before the dialog and sends cookieless pings
   when consent is denied, enabling modeling; basic mode blocks tags until consent. Audiences and
   remarketing need correct consent configuration, normally through a CMP (gads-consent).
9. **Merchant Center pricing reports** are for the retailer's internal use or those acting on its
   behalf; the data may not be resold, publicly displayed, advertised or aggregated across
   businesses. Price benchmarks need valid GTINs (gads-mcprice). Decision D2 applies, see below.
10. **Content API for Shopping** was sunset on 2026-08-18 and calls now fail progressively; feed and
    status integrations use the Merchant API (gads-mcapi).
11. **EU political advertising** as defined by Regulation (EU) 2024/900 is restricted on Google
    platforms, with exemption routes for official public information (gads-political).
12. **Merchant Center price benchmarks (D2).** Use them only from the client's own connected
    Merchant Center account, never across clients, never in pre-sales for a prospect, never in a
    multi-client summary or benchmark, and mark every report that contains them "uso interno"
    (internal use). If no connected account exists, do not show benchmark data at all.

## Heuristics

Defaults, overridable per client and always compared with the account's own history first.
Detail and thresholds: `references/audit-checklist.md`.

- Order of work: goal and unit economics, then measurement, then segmentation brand vs non-brand,
  then the binding constraint (budget, rank, data, target, approval, demand, site), then money at
  stake `[H]`.
- One major change per campaign at a time, logged, then wait 1-2 weeks or about 30-50 conversions
  `[H]`. Step sizes: target CPA down at most 20% per step, target ROAS up at most 15-20%, budget
  at most 20% per 7 days `[H]`.
- Consolidate for data: campaigns on Smart Bidding with under 15 conversions per 30 days are a
  structure finding `[H]`.
- Lost impression share to budget above 10% on a profitable campaign: raise or reallocate; lost to
  rank above 50% on a priority non-brand campaign: relevance, bid or quality problem `[H]`.
- Spend on search terms with no conversion and cost above 2 times target CPA above 15-20% of
  Search spend: negative-keyword work `[H]`.
- Pacing ratio (actual over linear expectation): 0.9-1.1 healthy, below 0.8 under, above 1.15
  over `[H]`; use bands, not the budget cap, to judge a day.
- Treat recommendations that raise spend as hypotheses; fixes for broken things as defects `[H]`.
- Week-over-week anomaly bands: cost and conversions 30%, CPA and CTR 25%, impression share
  15 points; compare with the same weekday of the last four weeks `[H]`.
- Reading the last 3-7 days for CPA decisions misleads when conversion lag is long `[H]`.
- Feed approval below 95% of the catalogue, or an ad disapproval rate above 5%, is a red flag `[H]`.
- Conversions in the account within +-15% of back-end orders is acceptable `[H]`.

## Do not recommend

- Optimization score as a KPI to report to clients or as a goal to chase.
- Accepting spend-raising recommendations (budget, broad match, new campaign types, target
  loosening) without evidence of efficiency.
- Auto-apply recommendations left on without review.
- Switching bidding strategy weekly, or stacking several changes in one learning window.
- Broad match with manual bidding or Maximise Clicks.
- Target ROAS on campaigns with constant or missing conversion value.
- Micro-conversions (page view, scroll, add to cart) as primary conversion actions.
- Mixing brand and non-brand in one Search campaign and reporting a blended CPA.
- Chasing "Excellent" ad strength with near-duplicate headlines.
- Showing Merchant Center price benchmarks across clients, to prospects, or without the internal-use note.
- Judging performance before tracking, consent and values are verified.
- Promising results from AI Max, Performance Max or Demand Gen without a test design.

## Italian market notes

- Consent Mode v2 and a Google-certified CMP are the baseline for EEA traffic; the Italian
  data-protection authority's 2021 cookie guidance asks for an equally prominent reject option, no
  cookie walls and renewed consent after at most six months. Whether a given analytics setup is
  lawful is a question for the client's legal adviser.
- Gambling advertising is banned in Italy by the Decreto Dignita (2018) with narrow exceptions;
  do not plan gambling campaigns, and flag restricted verticals (health, finance, alcohol).
- Prices shown to consumers include IVA; feed price and landing-page price must match, shipping and
  tax rules apply per country. "Was" prices must respect the Omnibus rule (lowest price in the
  previous 30 days) - check with the client's legal adviser.
- Political ads: Google restricts EU political advertising under Regulation 2024/900; NGOs, parties
  and public bodies need an alternative channel plan or an exemption check.
- Seasonality: saldi (January and July), Black Friday, Ferragosto, Natale; compare year over year.
- Language and location: Italian copy, location setting "presence" for local businesses, bilingual
  provinces need separate language targeting.
- Micro-businesses: budgets often below 10 times target CPA per month, which cannot leave learning;
  say so and favour Search with tight scope over Performance Max.
- Deliverables for Italian clients are in Italian with euro and 1.234,56 formatting; Merchant
  benchmark figures carry the note "uso interno".

## References

Read on demand.

- `references/structure-bidding.md`: hierarchy, campaign types, match types, negatives, bidding, learning, budgets, pacing.
- `references/measurement-consent.md`: conversion actions, enhanced and offline conversions, attribution, Consent Mode v2, audiences.
- `references/audit-checklist.md`: audit sequence, numbered checks with thresholds, questions to ask, quantifying impact.
- `references/recommendations-triage.md`: optimization score, recommendation triage table, change history, diagnostic flows.
- `references/quality-policies-seo.md`: Quality Score, RSA and assets, policies, SEO-SEA decision matrix, KPI and report structure.
- `references/merchant-center.md`: feed essentials, disapprovals, price benchmarks under D2, decision logic.
- `references/sources.md`: source ids, URLs, access dates, and the verification ledger.
