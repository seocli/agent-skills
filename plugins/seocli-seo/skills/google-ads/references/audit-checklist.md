last_verified: 2026-10-02
volatile: true

# Google Ads: audit sequence, checklist, questions

All thresholds are `[H]` defaults unless marked `[G]`; override per client and compare with the
account's own history first. Account data tools are not available yet (see `SKILL.md`): an audit
today is a guided interview plus method; mark every check "not assessed" until its data exists.
Each finding follows `methodology` (four fields). Rank by money: estimated wasted or foregone
monthly spend times confidence; report the top 5 with euro impact and a falsifier
("if CPA does not fall within 14 days after the negatives, the hypothesis is wrong; check the page").

## Sequence (measurement first)

1. Goal and unit economics: margin, order value or lifetime value, lead value, target CPA or ROAS,
   monthly budget. No target, no audit.
2. Measurement (section 9 below) before performance.
3. Brand vs non-brand and new vs returning, analysed separately.
4. The binding constraint: budget, rank, data, target, approval, demand, site.
5. Money at stake, then one change at a time with a 14-28 day check.

## Questions to ask the user (capability needs, no tool names)

- Which conversions matter to the business, and what is each worth? Which are primary?
- Which CMP and which Consent Mode mode are live? When did the banner last change?
- Is there a monthly budget and a target CPA or ROAS agreed with the client? Gross margin?
- Is Search Console linked, and is auto-tagging on? Is there a CRM and offline conversion import?
- Any recent site release, price change, tag change or catalogue change?
- Is auto-apply on? Who has account access? Last structural change and by whom?
- Is a Merchant Center account connected and which countries does the feed serve?
- Needed later from the account data capability: campaign list with primary status and reasons,
  conversion action config and volume, search-term report, change history, recommendations, feed status.

## Checklist

### 1 Health and access
1. Status ENABLED, billing set, no payment hold (red: suspended, payment failure).
2. Timezone and currency match the business and CRM.
3. Ex-agency users removed.
4. Auto-apply subscriptions: red if budget, broad match, keywords or assets apply without review.

### 2 Structure
5. Share of campaigns under 30 conversions per 30 days; red if over 50% of spend sits in Smart Bidding
   campaigns under 15.
6. Brand and non-brand split; Performance Max overlap with brand.
7. Geo "presence" for local; language matches ad copy; ad schedule and device sanity.
8. Search Partners and Display expansion on with segment CPA 1.5 times Search: red.
9. Naming and labels parseable.

### 3 Keywords and search terms
10. Wasted spend (no conversion, cost above 2 times target CPA) above 15-20% of spend: red.
11. Negative coverage: shared lists and account negatives exist; brand routing; zero negatives is red.
12. Broad match share of spend on manual or Maximise Clicks bidding above 30%: red.
13. Duplicate keywords; keywords with zero impressions for 90 days above 30% of the list.
14. Hidden search-term share above 40% of Search spend.
15. Brand: low CPC, impression share above 0.9 only if defending; competitor hijack check.

### 4 Ads and assets
16. At least one, ideally 2-3 active RSAs per ad group; POOR ad strength share at most 10%.
17. More than 3 pinned positions in one RSA: red.
18. Asset coverage: sitelinks at least 4, callouts at least 4, structured snippet, call if phone, images.
19. Disapproved or limited ads, broken final URLs (4xx, 5xx, redirect chains).

### 5 Bidding and budget
20. tCPA or Maximise Conversions under 30 conversions per 30 days; tROAS under 50 (Google evaluation
    guidance `[G]` gads-smartbid).
21. Target CPA more than 20% below actual for 2+ weeks (throttling) or more than 30% above (efficiency left).
22. Budget-limited and profitable: lost IS (budget) above 10% with CPA under target: raise.
23. Learning over 2 weeks, or more than 2 significant changes in 14 days (from change history).
24. Pacing outside 0.9-1.1.

### 6 Quality
25. Impression-weighted Quality Score under 6, or keywords with score 4 or below holding over 20% of spend.
26. Search rank-lost impression share above 50% on priority non-brand.
27. Landing page experience below average share; page speed (see `technical-seo`).
28. CTR by type is context only: brand Search 15-40%+, non-brand 3-8%, Shopping 0.5-1.5%, Display
    0.3-0.7%, video view rate 15-30% `[U]` practitioner ranges; use the account's history first.

### 7 Audiences
29. List sizes, customer match, converter exclusions, consent in the EEA.
30. Audiences in observation on Search; nothing restricting reach by accident.

### 8 Performance Max and Shopping
31. Asset groups: more than 30% assets rated LOW; each group has video, images in several ratios,
    text at maximum count.
32. Search themes, brand exclusion, URL expansion and final URL exclusions decided.
33. Feed approval at least 95%; GTIN coverage; custom labels; out-of-stock waste (see `merchant-center.md`).
34. Brand overlap between Performance Max, Shopping and Search (category insights).

### 9 Measurement (do first)
35. Primary conversions map to business goals; values; transaction ids; deduplication.
36. Enhanced conversions on; Consent Mode v2 in the EEA; offline import for leads; attribution model.
37. Auto-tagging on; GA4 linked; tracking template and suffix sane.
38. Account conversions vs back-end orders within +-15%.

### 10 Settings and compliance
39. Lookback windows fit the sales cycle.
40. Policy topics, restricted verticals, EU political ads, customer data and sensitive audiences.
41. Experiments running or concluded.
42. Change history sanity: many changes by unknown users or by auto-apply is red.

## Output

Per section pass / warn / fail with evidence (metric, value, window, threshold, entity), never a single
opaque score. Items that need data not yet available go under NOT ASSESSED.

## Sources

See `sources.md`.
