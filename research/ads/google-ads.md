# Google Ads + Merchant Center — research for the seocli plugin rebuild

Access date for all sources: 2026-10-02. Audience: agency campaign managers; seocli reads Google Ads via API (read-only diagnosis, suggested fixes) and Merchant Center (price benchmarking).

## 0. Method and confidence

- Fetched live this session (high confidence): Ads API best practices, optimization score help (answer 9061546), Smart Bidding help (7065882), Merchant Center pricing insights (9626903), Ads API field reference landing (fields/v21/overview redirects to a newer-version nav; fetch summary says v25 is latest — verify the pinned version before coding).
- Everything else is paraphrased from the Google Ads Help / Ads API / Merchant Center docs and practitioner consensus as known to the author; items marked [VERIFY] should be re-checked against the URL before they become hard-coded thresholds. Thresholds labelled "heuristic" are practitioner rules, not Google rules.
- Two fetch attempts 404'd (Merchant 14994803, Ads 10548326): do not cite those URLs.
- Principle for the plugin: Google rules (policy, minimums) are facts; practitioner thresholds are defaults the user can override. Every finding should carry: observation -> evidence (GAQL fields) -> hypothesis -> falsification check -> action -> leading indicator.

Primary URLs (all accessed 2026-10-02):
- https://developers.google.com/google-ads/api/docs/best-practices/overview
- https://developers.google.com/google-ads/api/docs/query/overview (GAQL)
- https://developers.google.com/google-ads/api/fields/v21/overview (field reference; pick current version)
- https://developers.google.com/google-ads/api/docs/recommendations
- https://developers.google.com/google-ads/api/docs/change-event (change_event, change_status)
- https://developers.google.com/google-ads/api/docs/campaigns/bidding/overview
- https://developers.google.com/google-ads/api/docs/conversions/overview
- https://developers.google.com/google-ads/api/docs/reporting/segmentation
- https://support.google.com/google-ads/answer/9061546 (optimization score)
- https://support.google.com/google-ads/answer/7065882 (Smart Bidding)
- https://support.google.com/google-ads/answer/6167118 (Quality Score) [VERIFY id]
- https://support.google.com/google-ads/answer/7684791 (Responsive Search Ads) [VERIFY id]
- https://support.google.com/google-ads/answer/2497836 (match types) [VERIFY id]
- https://support.google.com/google-ads/answer/6008942 (EU user consent policy) [VERIFY id]
- https://support.google.com/google-ads/answer/9888656 (enhanced conversions) [VERIFY id]
- https://support.google.com/merchants/answer/9626903 (pricing insights / benchmark)
- https://support.google.com/merchants/answer/7052112 (product data spec) [VERIFY id]
- https://support.google.com/merchants/answer/6149970 (product disapprovals / item issues) [VERIFY id]
- https://support.google.com/adspolicy/answer/6008942 (Google Ads policies hub) [VERIFY id]
- https://www.thinkwithgoogle.com (consumer/measurement research; use for narrative, not rules)

---

## 1. Account structure

### 1.1 Hierarchy (API resource names)
- Manager account (MCC) `customer` with `manager=true` -> client accounts (`customer_client` view lists the tree: `customer_client.id`, `.level`, `.manager`, `.descriptive_name`, `.currency_code`, `.time_zone`, `.status`, `.test_account`).
- Customer -> campaign -> ad_group -> ad_group_ad / ad_group_criterion (keywords, audiences). PMax: campaign -> asset_group -> asset_group_asset / asset_group_signal / listing groups. Shopping: campaign -> ad_group -> product groups (ad_group_criterion type LISTING_GROUP) or Standard Shopping/PMax feed-only.
- Shared sets: shared_set + shared_criterion (negative lists), campaign_shared_set. Budgets: campaign_budget (shareable, `explicitly_shared`).
- Bidding portfolios: bidding_strategy (shared) vs standard (per-campaign) strategy.
- Conversion setup: conversion_action, customer.conversion_tracking_setting, conversion_goal_campaign_config, custom_conversion_goal.
- Asset library: asset, campaign_asset, ad_group_asset, customer_asset (sitelinks, callouts, structured snippets, images, call, lead form, price, promotion).

### 1.2 Structure principles (campaign manager view)
- One campaign = one goal + one budget + one bidding strategy + one geo/language intent. Mixing brand and non-brand in one Search campaign hides inflated efficiency; split.
- Consolidate for data: Smart Bidding needs conversion volume. Heuristic: >=30 conv/30d per campaign for tCPA/max conv, >=50 for tROAS (Google's own wording in Smart Bidding help: measure over >=30 conversions, 50 for target ROAS). Fragmenting 200 conversions across 20 campaigns starves all of them.
- Ad groups: tightly themed (Google no longer needs single-keyword ad groups; "SKAG" is legacy). Heuristic 5–20 closely related keywords per group; one theme per RSA's message.
- Naming convention must encode: channel | goal | brand/non-brand | geo | match/theme. Auditor flags unparseable names as a hygiene issue, not a performance issue.
- Brand vs non-brand vs competitor vs generic vs DSA/AI Max: separate campaigns, separate budgets, separate targets (brand CPA is 3–10x lower; blended target destroys both).
- Geo: location setting should be "Presence" (people in/regularly in), not "Presence or interest" unless intentional (`campaign.geo_target_type_setting.positive_geo_target_type`). Wrong default is a classic waste source for local/EU agencies.
- Network settings: Search campaigns with Search Partners and Display Expansion on by default; check `campaign.network_settings.target_search_network`, `.target_content_network`, `.target_partner_search_network`.
- Languages: language targeting must match the ad copy language (`campaign_criterion` type LANGUAGE).
- Account-level: auto-apply recommendations, auto-created assets (`customer.asset_automation`? [VERIFY], "Automatically created assets" in settings), URL expansion/final URL expansion in PMax, customer match, conversion goals at account level (default goals vs campaign-specific).

### 1.3 Diagnostic data to expose (GAQL)
```
SELECT customer_client.id, customer_client.descriptive_name, customer_client.level,
       customer_client.manager, customer_client.status, customer_client.currency_code,
       customer_client.time_zone FROM customer_client
SELECT campaign.id, campaign.name, campaign.status, campaign.serving_status,
       campaign.primary_status, campaign.primary_status_reasons,
       campaign.advertising_channel_type, campaign.advertising_channel_sub_type,
       campaign.bidding_strategy_type, campaign.start_date, campaign.end_date,
       campaign.network_settings.target_search_network,
       campaign.network_settings.target_content_network,
       campaign.network_settings.target_partner_search_network,
       campaign.geo_target_type_setting.positive_geo_target_type,
       campaign.geo_target_type_setting.negative_geo_target_type,
       campaign_budget.amount_micros, campaign_budget.explicitly_shared,
       campaign.optimization_score
FROM campaign WHERE campaign.status != 'REMOVED'
```
`campaign.primary_status` and `primary_status_reasons` are the API's "why is this not serving" signal (e.g. NOT_ELIGIBLE, LIMITED, LEARNING, BUDGET_CONSTRAINED, BIDDING_STRATEGY_LEARNING, BID_STRATEGY_MISCONFIGURED, MISSING_CONVERSION_TRACKING [VERIFY enum names against current version]). Exposing these first saves most triage time.

### 1.4 Common mistakes
- Dozens of tiny campaigns with <10 conversions/month each; shared budgets masking which campaign is limited.
- Brand and generic mixed; PMax cannibalizing brand search (see 3.3, 16).
- Everything on "Presence or interest".
- Duplicated keywords across campaigns competing against each other (check with keyword overlap, ad_group_criterion text+match across campaigns).
- Leaving "Search Partners" and "Display expansion" on without segment analysis (`segments.ad_network_type`).

---

## 2. Campaign types: when to use which

| Type | `advertising_channel_type` | Use when | Do not use when |
|---|---|---|---|
| Search | SEARCH | Capturing existing intent; lead gen; high-value brand/non-brand queries | Budget < ~10x target CPA/month (cannot exit learning) |
| Performance Max | PERFORMANCE_MAX | Goal-led, conversion volume available, feed + creative assets exist; ecommerce, local, lead gen with offline value | No conversion tracking; need strict query control; tiny budgets; need placement reporting |
| Shopping (Standard) | SHOPPING | Need product-group level control, priority/negative keywords, brand-only isolating; simple catalogs | Large catalogs where PMax feed-only covers it and volume is low |
| Demand Gen | DEMAND_GEN | Upper/mid-funnel on YouTube, Discover, Gmail; visual product demand creation; lookalike/custom segments | Direct response without conversion data; expecting search-like CPA |
| Video (YouTube) | VIDEO | Reach/awareness, consideration, view-based remarketing | Pure last-click ROI evaluation |
| Display | DISPLAY | Remarketing, cheap reach, specific placements; with good exclusions | As main prospecting engine for small budgets; poor-quality placements |
| App | MULTI_CHANNEL | Mobile app installs/in-app actions | Not applicable to most agencies' web clients |
| Local / Hotel / Travel | LOCAL, HOTEL, TRAVEL | Niche | — |
| AI Max for Search | feature on SEARCH campaigns (`campaign.ai_max_setting` [VERIFY name]) | Expand reach via keywordless/landing-page match and text customization on existing Search campaigns; test via experiment | Where brand control, exact query control or compliance wording matter (regulated sectors); enable only with experiment |

[VERIFY] Demand Gen replaced Discovery and Video Action campaigns in 2024–2025; Smart campaigns are legacy. Check `advertising_channel_type` enum for current names.

### 2.1 Search
- Core intent channel. Segments: brand, non-brand high-intent, competitor, generic/problem-aware, DSA/AI Max for coverage.
- KPI: conversion rate, CPA/ROAS, impression share (`search_impression_share`, `search_budget_lost_impression_share`, `search_rank_lost_impression_share`, `search_top_impression_share`, `search_absolute_top_impression_share`).
- Campaign-manager reasoning: "Where is the demand and are we showing for it? Lost IS to budget -> money problem. Lost IS to rank -> bid/quality/relevance problem."

### 2.2 Performance Max
- Single campaign across Search, Shopping, YouTube, Display, Discover, Gmail, Maps. Inputs: asset groups (text, images, logos, videos), audience signals, optionally feed (Merchant Center), final URL expansion, brand exclusions, search themes.
- Reporting (API): `asset_group`, `asset_group_asset` with `performance_label` (PENDING, LEARNING, LOW, GOOD, BEST), `campaign_search_term_insight` / `campaign_search_term_view` (category-level search insight; full search terms limited), `performance_max_placement_view` (placement report), `asset_group_product_group_view` for shopping-side, `asset_group_top_combination_view`.
- Brand exclusions: `campaign.brand_guidelines`? [VERIFY]; brand exclusion lists via shared sets of type BRAND_LIST / negative keywords at campaign level (PMax negative keywords supported at account/campaign level).
- Pitfalls: cannibalizing brand search (PMax prefers search term matched to Search campaign with same query only if Search keyword is exact-identical and eligible; otherwise PMax can serve); tracking "Store visits/new customers" misconfigured; feed-only PMax "scaling" at poor margin; all conversion goals including low-value ones (page views, add-to-cart) make bidding optimize noise.
- Manager's checks: new-customer acquisition goal set? asset coverage (one video at least, multiple images ratios, headlines up to 15, descriptions up to 5; check `asset_group.ad_strength`); signals defined; asset group per theme/product category; URL expansion decision; excluding brand if brand is covered in Search.

### 2.3 Shopping / Standard Shopping
- Data source: Merchant Center feed linked via `merchant_id` on campaign `shopping_setting.merchant_id`. Priority (LOW/MED/HIGH) still exists on Standard Shopping.
- Segment: `segments.product_*` (brand, category level 1..5, item_id, title, type l1..l5, custom_attribute0..4, channel, condition), `shopping_performance_view` and `shopping_product` resources for status/issues (`shopping_product.status`, `.issues`, `.eligible_destinations`).
- Use: separate brand/non-brand products; margin tiers via custom_label; hero SKUs; query isolation with campaign priority and negative keywords.

### 2.4 Demand Gen
- Creative-led. Needs: strong images/video, audiences (lookalikes, custom segments, customer match, your data), conversion value for tROAS later. Evaluate on assisted conversions/view-through and incrementality, not last-click CPA only.
- Typical use: warm-up for Search; channel controls (YouTube In-feed, Shorts, Discover, Gmail) in `campaign.demand_gen_campaign_settings` [VERIFY].

### 2.5 Video
- Goals: reach (tCPM), view (CPV), action (Video action → Demand Gen). Metrics: `video_quartile_p25_rate`..`p100_rate`, `average_cpv`, `video_view_rate`, `engagements`, `view_through_conversions`.
- Red flag: frequency > ~6–8/week on same audience without a CTA; view rate < ~15–20% for in-stream (heuristic).

### 2.6 Display
- Remarketing + managed placements; use exclusions (`campaign_criterion` PLACEMENT negative, content labels, topics). Red flags: mobile app placements with very high CTR and zero conv (accidental clicks); `segments.ad_network_type` and placement report (`group_placement_view`, `detail_placement_view`).

### 2.7 Decision shortcuts a campaign manager uses
1. Is there ≥30 conv/month and reliable conversion value? If no -> start Search with manual/Max Clicks (with cap) or Max Conversions, fix tracking first.
2. Retail with feed? Brand/non-brand Shopping or PMax feed-only + Search for brand.
3. Lead gen with CRM stages? Search + offline conversion import, then PMax.
4. Need demand creation? Demand Gen/Video, judged on reach, brand search lift, assisted conv.

---

## 3. Keywords, match types, negatives, search terms

### 3.1 Match types (current behavior; [VERIFY] at https://support.google.com/google-ads/answer/2497836)
- Exact `[kw]`: same meaning/intent as the keyword, includes close variants (misspellings, plurals, reordering with the same meaning, implied words).
- Phrase `"kw"`: query includes the keyword's meaning; additional words before/after allowed; order matters when it changes meaning. Since 2021 phrase absorbed Broad Match Modifier.
- Broad `kw`: reaches related searches, synonyms, other related terms; uses context (other keywords in the ad group, landing page, recent searches). Google says broad pairs best with Smart Bidding (confirmed in Smart Bidding help, 2026-10-02).
- Practitioner stance: exact + phrase for control on brand and proven terms; broad + Smart Bidding + strong negatives + conversion quality for scale; avoid broad with Manual CPC/Max Clicks.
- API: `ad_group_criterion.keyword.text`, `.keyword.match_type` (EXACT/PHRASE/BROAD), `.negative`, `.status`, `.quality_info.*`, `.position_estimates.*` (first_page_cpc_micros, top_of_page_cpc_micros).

### 3.2 Negative keywords
- Levels: ad group negative, campaign negative (`campaign_criterion.negative=true`), shared lists (`shared_set.type=NEGATIVE_KEYWORDS`, limit 20 lists/account [VERIFY], 5,000 keywords per list [VERIFY]), account-level negatives (available since 2024-2025 [VERIFY]), PMax negatives, brand exclusions.
- Negatives do NOT use close variants: add singular/plural/misspellings explicitly; negative broad = all words in any order; negative phrase = words in order; negative exact = exact query only.
- Cross-negation to route traffic (brand vs non-brand) only with care: negating brand in a non-brand campaign works; do not negate in a way that blocks converting queries.
- Always-on negative themes: jobs/careers/free/DIY/torrent/login/support (if not support), competitor names when not wanted, geographic irrelevant locations, wrong product lines.
- Mistake: adding negatives that cut converting queries (check `search_term_view` conversions before negating). Falsification check: after adding, does impression volume of the intended cluster remain flat 14 days later?

### 3.3 Search terms analysis
- Resources: `search_term_view` (Search/Shopping; terms with enough volume to be reported — privacy thresholds hide low-volume terms; [VERIFY] share of spend hidden), `campaign_search_term_view` (PMax), `search_term_insight`/`campaign_search_term_insight` (category labels), `segments.search_term_match_type`, `search_term_view.status` (ADDED, EXCLUDED, ADDED_EXCLUDED, NONE).
```
SELECT search_term_view.search_term, search_term_view.status,
       segments.search_term_match_type, campaign.name, ad_group.name,
       metrics.impressions, metrics.clicks, metrics.cost_micros,
       metrics.conversions, metrics.conversions_value, metrics.ctr, metrics.average_cpc
FROM search_term_view
WHERE segments.date DURING LAST_30_DAYS AND metrics.cost_micros > 0
ORDER BY metrics.cost_micros DESC LIMIT 500
```
- Report cut: (a) spend with 0 conv above 2–3x target CPA (heuristic) = negative candidate; (b) converting terms not in keyword list = add as exact/phrase; (c) share of spend on terms not matching any keyword intent; (d) brand-leak (brand term in non-brand campaign); (e) competitor term performance.
- Significance rule (heuristic): decide to negate only if clicks ≥ (1/historical CVR) × 2 or cost > 2× target CPA with zero conversions.
- Hidden term share: % of cost on "other search terms" (not in view). If >30–40% of Search spend is hidden, term-level analysis is unreliable -> use word-level n-gram aggregation and category insights.

### 3.4 Keyword-level diagnostics
```
SELECT ad_group_criterion.keyword.text, ad_group_criterion.keyword.match_type,
       ad_group_criterion.quality_info.quality_score,
       ad_group_criterion.quality_info.creative_quality_score,
       ad_group_criterion.quality_info.post_click_quality_score,
       ad_group_criterion.quality_info.search_predicted_ctr,
       ad_group_criterion.approval_status, ad_group_criterion.status,
       ad_group_criterion.system_serving_status,
       ad_group_criterion.effective_cpc_bid_micros,
       ad_group_criterion.position_estimates.first_page_cpc_micros,
       metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions,
       metrics.search_impression_share, metrics.search_rank_lost_impression_share
FROM keyword_view WHERE segments.date DURING LAST_30_DAYS AND ad_group_criterion.status = 'ENABLED'
```
- `system_serving_status` = RARELY_SERVED means "low search volume" (older term) -> consolidate or broaden.
- Duplicates, "Eligible (Limited)" keywords, and paused/rare keywords with zero impressions for 90 days: clean-up hygiene.

### 3.5 Mistakes
- Treating Google match "close variants" as bugs; ignoring that exact now captures same-intent.
- Negative lists applied to wrong campaigns (shared list attachments: `campaign_shared_set`).
- Using Broad with Max Clicks.
- DSA without page feed/URL exclusions -> serving irrelevant pages.
- Relying on Search Terms report for PMax (not equivalent).

---

## 4. Bidding strategies

### 4.1 Overview (`campaign.bidding_strategy_type`)
- Manual CPC (+ enhanced CPC deprecated for Search/Display, EOL 2025 [VERIFY]).
- Maximize Clicks (TARGET_SPEND) — set max CPC cap; use for traffic/data bootstrapping only.
- Maximize Conversions (MAXIMIZE_CONVERSIONS) with optional `target_cpa`; Maximize Conversion Value (MAXIMIZE_CONVERSION_VALUE) with optional `target_roas` [in current versions tCPA/tROAS are optional targets on the max strategies — VERIFY mapping to legacy TARGET_CPA/TARGET_ROAS enums].
- Target CPA / Target ROAS; Target Impression Share (TARGET_IMPRESSION_SHARE: absolute top / top / anywhere; use for brand defense); Maximize Click; tCPM / CPV / vCPM for Video/Display/Demand Gen.
- Portfolio strategies: `bidding_strategy` resource shared across campaigns; useful for pooling data of small campaigns of the same intent.

### 4.2 Data thresholds and learning
- Google guidance (Smart Bidding help): evaluate over ≥30 conversions, ≥50 for tROAS; lengthy windows (a month+). Conversion lag matters: use `customer.conversion_tracking_setting` and conversion action `click_through_lookback_window_days` to size evaluation windows.
- Practitioner learning-period rule: ~1–2 weeks or ~30–50 conversions after any significant change (bid strategy, target change >20%, budget change >20–30%, conversion goal change, big asset/structure change). Do not stack changes; `campaign.primary_status_reasons` includes BIDDING_STRATEGY_LEARNING (status "Learning").
- Rules of thumb: tCPA no more than 20% below historical CPA in one step; tROAS no more than 15–20% above; budget change ≤20% per 7 days for stable accounts. These are heuristics.
- Start target close to actual (current 30-day CPA/ROAS), then tighten by steps. Setting an unrealistic target = throttled delivery, "Limited by bid strategy" notes, drop in impressions.
- Bid strategy status: `campaign.bidding_strategy_system_status` and `bidding_strategy.*` [VERIFY]; primary_status_reasons includes BIDDING_STRATEGY_CONSTRAINED, BID_STRATEGY_MISCONFIGURED, MISSING_CONVERSION_TRACKING.

### 4.3 Budget constraints
- `metrics.search_budget_lost_impression_share` (Search) — fraction of eligible impressions lost to budget. Heuristic: >10–15% on a profitable campaign (CPA < target or ROAS > target) = raise budget or reallocate; if unprofitable, do NOT raise.
- `campaign_budget.has_recommended_budget`, `.recommended_budget_amount_micros`, `.estimated_change_*` — Google's budget recommendations; treat with caution (they push spend up). Accept only when marginal CPA/ROAS is acceptable, and when constrained campaigns are efficient.
- Daily budget semantics: Google can spend up to 2x the daily budget on a day, but never more than 30.4 × daily budget in a month (monthly cap = 30.4 × daily average) [VERIFY]. So pacing checks must compare month-to-date spend vs (days elapsed × daily budget) with ±100% daily tolerance but ≤ monthly cap.
- Shared budgets obscure which campaign eats it: check `campaign_budget.explicitly_shared` and per-campaign spend share.

### 4.4 Diagnostic queries
```
SELECT campaign.id, campaign.name, campaign.bidding_strategy_type,
       campaign.target_cpa.target_cpa_micros, campaign.target_roas.target_roas,
       campaign.maximize_conversions.target_cpa_micros,
       campaign.maximize_conversion_value.target_roas,
       campaign.primary_status, campaign.primary_status_reasons,
       metrics.cost_micros, metrics.conversions, metrics.conversions_value,
       metrics.cost_per_conversion, metrics.search_budget_lost_impression_share,
       metrics.search_rank_lost_impression_share
FROM campaign WHERE segments.date DURING LAST_30_DAYS
```
- Bid change history: `change_event` with `change_resource_type = CAMPAIGN` and field mask containing `bidding_strategy`/`target_cpa` (see 14).
- Simulators: `campaign_simulation` (type BUDGET/TARGET_CPA/TARGET_ROAS), `ad_group_simulation`; points give projected clicks/conversions/cost — use as directional hints only.

### 4.5 Mistakes
- Switching strategy weekly (resets learning).
- tROAS on campaigns with fewer than ~15–30 conversions/month or with no real conversion value (constant values).
- Counting soft/duplicate conversions (each page view as 'purchase' value) so Smart Bidding optimizes the wrong thing.
- Setting target CPA below achievable and then blaming "no impressions".
- Raising budgets on campaigns limited by rank rather than budget.
- Mixing very different products in one tROAS campaign (margin differences) — split by margin tiers.

### 4.6 Manager reasoning
"Is the system constrained by data, budget, or target? Data -> consolidate; budget -> reallocate to winners; target -> loosen by ≤20% and watch impression share recover; none -> look at conversion quality and landing page."

---

## 5. Quality Score and ad strength

### 5.1 Quality Score (QS)
- Keyword-level 1–10 diagnostic (not used in auction directly in the same way; the auction uses real-time quality signals). Components (3 sub-scores each BELOW_AVERAGE / AVERAGE / ABOVE_AVERAGE): expected CTR, ad relevance, landing page experience. API: `ad_group_criterion.quality_info.quality_score` (1–10; null when insufficient data), `.search_predicted_ctr`, `.creative_quality_score` (ad relevance), `.post_click_quality_score` (landing page).
- Heuristic thresholds: weighted by impressions, account average QS < 5 = problem; keywords with QS ≤ 4 and >5% of spend = priority; QS 7+ is healthy. Do not chase QS on low-volume keywords.
- Diagnosing each component:
  - Expected CTR below average -> ad copy not compelling/not matching query; add keyword-specific headlines; use assets (sitelinks, callouts); check position-related CTR (don't compare across positions).
  - Ad relevance below average -> keyword not in ad text; ad group too broad; split ad groups; use RSA pinning sparingly.
  - Landing page below average -> speed, mobile UX, content mismatch, intrusive interstitials, missing trust signals; check Core Web Vitals / PageSpeed (seocli SEO side).
- QS reflects historical exact-match performance for the keyword; changes to ads/pages can take weeks to reflect.

### 5.2 Ad strength (RSA)
- Ratings: POOR, AVERAGE, GOOD, EXCELLENT (`ad_group_ad.ad_strength`; `ad_strength_info`/`policy_summary` for disapprovals). Google guidance: Good or Excellent associated with more conversions at same CPA (Google claims ~12% more conversions for Excellent vs Poor [VERIFY]). Ad strength is advisory, not a ranking factor (Google states it's not an auction input [VERIFY]).
- Drivers: number of unique headlines (≥8–10 diverse, up to 15), descriptions (≥3–4, up to 4 [VERIFY max]), keyword inclusion, diversity (not repeating phrases), few pins.
- Required for PMax: `asset_group.ad_strength` similarly.
- API fields:
```
SELECT ad_group_ad.ad.id, ad_group_ad.ad_strength, ad_group_ad.policy_summary.approval_status,
       ad_group_ad.policy_summary.policy_topic_entries,
       ad_group_ad.ad.responsive_search_ad.headlines, ad_group_ad.ad.responsive_search_ad.descriptions,
       ad_group_ad.ad.final_urls, ad_group.name, campaign.name,
       metrics.impressions, metrics.clicks, metrics.conversions, metrics.cost_micros
FROM ad_group_ad WHERE ad_group_ad.status = 'ENABLED'
```
- Asset-level: `ad_group_ad_asset_view` with `performance_label` (BEST/GOOD/LOW/LEARNING/PENDING), `pinned_field`; use to replace LOW after ~30 days / ≥ a few thousand impressions (heuristic).

### 5.3 Mistakes
- Chasing EXCELLENT by stuffing duplicative headlines (hurts brand voice, legal claims).
- Over-pinning (limits combos; ad strength falls; Google reports pinned assets reduce flexibility).
- 1 RSA per ad group only: recommended ≥1 and typically 2–3 RSAs per ad group for testing (Google allows up to 3 enabled RSAs per ad group [VERIFY]).

---

## 6. RSA best practices and assets

- Headlines: up to 15, ≤30 chars; descriptions up to 4, ≤90 chars; 2 path fields ≤15 chars each. Pin only legal/regulated lines (e.g., disclaimers) and brand name if mandatory; pin to position, with ≥2 options per pinned slot.
- Content mix: keyword-based headline(s) (match query), benefit, proof (numbers, reviews, awards), offer/price, CTA, brand, location, urgency (only when true). Avoid repetition of the same phrase across headlines (Google treats near-duplicates as low diversity).
- Dynamic Keyword Insertion/countdown/location insertion where relevant; careful with compliance.
- Check policy_topic_entries for trademarks, misleading claims, superlatives w/o proof, excessive capitalization/punctuation.
- Assets (formerly extensions): sitelinks (≥4, ideally 6+ with descriptions), callouts (≥4), structured snippets, call, location (Business Profile link), price, promotion, lead form, image assets, business name/logo, app. Assets increase CTR and are part of ad rank (expected impact of assets).
- Asset-level diagnostics: `campaign_asset`, `ad_group_asset`, `customer_asset` with `status`, `source`, `policy_summary`; `asset.type`; `asset.sitelink_asset.link_text`; `asset_field_type_view`; performance by `segments.asset_interaction_target.*`.
- Auto-created assets (dynamic): Google can generate headlines/descriptions from landing page. Agency choice: opt-out in regulated or tone-sensitive clients (`customer.asset_automation_settings` [VERIFY]).
- Testing: use Ads Experiments (`experiment`, `experiment_arm`), ≥2–4 weeks, sufficient conversions; avoid declaring winners before ~95% confidence, ~100 conv per arm (heuristic). With RSAs, test by rotating different strategic messages rather than micro-variations.
- Responsive Display / Demand Gen creatives: multiple aspect ratios (1.91:1, 1:1, 4:5, 9:16), logo (1:1 and 4:1), video ≥ 1 in YT; avoid text-heavy images.
- Landing page match: ad message and H1/offer should be consistent. Message match influences post-click experience and conversion rate.

---

## 7. Audiences and signals

- Types (`user_list`, `audience`, `custom_audience`, `combined_audience`, `ad_group_criterion` type USER_LIST/USER_INTEREST/CUSTOM_AUDIENCE):
  - Your data: website visitors (remarketing), app users, customer match (email/phone upload — consent required; EU hashing), YouTube viewers.
  - Google audiences: affinity, in-market, life events, detailed demographics.
  - Custom segments: keywords, URLs, apps — for Demand Gen/Video/Display.
  - Similar/lookalike: Demand Gen lookalike segments (seeds >~1,000 users [VERIFY]).
- Targeting vs observation: on Search/Shopping, apply audiences in Observation (bid adjustments/insights) unless you intentionally restrict; in Display/Video/Demand Gen, Targeting.
- PMax: audience signals are suggestions, not constraints; Google may expand beyond. Provide customer lists, remarketing, custom segments (search terms of converting queries; competitor URLs).
- Minimum list sizes: Search remarketing lists need ≥1,000 active users (Search) / ≥100 for Display (Google doc [VERIFY]); customer match ≥1,000 matched.
- EU: customer match and remarketing require consent (Google EU user consent policy). Consent Mode v2 flags `ad_user_data`, `ad_personalization` required to populate remarketing audiences in EEA/UK.
- Diagnostics:
```
SELECT ad_group_criterion.criterion_id, ad_group_criterion.user_list.user_list,
       ad_group_criterion.bid_modifier, ad_group_criterion.negative,
       campaign.name, metrics.impressions, metrics.conversions
FROM ad_group_audience_view
SELECT user_list.id, user_list.name, user_list.size_for_search, user_list.size_for_display,
       user_list.membership_status, user_list.membership_life_span, user_list.eligible_for_search
FROM user_list
```
- Mistakes: audiences applied as Targeting on Search shrinks reach to near zero; using All visitors only; excluding converters not applied; list membership too long (stale) or too short (<1,000); sensitive categories prohibited (health, personal hardships in personalised advertising policy).

---

## 8. Conversion tracking and measurement

### 8.1 Tag architecture
- Google tag (gtag.js) + Google Tag Manager; conversion linker; conversion actions in Ads with `conversion_action.type` (WEBPAGE, UPLOAD_CLICKS, UPLOAD_CALLS, CLICK_TO_CALL, GOOGLE_PLAY_*, GA4_*, WEBSITE_CALL, etc.), `status`, `primary_for_goal`, `category`, `value_settings` (default value, always_use_default_value), `counting_type` (ONE_PER_CLICK for leads, MANY_PER_CLICK for purchases), attribution model, lookback windows.
- Primary vs secondary: only PRIMARY (`primary_for_goal=true`) conversion actions feed bidding & "Conversions" column; secondary appear in "All conversions". Common error: micro-conversions (page_view, scroll, add_to_cart) set as primary.
- GA4 imported conversions vs native Ads tag: avoid double-counting (same event twice). Check `conversion_action.origin`/type. Prefer one source of truth for bidding; compare against back-end orders (delta ±10–15% acceptable heuristic).

### 8.2 Enhanced conversions
- Enhanced conversions for web: hashed first-party data (email, phone, name, address) sent with the conversion tag to improve match; recovers conversions lost to cookie limits. For leads: Enhanced conversions for leads (hash lead email/phone at submission, upload with gclid/ or email later). API: `customer.conversion_tracking_setting.enhanced_conversions_for_leads_enabled`, `accepted_customer_data_terms`.
- Diagnostics: Diagnostics tab -> `conversion_action` status (e.g., NO_RECENT_CONVERSIONS, UNVERIFIED, MISCONFIGURED, RECORDING_UNKNOWN [VERIFY enum: `conversion_action.tag_snippets`, `ConversionActionStatus`]); in API there is `conversion_action.status` (ENABLED/REMOVED/HIDDEN) and tag health is shown through UI diagnostics; closest API proxy: zero conversions in 14 days for a previously-active action.

### 8.3 Consent Mode v2 (EEA/UK)
- Required since March 2024 for audience features and conversion modeling in EEA/UK (Google EU user consent policy): signals `ad_storage`, `analytics_storage`, `ad_user_data`, `ad_personalization`. Implement via a Google-certified CMP (TCF v2.2/2.3). Basic (no tags fire before consent) vs Advanced (tags fire with cookieless pings; enables conversion modeling).
- Without consent mode: loss of remarketing lists and reduced conversion measurement in EU; modeled conversions need thresholds (e.g., ≥700 ad clicks per domain and country over 7 days for 7 consecutive days [VERIFY]).
- Red flags: sudden conversion drop ~Mar 2024+ in EU accounts; remarketing lists not growing; "consent mode status" warnings in Ads/Tag Assistant; consent banner loading after tags.
- seocli can only infer from API: trend in `metrics.conversions` vs `metrics.all_conversions`, `metrics.conversions_from_interactions_value_per_interaction`; `metrics.biddable_app_post_install_conversions`; modeled share not directly exposed in API [VERIFY]. Use "ask user to confirm CMP + Advanced mode" as checklist item.

### 8.4 Offline conversion import (OCI)
- For B2B/lead gen: upload gclid-matched conversions with value (qualified lead, sale) via `ConversionUploadService.UploadClickConversions` (gclid, gbraid, wbraid, or hashed email/phone via enhanced conversions for leads). Conversion window: up to 90 days from click. Import within 24–48h target; delays reduce attribution accuracy.
- Provide value (probability-weighted by stage) so tROAS can optimize to pipeline revenue; rules: ≥30 uploads/month heuristic.
- Diagnostics: `offline_conversion_upload_client_summary`, `offline_conversion_upload_conversion_action_summary` (success rates, alerts like TOO_RECENT_EVENT, EVENT_NOT_PERMITTED_FOR_CUSTOMER, GCLID_DATE_TIME_PAIR_IS_NOT_UNIQUE).
- Common errors: gclid not captured in CRM; timezone format; duplicate order_id; uploading before click time.

### 8.5 Attribution
- Models: data-driven (DDA, default; requires adequate data; since 2023 last-click, first-click, linear, time decay, position-based deprecated for most accounts; DDA is the only option for new conv actions [VERIFY]), last-click for legacy. API: `conversion_action.attribution_model_settings.attribution_model`, `.data_driven_model_status`.
- Lookback windows: click-through 30 days default (1–90), view-through 1–30 days (default 1), engaged-view 3 days. Consider longer for considered purchases.
- Interpretation: cross-channel reporting differs from GA4 (GA4 DDA across channels vs Ads DDA across Google Ads touchpoints); do not expect equality. Use blended MER (marketing efficiency ratio) at business level.
- Conversion lag: `segments.conversion_lag_bucket`; conversion-delay causes underreported recent days; avoid reading last 3–7 days for CPA-driven decisions in long-cycle products (use `metrics.conversions_by_conversion_date` and `metrics.cost_per_conversion` with date segmentation `segments.conversion_action`).
- Value rules: for lead gen, set conversion value rules or use offline import; for ecommerce, pass transaction_id and dynamic value, currency; check `conversion_action.value_settings.default_value` vs dynamic; tROAS with constant value = meaningless.

### 8.6 Measurement audit queries
```
SELECT conversion_action.id, conversion_action.name, conversion_action.type,
       conversion_action.status, conversion_action.category, conversion_action.primary_for_goal,
       conversion_action.counting_type, conversion_action.attribution_model_settings.attribution_model,
       conversion_action.click_through_lookback_window_days,
       conversion_action.view_through_lookback_window_days,
       conversion_action.value_settings.default_value,
       conversion_action.value_settings.always_use_default_value,
       metrics.all_conversions, metrics.conversions, metrics.all_conversions_value
FROM conversion_action WHERE segments.date DURING LAST_30_DAYS
SELECT customer.conversion_tracking_setting.conversion_tracking_id,
       customer.conversion_tracking_setting.cross_account_conversion_tracking_id,
       customer.conversion_tracking_setting.accepted_customer_data_terms,
       customer.conversion_tracking_setting.enhanced_conversions_for_leads_enabled,
       customer.conversion_tracking_setting.google_ads_conversion_customer
FROM customer
SELECT conversion_goal_campaign_config.campaign, conversion_goal_campaign_config.goal_config_level,
       conversion_goal_campaign_config.custom_conversion_goal FROM conversion_goal_campaign_config
SELECT customer_conversion_goal.category, customer_conversion_goal.origin,
       customer_conversion_goal.biddable FROM customer_conversion_goal
```
### 8.7 Red flags
- >3 primary conversion actions with mixed intent; "Conversions" ~ clicks (page-view type); CVR >30% on Search (double counting) or <0.5% (broken); all_conversions >> conversions (many secondary); no value on ecommerce; conversion action unchanged since site migration; call conversions without duration threshold (default 60s); import of GA4 and native Ads tag simultaneously.

---

## 9. Merchant Center: feed quality, disapprovals, price benchmarks

### 9.1 Feed essentials (product data specification, https://support.google.com/merchants/answer/7052112 [VERIFY])
- Required: id, title (≤150 chars; front-load brand/type/key attributes), description (≤5,000), link, image_link (≥100×100 non-apparel, ≥250×250 apparel; recommend ≥800×800 [VERIFY]), availability, price, condition (used/refurbished/new), brand, GTIN (for products with manufacturer GTIN; strongly improves matching, benchmarks), MPN, identifier_exists, google_product_category, shipping and tax (country-dependent; EU: tax included; shipping required in many countries, can be set at account level), item_group_id for variants, color/size/gender/age_group for apparel, product_type, custom_label_0–4 for segmentation (margin, seasonality, bestseller).
- Data sources: primary feed, supplemental feeds, content API / Merchant API (Content API sunset 2026-08-18 -> Merchant API [VERIFY]); automated feeds (crawl structured data - needs schema.org Product markup consistent with the page).
- Quality principles: titles = how people search (brand + model + attribute); price/availability identical in feed and landing page (mismatch -> disapproval "Mismatched value (page crawl)"); images without watermarks/promo text; unique descriptions; correct categories; avoid all-caps; sale_price with sale_price_effective_date.
- Free listings: `shopping_ads` and `free listings` surfaces separate; use destination control (excluded_destination).

### 9.2 Disapprovals and account issues
- Item-level issue types: Missing value, Invalid value, Mismatched value (feed vs page) — price, availability, image; Landing page not working (404/redirect/robots); Policy: prohibited products, misrepresentation, adult, restricted (alcohol, gambling, healthcare), counterfeit, unsupported claims, promotional overlays on images.
- Account-level: Misrepresentation, missing return policy/contact, missing business info, website verification and claim, shipping policy, "Suspicious payment" etc. Suspensions block all products.
- Metrics to expose: count of products by status (approved/pending/disapproved/demoted), top issues by impacted product count, percent approved. Heuristic red flags: disapproval rate >5% of catalog; >0 'critical' account issues; availability out-of-stock products still advertised (wasted spend).
- API surfaces:
  - Merchant API (`products`, `productstatuses` / `accounts.issues`): `itemLevelIssues` (code, severity, resolution, attributeName, destination, servability).
  - Google Ads: `shopping_product` resource (`shopping_product.status`, `.issues`, `.availability`, `.price_micros`, `.brand`, `.item_id`, `.title`, `.resource_name`, and metrics), `product_group_view`, `shopping_performance_view`, `shopping_product.eligible_destinations`; `campaign.shopping_setting.merchant_id`/`.feed_label`/`.campaign_priority`.
```
SELECT shopping_product.item_id, shopping_product.title, shopping_product.status,
       shopping_product.issues, shopping_product.availability, shopping_product.brand,
       shopping_product.price_micros, shopping_product.currency_code,
       metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions
FROM shopping_product WHERE segments.date DURING LAST_30_DAYS
```
- Diagnose wasted spend on products: high cost, 0 conv, price above benchmark, low review count, poor image; zero-impression approved products (title/category weak); items with high clicks but out-of-stock.

### 9.3 Price competitiveness and benchmarks (fetched 2026-10-02 from support.google.com/merchants/answer/9626903)
- Pricing insights: Benchmark Price (average price that typically leads to more successful auctions for the GTIN-matched product across retailers), Price Gap (% difference between your price and benchmark), distribution (higher / similar / lower than benchmark), predicted click & conversion uplift from suggested prices (sale price suggestions require purchase conversions reporting).
- Requirements: valid GTINs; suggestions appear only for products with significant predicted improvement; data is restricted to internal retailer use (cannot be resold/publicly displayed/aggregated across businesses) — important for seocli: do not expose benchmark data in cross-client aggregated reports and do not redistribute publicly.
- Reports: Price competitiveness report (Merchant Center: Growth > Pricing insights) and BigQuery data transfer tables `BestSellers`, `PriceBenchmarks`, `PriceInsights`; Merchant API `reports` service: `price_competitiveness_product_view` with `price`, `benchmark_price`, `report_country_code`, `product_type`; `price_insights_product_view` with `suggested_price`, `predicted_impressions_change_fraction`, `predicted_clicks_change_fraction`, `predicted_conversions_change_fraction`, `effectiveness` [VERIFY names against Merchant API reports reference].
- Decision logic for a campaign manager: 
  - Price gap > +10% above benchmark with clicks but low CVR -> price is likely culprit; test price/promo or reduce bids/exclude product from growth campaigns.
  - Price gap ≤ -10% below benchmark -> margin leak; test raising price; push into hero campaign (high priority).
  - Within ±5% -> competitive; invest in title/image/reviews.
  - Check shipping cost and delivery speed (Shopping consumers compare total price); merchant promotions (`promotion` feed), merchant star ratings/Google Customer Reviews, return policy.
- Seasonality: benchmark updates ~weekly-ish [VERIFY]; evaluate trends, not single points.
- Combine with Ads: join price gap by item_id with `shopping_product` metrics (cost, clicks, conversions, ROAS) -> table: spend share on products priced above benchmark; potential savings; products with best "benchmark - price" and ROAS to scale.

### 9.4 Mistakes
- Feed price != page price (sale price not synced); stale availability; GTIN missing for branded items -> no benchmark and lower matching.
- Using one feed for all countries without feed labels / shipping per country.
- All products in a single product group (no margin or performance segmentation).
- Ignoring supplemental feed for titles and custom labels (rewrite titles with rules, not manual edits).
- PMax with feed that includes low-margin/out-of-season items.

---

## 10. Policies and disapprovals (Ads)

- Policy hub: Google Ads policies (prohibited content/practices, prohibited and restricted content, editorial and technical requirements, restricted financial/health/legal/gambling/alcohol, personalized advertising (sensitive categories), political ads (EU TTPA: political ads banned in EU from Oct 2025 [VERIFY]), misrepresentation, destination requirements).
- States: `ad_group_ad.policy_summary.approval_status` (APPROVED, APPROVED_LIMITED, DISAPPROVED, AREA_OF_INTEREST_ONLY); `policy_topic_entries.type` (PROHIBITED, LIMITED, FULLY_LIMITED, DESCRIPTIVE, BROADENING, AREA_OF_INTEREST_ONLY), `topic`, `evidences`, `constraints`.
- Disapproval workflows: fix, request review (UI or API `PolicyValidationParameter` with exemption requests, not via `GoogleAdsService` alone), appeal; repeated violations -> account suspension (circumventing systems, unacceptable business practices).
- Common: trademarks in ad text, destination not working, destination mismatch, misleading claims, healthcare restricted, clickbait, "Free" claims, inappropriate capitalization, unreliable claims.
- Asset-level policy: `ad_group_asset.policy_summary`, `asset.policy_summary`.
- Account-level: payment status (`customer.status`, billing_setup, `account_budget`), suspended accounts (`customer.status=SUSPENDED`), advertiser verification, Business Info verification.
```
SELECT ad_group_ad.ad.id, ad_group_ad.policy_summary.approval_status,
       ad_group_ad.policy_summary.review_status,
       ad_group_ad.policy_summary.policy_topic_entries,
       campaign.name, ad_group.name FROM ad_group_ad
WHERE ad_group_ad.policy_summary.approval_status IN ('DISAPPROVED','APPROVED_LIMITED','AREA_OF_INTEREST_ONLY')
```
- Red flags: >5% of ads disapproved; final URLs with 4xx/5xx/redirect chains (check with seocli crawl); limited ads in regulated verticals; disapproved sitelinks reducing assets.

---

## 11. Budget pacing

- Metrics: month-to-date cost vs monthly budget target (client-agreed, not Google's). Linear target: spend_expected = monthly_budget × day_of_month / days_in_month. Pace ratio = actual / expected. Heuristic bands: 0.9–1.1 healthy; <0.8 underpacing (investigate rank/budget/targets/learning/approvals); >1.15 overpacing (check daily budget × 30.4 cap, shared budgets, seasonality).
- Google rules: daily budget can be exceeded up to 2×; monthly charge capped at 30.4 × daily budget [VERIFY]. Underspend on days is "carried" — avg daily budget model.
- Forecast: end-of-month projection = MTD + (avg last 7 days spend × remaining days); adjust for day-of-week.
- Reallocation logic: reduce budgets on campaigns with CPA > 1.3× target or ROAS < 0.7× target for ≥14 days with enough data; increase where IS lost to budget >10% and profitable.
- GAQL: `SELECT segments.date, campaign.id, campaign.name, campaign_budget.amount_micros, metrics.cost_micros FROM campaign WHERE segments.date DURING THIS_MONTH`; for billing: `invoice` (monthly invoice via `InvoiceService`), `account_budget`, `billing_setup`.
- Budget "limited" status flag: `campaign.primary_status_reasons` contains BUDGET_CONSTRAINED [VERIFY].
- Common mistakes: shared budgets starving the strongest campaign; mid-month budget slashes resetting learning; ignoring weekday patterns (B2B drops on weekends); not accounting for conversion delay.

---

## 12. Account audit checklist (thresholds and red flags)

Legend: [G] Google rule, [H] heuristic. Each item: Check -> GAQL/field -> Red flag -> Fix.

### 12.1 Account health and access
1. Status & billing: `customer.status` ENABLED; billing set (`billing_setup`); no payment hold. Red: SUSPENDED / payment failure.
2. Account labels, timezone/currency vs business. Red: timezone mismatch with CRM (affects reporting).
3. Users/access: remove ex-agency users (`customer_user_access`). [H]
4. Auto-apply recommendations: `recommendation_subscription` (type, status). Red: auto-apply enabled for budget increases, broad match, keyword additions, assets without review.
5. Conversion tracking active; see 8.

### 12.2 Structure
6. Campaigns count and size: share of campaigns with <30 conv/30d [H]. Red: >50% of spend in campaigns with <15 conv/30d on Smart Bidding.
7. Brand vs non-brand split; PMax brand overlap. Red: brand in generic campaign; blended CPA in one campaign.
8. Geo settings presence; language; ad schedule; device. Red: "presence or interest" for local business; no language match.
9. Network settings. Red: Search partners/Display expansion on with unfavorable segment performance (CPA 1.5× search).
10. Naming and labelling. [H]

### 12.3 Keywords and search terms
11. Wasted spend: share of cost on search terms with 0 conv and cost > 2× target CPA. Red: >15–20% of spend [H].
12. Negative coverage: shared negative lists exist; >0 account negatives; cross-campaign negative routing for brand. Red: zero negatives.
13. Match type mix: broad share of spend with Manual bidding. Red: >30% broad on Manual CPC/Max clicks.
14. Duplicate keywords; zero-impression keywords for 90d (>30% of keywords). Clean up.
15. Hidden search-term share. Red: >40% of search spend in "other".
16. Brand terms: brand CPC low, IS>90% (brand IS: `search_impression_share` > 0.9) if defended; competitor hijacking. See 16.

### 12.4 Ads and assets
17. RSAs per ad group ≥1 (ideally 2–3 active); ad strength: share of POOR ≤ 10% [H]; all ad groups with ≥1 GOOD+ ad.
18. Headline diversity; pinning count. Red: >3 pinned positions in one RSA.
19. Asset coverage: sitelinks ≥4, callouts ≥4, structured snippets ≥1, call (if phone), image assets; check at account/campaign level.
20. Policy status; disapproved/limited ads; broken landing pages.
21. Ad rotation: for RSAs only "Optimize" is available; not "rotate indefinitely."

### 12.5 Bidding and budget
22. Strategy vs data: tCPA/Max conv campaigns with <30 conv/30d; tROAS with <50 [G ref: Smart Bidding help].
23. Targets vs actuals: target CPA below actual CPA by >20% sustained for 2+ weeks -> throttling; targets above actual by >30% -> leaving efficiency on table.
24. Budget constrained & profitable: lost IS (budget) >10% with CPA < target. Action: raise.
25. Learning status: campaigns in "Learning" for >2 weeks; frequent changes (>2 significant changes in 14 days from `change_event`).
26. Pacing (11).

### 12.6 Quality
27. Account QS (impression-weighted): <6 red [H]; keywords with QS ≤4 representing >20% of spend.
28. CTR benchmarks by type [H]: Search brand 15–40%+, non-brand 3–8%, Shopping 0.5–1.5%, Display 0.3–0.7%, YouTube view rate 15–30%; compare against account's own history first; industry benchmarks vary widely.
29. Landing page experience below average share; page speed (seocli).
30. Impression share: non-brand `search_rank_lost_impression_share` >50% on priority campaigns = relevance/bid/quality issue.

### 12.7 Audiences
31. Remarketing lists size; customer match uploaded; exclusions of converters; consent in EU.
32. Observations applied on Search; no accidental targeting restricting reach.

### 12.8 PMax and Shopping
33. Asset groups: assets rated LOW >30% -> replace; each asset group ≥ 1 video; ≥3 images per ratio; text assets at max count.
34. Search themes and brand exclusion; URL expansion status; final URL exclusions.
35. Feed approval rate ≥95% [H]; GTIN coverage; custom labels in use; price vs benchmark; out-of-stock waste.
36. PMax/Shopping/Search overlap on brand; check `campaign_search_term_insight` categories for brand vs non-brand.

### 12.9 Measurement
37. Primary conversions mapped to business goals; values; transaction IDs; dedup. Enhanced conversions on; consent mode v2 in EEA; OCI for leads; DDA model.
38. GA4 link and auto-tagging (`customer.auto_tagging_enabled` must be true); `customer.tracking_url_template`, `final_url_suffix`.
39. Data discrepancy: Ads conversions vs backend orders within ±15% [H].

### 12.10 Settings and compliance
40. Conversion lookback windows aligned with sales cycle; ad schedule; device bid adjustments (where applicable).
41. Policy topics; restricted category compliance; EU political ads; Customer data policies; sensitive audience.
42. Experiments running or concluded (`experiment`).
43. Change history sanity: who changed what (14). Red: many changes by unknown users/auto-apply.

### 12.11 Scoring approach
- Weight by money at stake: finding priority = estimated wasted/foregone monthly spend × confidence. Output top 5 issues with quantified impact (e.g., "€2,300/month spend on 0-conversion terms"). Provide falsification: "If after negatives CPA doesn't drop within 14 days, hypothesis wrong; check LP."

---

## 13. Optimization score and recommendations

### 13.1 What it is (Google help, fetched 2026-10-02)
- 0–100% estimate of how well the account is set to perform; computed in real time from account stats, campaign settings, available recommendations, recent recommendation history, and ads ecosystem trends; tailored to objectives inferred mainly from the bidding strategy (Max conversions / tCPA / tROAS). Applying or dismissing a recommendation changes the score. It is not Quality Score and is not a performance metric. Source: https://support.google.com/google-ads/answer/9061546.
- API: `campaign.optimization_score` (0–1), `customer.optimization_score`, `customer.optimization_score_weight`, `metrics.optimization_score_uplift`, `metrics.optimization_score_url`; `recommendation` resource: `type`, `impact.base_metrics`, `impact.potential_metrics`, `campaign`, `ad_group`, `dismissed`, plus typed payloads (e.g., `campaign_budget_recommendation`, `keyword_recommendation`, `target_cpa_opt_in_recommendation`). Methods: `RecommendationService.ApplyRecommendation`, `DismissRecommendation`. `recommendation_subscription` for auto-apply.
```
SELECT recommendation.type, recommendation.campaign, recommendation.impact.base_metrics.impressions,
       recommendation.impact.potential_metrics.impressions,
       recommendation.impact.base_metrics.conversions, recommendation.impact.potential_metrics.conversions,
       recommendation.dismissed FROM recommendation
SELECT campaign.id, campaign.name, campaign.optimization_score FROM campaign WHERE campaign.status='ENABLED'
```
### 13.2 Agency stance
- Score is a hygiene indicator, not a goal. 100% optimization score with a poor account is common; 60% with excellent ROAS is fine. Never chase score; never report it as a KPI to clients.
- Triage table (type names per `RecommendationType` enum [VERIFY]):

| Recommendation | Default | Rationale |
|---|---|---|
| Fix conversion tracking / enhanced conversions / consent mode | ACCEPT | Foundational |
| Fix disapproved ads/feed/policy; repair broken URLs | ACCEPT | Direct loss |
| Add/improve RSA assets, sitelinks, callouts, images | ACCEPT after review | Cheap, usually beneficial; check brand/legal |
| Add negative keywords suggestions (if offered) | REVIEW then ACCEPT | Verify no converting terms |
| Switch to Maximize Conversions/Conversion value or tCPA/tROAS | ACCEPT only if ≥30/50 conv, tracking validated | Else REJECT |
| Raise target CPA / lower tROAS | REVIEW | Google pushes volume over efficiency |
| Raise budgets | ACCEPT only if profitable & budget-limited | Otherwise REJECT |
| Add keywords (broad) / "Use broad match" | REVIEW; accept for Smart Bidding + good negatives | Reject with manual bidding |
| Add new keywords from search terms | REVIEW (exact/phrase) | Only if converting |
| Search Partners/Display expansion on | REJECT by default | Test with segments |
| Auto-apply recommendations | REJECT (disable) | Loss of control |
| Upgrade to PMax / "Create PMax" | REJECT unless planned | Cannibalization |
| Remove redundant/low-performing keywords | REVIEW; usually REJECT (harmless) |  |
| Responsive Display ads / dynamic search ads additions | REVIEW | Quality varies |
| Add audience/customer match/remarketing | ACCEPT if consent & lists ready |  |
| Improve ad strength (generate assets) | REVIEW generated text for accuracy/legal |  |
| Move budget between campaigns | REVIEW | Often aligns with goals |
| Create new campaign types, e.g. Demand Gen, App | REJECT unless strategy |  |
| Bid on competitor/brand/AI Max | EXPERIMENT only |  |

- Rule: accept if (a) it fixes a defect, (b) it is reversible and cheap, or (c) the expected metric has a clear leading indicator and acceptance maps to a documented goal; reject if it increases spend without efficiency evidence.
- Use `impact.potential_metrics - base_metrics` as Google's estimate; discount by 50% (heuristic) and compute incremental cost per conversion before accepting spend-raising recommendations.
- Dismiss with reason in change log; schedule review monthly.

---

## 14. Change history and attribution of performance shifts

- `change_event` (last 30 days only, high detail): `change_date_time`, `change_resource_type`, `change_resource_name`, `user_email`, `client_type` (GOOGLE_ADS_WEB_CLIENT, GOOGLE_ADS_API, GOOGLE_ADS_AUTOMATED_RULE, GOOGLE_ADS_RECOMMENDATIONS, GOOGLE_ADS_RECOMMENDATIONS_SUBSCRIPTION, GOOGLE_ADS_SCRIPTS etc.), `resource_change_operation`, `changed_fields` (field mask), `old_resource`, `new_resource`. Query constraints: must filter by `change_event.change_date_time` range (≤30 days) and `LIMIT ≤ 10000`.
```
SELECT change_event.change_date_time, change_event.change_resource_type, change_event.user_email,
       change_event.client_type, change_event.resource_change_operation,
       change_event.changed_fields, change_event.campaign, change_event.ad_group,
       change_event.old_resource, change_event.new_resource
FROM change_event WHERE change_event.change_date_time >= '2026-09-01' AND change_event.change_date_time <= '2026-10-01'
ORDER BY change_event.change_date_time DESC LIMIT 1000
```
- `change_status` (resource-level, shorter detail, 90 days) for sync use.
- Use: explain performance drops: correlate cost/conv anomalies with changes in bid strategy, budget, targets, conversion actions, URLs, negative lists, auto-applied recommendations (client_type GOOGLE_ADS_RECOMMENDATIONS*). Red flag: >3 strategic changes in 14 days on one campaign; changes by `GOOGLE_ADS_RECOMMENDATIONS_SUBSCRIPTION`.
- Detect anomalies: week-over-week change thresholds [H]: cost ±30%, conv ±30%, CPA ±25%, CTR ±25%, IS ±15 pts; compare vs same weekday last 4 weeks; mind seasonality (Think with Google trend data; Google Trends).

---

## 15. Reporting KPIs for agencies

### 15.1 Metric dictionary (API names)
- Volume: `metrics.impressions`, `.clicks`, `.cost_micros` (÷1,000,000), `.conversions`, `.all_conversions`, `.conversions_value`, `.all_conversions_value`, `.view_through_conversions`.
- Efficiency: `.ctr`, `.average_cpc`, `.average_cpm`, `.conversions_from_interactions_rate`, `.cost_per_conversion`, `.cost_per_all_conversions`, `.value_per_conversion`, ROAS = conversions_value / (cost_micros/1e6); POAS/MER need back-end margin.
- Competitiveness: `.search_impression_share`, `.search_budget_lost_impression_share`, `.search_rank_lost_impression_share`, `.search_top_impression_share`, `.search_absolute_top_impression_share`, `.search_exact_match_impression_share`, `.absolute_top_impression_percentage`, `.top_impression_percentage`; Shopping: `.content_impression_share`, `.content_budget_lost_impression_share`, `.content_rank_lost_impression_share`; benchmark: `.benchmark_ctr`, `.benchmark_average_max_cpc` [VERIFY exist].
- New customers (PMax/Search with new customer acquisition goal): `metrics.new_customer_lifecycle_*`? use `customer_lifecycle_goal` settings and `segments.new_versus_returning_customers` [VERIFY].
- Lead quality: offline-imported conversions segmented by `segments.conversion_action`; lead-to-sale rate from CRM.
- Segments: `segments.date`, `.day_of_week`, `.hour`, `.device`, `.ad_network_type`, `.click_type`, `.slot`, `.conversion_action`, `.geo_target_*` via `geographic_view`/`user_location_view`, `.age_range`/`gender` via `age_range_view`, `gender_view`, `income_range_view`, `parental_status_view`.
- Constraints: some segments cannot be combined (e.g., `conversion_action` with some metrics); metrics availability by resource (see segmentation doc).

### 15.2 Report structure (monthly)
1. Executive summary: spend, conversions/value, CPA/ROAS vs target, MoM and YoY; 3 insights, 3 actions.
2. Efficiency by campaign type and brand/non-brand; separate brand from non-brand always.
3. Funnel: impressions -> clicks -> sessions (GA4) -> conversions -> revenue/pipeline.
4. Share of voice: IS lost to budget/rank by priority campaigns.
5. Search term insights, negative actions, new keywords.
6. Creative/asset insights.
7. Shopping/PMax: product-level winners/losers, price benchmark, feed issues.
8. Tests: hypothesis, results, decision.
9. Next-month plan with leading indicators and falsification checks.
- Avoid vanity metrics (impressions, CTR alone, optimization score). Include incremental/assisted view for upper-funnel.
- Client targets: agree on primary KPI (CPA, ROAS, MER, new customers), budget, and thresholds in advance; report variance to target not just trend.

### 15.3 Efficiency math
- Break-even ROAS = 1 / gross margin; target ROAS = break-even / (1 - desired profit share) etc. Break-even CPA = AOV × margin (or LTV-based).
- Incremental ROAS vs average ROAS: marginal returns decline as spend grows; use simulators/experiments.

---

## 16. SEO–SEA synergy

- Paid/organic overlap: Google Ads "Paid & organic" report requires Search Console link (UI-only report; API: not directly exposed — join GSC query data by seocli's own Search Console connection with Ads `search_term_view`). Join keys: normalized query.
- Decision matrix (per query cluster):
  | Organic position | Brand? | Action |
  |---|---|---|
  | 1–2 with rich SERP (sitelinks), brand | Brand | Ads optional: defend only if competitors bid on brand (check Auction Insights `auction_insight`? API: `metrics.auction_insight_*` limited; use `segments` not available — use UI export [VERIFY]); else save budget |
  | 1–3 non-brand | Non-brand | Test incremental: pause ads on subset (geo or time holdout) to measure total clicks; typical incrementality varies; do not assume |
  | 4–10 or absent | any | Keep/increase ads; ads cover gap while SEO builds |
  | Low CVR on ads, high organic CVR | any | Reuse organic LP/message on ads |
- Brand bidding: benefits — control of message, protects against competitor conquesting, captures SERP real estate, trademark/policy leverage, brand traffic CPC is low; cost — may cannibalize organic clicks with little incremental. Evidence: eBay study (2012, Blake/Nosko/Tadelis) found little incremental from brand search for well-known brand; but competitor presence changes outcome; use geo/time holdouts. Keep brand in its own campaign with Target Impression Share (top, ≥80–95%) and low budget; monitor CPC and IS lost.
- Keyword mining: ads search terms with high CVR -> SEO content targets; GSC queries with impressions but low CTR -> ad copy tests / title tags; ads headline winners -> title tag/meta description tests.
- Landing page synergy: Ads LP experience feeds QS; improve Core Web Vitals, mobile UX; create dedicated pages for high-intent clusters (benefit both).
- Shared audiences: organic visitors to retarget; paid visitors' GA4 audiences for organic content.
- Shopping/Merchant: free listings plus paid; structured data (Product schema, Merchant listing) keep price/availability consistent with feed; price benchmarks inform organic product positioning.
- Negative synergy to avoid: bidding on informational queries that organic dominates and where intent is too early; PMax "URL expansion" sending traffic to unrelated pages.
- Reporting: combined view = paid + organic clicks by cluster; total share of clicks; blended CPA.

---

## 17. MCP exposure: recommended tools and data model for seocli

Read-only first. Suggested tools (capability names, no vendor leak in tool names per project constitution; Google services are exempt for Google-connected accounts):

1. `ads_accounts` — list accessible customers (`customer_client`), status, currency, timezone, test flag.
2. `ads_account_health` — customer status, billing, conversion setup, auto-tagging, auto-apply subscriptions, optimization_score.
3. `ads_campaigns` — campaigns with status/primary_status(+reasons), type, bidding, budget, geo/network settings, IS metrics, cost/conv by period.
4. `ads_search_terms` — search terms with cost/conv, match type, status; filters: min cost, zero-conv; PMax insights separate.
5. `ads_keywords` — keyword quality info, serving status, metrics, position estimates.
6. `ads_ads_and_assets` — RSA ad strength, policy, asset performance labels, sitelinks etc.
7. `ads_conversions` — conversion_action config + metrics, goals config, offline upload summaries.
8. `ads_audiences` — user_list sizes/status, targeting vs observation.
9. `ads_recommendations` — recommendation resources with impact, subscriptions; classification per 13.2.
10. `ads_change_history` — change_event with filters; anomalies correlation.
11. `ads_budget_pacing` — MTD vs target; forecast.
12. `ads_pmax` — asset groups, performance labels, placements, search insights, signals.
13. `ads_audit` — runs checklist 12, returns scored findings with evidence and falsification checks.
14. `merchant_products` — status counts, issues, GTIN coverage, availability; join with Ads `shopping_product`.
15. `merchant_price_benchmark` — price vs benchmark per product (internal use only), joined to Ads cost/ROAS.
16. `seo_sea_overlap` — joins GSC queries and Ads search terms.

Engineering notes:
- API: use GoogleAdsService.SearchStream for large reports; GAQL limits: no JOIN (implicit via resource attribute selection); `ORDER BY`, `LIMIT`, `PARAMETERS include_drafts`; date ranges via `segments.date BETWEEN` or `DURING`; cost in micros; paginate; developer token level (Explorer/Basic/Standard) governs quota — project memory says Explorer access obtained, Basic after brand verification: Explorer has low daily operation limits [VERIFY: Explorer ~2,880 ops/day?]; batch queries and cache.
- Use manager (login-customer-id header) for MCC access; per-client OAuth with `https://www.googleapis.com/auth/adwords` scope (read-only access not separate: scope is full adwords; enforce read-only in seocli code) — note security implication; GAQL queries only, no mutate operations unless explicitly approved.
- Version churn: Ads API releases a new major version roughly every ~2–3 months and sunsets old ones ~12 months later; pin version and run field validation in CI (`GoogleAdsFieldService`).
- Data freshness: metrics up to ~3 hours delay; conversions can be adjusted for 30–90 days; do not compare partial days.
- Privacy: search_term_view thresholds; EU data residency per constitution VII (private client data like Ads account data must not go to non-EU models) — Ads account data is private customer data; keep analysis deterministic server-side, return structured facts to Claude (client LLM) rather than sending to other models.
- Pricing/benchmark data licensing: internal use only (see 9.3).

---

## 18. Campaign manager persona: reasoning scripts

1. Start with goal & unit economics: margin, AOV/LTV, lead value, target CPA/ROAS, budget. No target -> no audit.
2. Check measurement before performance: conversions primary/value/dedup/consent. Bad tracking invalidates all else.
3. Segment brand vs non-brand, new vs returning; analyze each separately.
4. Find the constraint: budget (IS lost budget), rank (IS lost rank), data (conversion volume), target (tCPA too tight), approval (policy/feed), demand (search volume/seasonality), site (CVR).
5. Prioritize by money: wasted spend + foregone profit.
6. Change one major thing at a time per campaign; log it; wait the learning period; compare with control (experiments).
7. Distrust Google's recommendations that increase spend; trust fixes for broken things.
8. Look for leading indicators (CTR, IS, CVR, CPC) before lagging (ROAS).
9. Always ask "what would prove me wrong?" and define a 14–28 day check.
10. Report variance vs target, decisions made, next experiments.

### 18.1 Example diagnostic flow ("conversions dropped 35% week-over-week")
- Check tracking: conversion action volume by source; tag change? consent banner update? site release? (change_event, GA4, tag status.)
- Check serving: primary_status_reasons, disapprovals, budget exhausted early, billing.
- Check demand: impressions, IS, seasonality, holidays; competitor IS (Auction Insights).
- Check efficiency: CVR vs clicks; device split; landing page; stock.
- Check changes: bid strategy, targets, negatives (accidental), auto-applied recs.
- Output: ranked hypotheses with evidence and falsification test.

### 18.2 Example flow ("ROAS below target on PMax")
- Brand share of PMax conversions (search term categories); new-customer mix; asset group performance; product-level winners/losers; feed price gap; URL expansion landing pages; exclude brand, split asset groups by margin tier, adjust tROAS ≤15% steps, wait 2 weeks.

---

## 19. Quick reference: thresholds table

| Topic | Threshold | Type |
|---|---|---|
| Smart Bidding evaluation | ≥30 conv (tCPA/Max conv), ≥50 (tROAS), over month+ | Google (Smart Bidding help) |
| Target changes per step | tCPA ±20%, tROAS ±15–20%, budget ±20% | Heuristic |
| Learning period | ~1–2 weeks / 30–50 conv | Heuristic |
| Lost IS (budget) action | >10% on profitable campaign | Heuristic |
| Lost IS (rank) | >50% non-brand priority -> fix relevance/bid | Heuristic |
| QS | account weighted <6 red; kw ≤4 with big spend | Heuristic |
| Wasted spend | >15–20% on 0-conv terms | Heuristic |
| Hidden search terms | >40% of spend | Heuristic |
| Feed approval | <95% red | Heuristic |
| Price gap | ±5% competitive, >+10% expensive | Heuristic |
| Conversion discrepancy vs back-end | ±15% | Heuristic |
| Pace | 0.9–1.1 ok | Heuristic |
| Budget | up to 2×/day, 30.4×/month | Google [VERIFY] |
| RSA | 15 headlines, 4 descriptions, 30/90 chars | Google |
| change_event window | 30 days | Google |
| OCI window | 90 days from click | Google |
| Consent Mode v2 | required EEA/UK for personalization/measurement | Google policy |
| Remarketing list min | 1,000 (Search) / 100 (Display) | Google [VERIFY] |

## 20. Open questions for implementation (verify before coding)
1. Current Ads API major version and sunset schedule; enum names for `RecommendationType`, `CampaignPrimaryStatusReason`, `BiddingStrategyType` (tCPA/tROAS mapping).
2. Whether account-level negative keywords and PMax negatives are fully writable/readable via API in the pinned version.
3. Merchant API reports service (`price_competitiveness_product_view`, `price_insights_product_view`) field names and Content API sunset date.
4. Developer token tier (Explorer) quotas for read volume; caching strategy.
5. Auction Insights availability via API (historically not available; UI/Report export only) — check.
6. Whether `ai_max` settings are readable in GAQL.
7. EU political ads policy status and impact for clients.
8. Exact UI/API behavior of Consent Mode modeling thresholds.
