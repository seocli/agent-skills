# Meta Ads (Facebook / Instagram) - research for the seocli paid-social persona

Accessed: 2026-10-02. Audience: Italian agencies managing clients' paid social. All text is paraphrased.

## 0. Source quality and how to read this file

Verification tags used below:
- [V] = confirmed this session by fetching/searching the page cited.
- [S] = secondary source (practitioner blog/aggregator) seen in search results, not Meta itself.
- [K] = established Meta behaviour from background knowledge (Business Help Center / Blueprint / Marketing API docs), NOT re-fetched this session. Re-verify before hard-coding in a tool or skill.

Meta's own pages (facebook.com/business/help, developers.facebook.com) are partly JS-rendered, so several fetches returned thin summaries. Numeric thresholds (50 events, 20% budget edit, 48h dedup) are stable and widely repeated, but Meta changes UI names and defaults often; treat anything marked [K]/[S] as "verify in the live account".

Primary URLs consulted (all accessed 2026-10-02):
- Insights API reference: https://developers.facebook.com/documentation/ads-commerce/marketing-api/insights
- Insights breakdowns: https://developers.facebook.com/documentation/ads-commerce/marketing-api/insights/breakdowns [V]
- Omni-optimal / CAPI setup guide: https://developers.facebook.com/docs/marketing-api/best-practices/omni-optimal-setup-guide [V]
- Special ad categories (API): https://developers.facebook.com/documentation/ads-commerce/marketing-api/audiences/special-ad-category
- Insights API 2026 changes (secondary, quotes Meta changelog): https://ppc.land/meta-restricts-attribution-windows-and-data-retention-in-ads-insights-api/ [V][S]
- Learning phase explainers: https://gomarble.ai/blog/meta-ads-learning-phase-understanding , https://ppc.land/learning-phase/ [S]
- ODAX overview: https://influee.co/blog/meta-campaign-objectives [S]
- Andromeda / creative diversification: https://www.jonloomer.com/meta-andromeda/ , https://www.dentsu.com/ae/en/our-latest-thinking/meta-andromeda-strategy , https://segwise.ai/blog/meta-andromeda-update-creative-strategy-2026 [S]
- Incrementality / incremental attribution: https://www.haus.io/article/meta-incrementality-testing , https://bir.ch/blog/meta-incremental-attribution [S]
- EU DMA pay-or-consent: https://www.theregister.com/2025/07/03/meta_ec_dma_sulk/ , https://heise.de/-11107480 [S]
- EU political ads halt (TTPA): https://www.websiteplanet.com/news/meta-stop-political-ads-eu-october-2025 [S]
- Special ad categories practitioner guide: https://www.jonloomer.com/special-ad-categories-meta-ads/ [S]
- Meta Business Help Center (landing; consult per topic): https://www.facebook.com/business/help ; Meta Blueprint: https://www.facebook.com/business/learn

---

## 1. The persona: how a paid-social manager reasons

A senior Meta media buyer for an Italian agency thinks in this order:
1. **Business goal and unit economics first**: margin, AOV, LTV, lead-to-sale rate, sales cycle. Derive break-even CPA / break-even ROAS before opening Ads Manager. (Break-even ROAS = 1 / gross margin; e.g. 40% margin -> 2.5.)
2. **Signal quality second**: can Meta see the conversion I care about (Pixel + CAPI, dedup, value, EMQ)? If not, nothing downstream is trustworthy. Meta optimizes towards the signal you give it, so a poor event = optimized for the wrong people.
3. **Structure third**: as few campaigns/ad sets as the budget allows, so each ad set clears learning. Consolidate before segmenting.
4. **Creative is the main lever** in the Andromeda era (targeting is mostly automated). Plan creative volume, concept diversity, and a testing cadence.
5. **Measure with skepticism**: platform-reported ROAS is attribution-model output, not truth. Cross-check with GA4/server data/CRM, and use lift tests when budget allows. Never judge on <3-7 days of data or single-day swings.
6. **Change slowly**: avoid significant edits mid-learning; scale in steps; duplicate rather than edit when testing.
7. **Report in business terms** to the client (revenue, leads that became customers, CPA vs target), with platform metrics as diagnostics.

Typical questions the persona asks an MCP server: "Which ad sets are Learning Limited? What is frequency by ad set over 7d? Which creatives show CTR decay? Which events have EMQ < 6 or dedup problems? Is spend pacing vs monthly budget? How much of spend sits in <50-events/week ad sets?"

---

## 2. Campaign objectives (ODAX) [S, K]

Since 2022-2023 Meta uses six Outcome-Driven Ad Experience (ODAX) objectives (replacing 11 legacy ones): **Awareness, Traffic, Engagement, Leads, App promotion, Sales**. Source: https://influee.co/blog/meta-campaign-objectives [S]. Catalog Sales is no longer a standalone objective: Dynamic Product Ads run under Sales with a catalog connected at ad set level [S].

Marketing API enums (verify in docs; names are `OUTCOME_*`): `OUTCOME_AWARENESS`, `OUTCOME_TRAFFIC`, `OUTCOME_ENGAGEMENT`, `OUTCOME_LEADS`, `OUTCOME_APP_PROMOTION`, `OUTCOME_SALES` [K].

| Objective | Use when | Typical optimization goals (ad set) | Conversion location options | Persona note |
|---|---|---|---|---|
| Awareness | Reach/brand, new local business, event announcement | Reach, Impressions, Ad recall lift, ThruPlay (video) | n/a | Frequency control matters; measure reach/CPM/ad recall lift, not ROAS |
| Traffic | Content/blog, SEO landing page amplification | Landing page views (NOT link clicks), Link clicks, Impressions, Daily unique reach | Website, app, Messenger/WhatsApp/IG | Optimize for Landing Page Views to filter accidental clicks; low-intent audience, rarely a primary revenue driver |
| Engagement | Video views, page/post engagement, messages, Instagram profile visits | Post engagement, ThruPlay, 2-sec continuous video, Conversations, Page likes | On-ad, Messenger, WhatsApp, IG, website | Cheap interactions != value; use for warm-up/retargeting pools |
| Leads | Lead gen (forms, calls, Messenger/WhatsApp, website) | Leads, Conversion leads, Maximize conversations | Instant forms, website, calls, Messenger/IG/WhatsApp, app | Instant forms = volume but low quality; "Conversion leads"/CRM-fed optimization = quality |
| App promotion | Installs / in-app events | App installs, App events, value | App | Needs SDK/MMP; SKAN limits on iOS |
| Sales | E-commerce, bookings, purchases, offline | Conversions (purchase/ATC/...), Value, Landing page views, Conversations | Website, app, website+app, Messenger/IG/WhatsApp, calls, shop | Default for revenue accounts; Advantage+ sales campaigns live here |

Rules of thumb:
- Choose the objective that matches the **lowest-funnel event with enough volume** (>= ~50/week per ad set). If purchases are too few, step up one level (Add to cart / Initiate checkout / Lead) and re-evaluate.
- Mismatch red flags: Traffic objective optimizing link clicks for an e-commerce client that wants sales; Engagement campaign "reporting ROAS"; Leads campaign optimizing "Leads" without any quality feedback loop.
- For local Italian businesses (restaurants, dentists, gyms), WhatsApp/Messenger conversation objectives and call objectives are common; lead quality hinges on follow-up speed (minutes, not hours).

Common mistakes: running Traffic then retargeting "everyone who clicked" as strategy; using Engagement as a proxy for reach; mixing objectives in one funnel without separate budgets; leaving "Link clicks" instead of "Landing page views".

---

## 3. Advantage+ (automation suite) [S, K]

Meta groups AI automation under "Advantage+". Pieces (names shift; confirm in UI):
- **Advantage+ sales campaigns** (successor of Advantage+ Shopping Campaigns, ASC): automated campaign under Sales objective; audience, placements and creative combos largely automated, up to a high ad count per campaign (historically 150 ads for ASC [K]; verify current cap). Prior "existing customer budget cap" has been removed/changed [S: https://dollarcommerce.substack.com/p/why-did-facebook-remove-the-existing], so you can no longer cap spend on existing customers the old way; use the account-level existing-customer audience definition and customer lists to steer.
- **Advantage+ app campaigns**: automated app installs/in-app events.
- **Advantage+ leads** (newer, rolling out) [K, verify].
- **Advantage+ audience**: replaces "detailed targeting expansion"/lookalike expansion as the default audience control. You provide optional *audience suggestions* (custom lists, interests) which are hints, not hard limits. Hard limits remain: location, minimum age, (exclusion availability and exclusions are limited for some formats/objectives) [K].
- **Advantage+ placements**: delivery across Facebook, Instagram, Messenger, Audience Network, with allocation by predicted performance. Manual placements still possible.
- **Advantage+ creative**: automated enhancements (text variations, image/video enhancements, music, templates, visual touch-ups, brightness/crop, 3D animation). In the EU, review which optimizations are enabled per ad; brand-safety-sensitive clients (luxury, pharma, finance) should switch many off.
- **Advantage+ catalog ads** (dynamic product ads) with catalog segments and broad audiences.
- **Advantage campaign budget** (formerly CBO) and **Advantage+ bidding/Advantage detailed targeting**.

Direction of travel (2025-2026): Meta pushes a "unified" structure with fewer campaigns, more automation, and more creative variety. Dentsu/Loomer/Segwise summaries describe Andromeda (retrieval) + GEM (ranking) selecting ads from a larger candidate pool; creative diversity becomes the targeting signal. [S: https://www.jonloomer.com/meta-andromeda/ , https://www.dentsu.com/ae/en/our-latest-thinking/meta-andromeda-strategy]

Published/claimed numbers to treat with caution:
- Meta-reported internal test: one ad set with 25 diverse creatives produced ~17% more conversions at ~16% lower cost than five ad sets with five creatives each [S: segwise/blckalpaca summaries of Meta claims].
- Practitioner claims that accounts testing 20+ new ads/month outperform those testing <10 [S] - correlation, not proof.
- Independent incrementality studies have found Advantage+ can look better on attributed ROAS than on incremental ROAS (one study cited ~12% worse iROAS than manual by end of test; platform-reported efficiency inflates because of existing customers/retargeting overlap) [S: https://dollarcommerce.substack.com/ ; https://www.haus.io/blog/optimizing-meta-ads-a-playbook-for-brands].

How the persona decides Advantage+ vs manual:
- E-commerce with catalog, >= ~50 purchases/week, healthy pixel+CAPI: start/scale with Advantage+ sales + one manual/broad test campaign for creative testing and a separate prospecting vs existing-customer view.
- Low-volume, lead-gen, B2B, local services: manual Sales/Leads ad sets with broad targeting and 1-2 strong audiences; Advantage+ audience on.
- Heavy brand-safety/regulatory clients (health, finance, alcohol): tighter controls, disable creative enhancements, check placement exclusions via placement/brand-safety controls.
- Always keep an *account-level existing-customer definition* and exclude/segregate customers when the goal is acquisition.

Data to expose: campaign `buying_type`, `objective`, `smart_promotion_type` / Advantage+ flags (field names change; check Campaign fields), `bid_strategy`, `special_ad_categories`, ad set `targeting.targeting_automation.advantage_audience`, `advantage_creative` features in `creative.degrees_of_freedom_spec` [K].

Common mistakes: judging Advantage+ by blended ROAS when 50%+ of purchases are existing customers; changing audiences/creative daily; adding too few creatives; ignoring that Advantage+ cannot be restricted like manual ad sets.

---

## 4. Account / campaign / ad set structure and consolidation [K, S]

Hierarchy: Business Portfolio (Business Manager) -> Ad account -> Campaign (objective, budget level, special ad category) -> Ad set (budget/schedule, audience, placements, optimization & bid, conversion event) -> Ad (creative, copy, CTA, URL parameters, tracking).

Consolidation principles:
- Each ad set needs enough conversions to leave learning: **~50 optimization events in 7 days** (see 5). Budget per ad set therefore >= target CPA x ~50 / 7 per day, e.g. CPA EUR 20 -> ~EUR 143/day per ad set; Meta guidance often phrased as "daily budget >= ~5-10x target CPA" for tests. [K, S]
- Fewer ad sets, broader audiences, more ads per ad set. Overlap between ad sets competes in the same auction (self-competition, higher CPM).
- Typical lean structure for an Italian SMB e-commerce account (EUR 3-15k/month):
  1. Prospecting: 1 Advantage+ sales or 1-2 broad ad sets.
  2. Creative testing: 1 campaign (ABO) with isolated ad sets per concept or per batch.
  3. Retargeting/existing customers: 1 campaign (website visitors 7-30d, cart abandoners, customer list for upsell) - small budget, watch frequency.
- Naming convention (agency hygiene): `[Client]_[Objective]_[Funnel]_[Audience/Geo]_[Offer]_[YYYYMM]` and ad names carrying concept/format/hook, because insights are queried by name. Consistent UTM: `utm_source=facebook|instagram`, `utm_medium=paid_social`, `utm_campaign={{campaign.name}}`, `utm_content={{ad.name}}` (dynamic macros `{{campaign.id}}`, `{{adset.id}}`, `{{ad.id}}`, `{{placement}}`, `{{site_source_name}}`).
- Multi-advertiser ads, Advantage+ catalog, partnership ads, and Instagram "boost" create differing structures; boosted posts from the app are usually a red flag for agency-managed accounts (limited control).
- Accounts: one Business Portfolio per agency client is cleaner; use partner access (client owns assets; agency gets partner access) so the client keeps ownership of Pixel, page, ad account, catalog, and domain verification.

Red flags: 10+ ad sets each under EUR 10/day; duplicated ad sets with identical audiences; many campaigns with identical objective and audience; ads count per ad set >50 without testing intent (or <3 with no variety); multiple pixels per domain; domain not verified.

Data to expose: counts of campaigns/ad sets/ads by status (`effective_status`: ACTIVE, PAUSED, WITH_ISSUES, IN_PROCESS, PENDING_REVIEW, DISAPPROVED, ARCHIVED...), `daily_budget`, `lifetime_budget`, `budget_remaining`, `learning_stage_info` (status: LEARNING, SUCCESS, FAIL; `attribution_windows`, `conversions`, `last_sig_edit_ts`), `optimization_goal`, `billing_event`, `bid_strategy`, `bid_amount`, `promoted_object`, `targeting`, `destination_type`, `issues_info`, `ad_review_feedback`, `created_time`, `updated_time`.

---

## 5. Learning phase and significant edits [V/S/K]

Rules (Meta Help Center, repeated by secondary sources: https://gomarble.ai/blog/meta-ads-learning-phase-understanding , https://ppc.land/learning-phase/):
- Learning begins when an ad set launches or after a **significant edit**. It ends when the ad set gets about **50 optimization events within 7 days** of its last significant edit. [S]
- Statuses in Ads Manager/API (`learning_stage_info.status`): `LEARNING`, `SUCCESS` (active / learning complete), `FAIL` (**Learning Limited**: not enough events), plus "Learning limited" is the surfaced label. [K]
- Learning Limited ad sets can still deliver but have unstable, usually higher CPA; Meta states the delivery system could not explore enough.
- Significant edits (reset learning) [K, S]: changing targeting; creative edits that change the ad (new ad, edited ad content in some cases); optimization event/goal; bid strategy or bid amount; **budget change above ~20%** in one edit (practitioners: raise by <= 20% every 48-72h); pausing the ad set for >7 days; adding a new ad to an ad set (adding ads can trigger re-learning in some accounts); switching placement from automatic to manual. Not significant: small budget changes, pausing briefly, changing schedule within bounds.
- Budget bumps within tolerance (<= ~20%) generally don't reset learning; large jumps do. Duplicating an ad set resets learning on the copy but leaves the original untouched (used for "scaling by duplication", less favoured with Advantage campaign budget).

Persona reasoning:
- Never judge an ad set in its first ~72h/50% of events; exit decision rules: wait for 7d or ~50 events, whichever applies, unless spend is >= 2-3x target CPA with 0 conversions.
- If Learning Limited persists: consolidate ad sets, broaden audience, raise budget, optimize for a higher-funnel event, or lengthen the attribution window (e.g. 7-day click) to capture more events.
- Don't edit constantly. Stagger changes. Log each change (date, who, why) in the client change log.

Data to expose: per ad set `learning_stage_info` (status, conversions since last significant edit, last_sig_edit_ts), `recommendations` (Meta "opportunity score"), edit history from `activities` endpoint on ad account (`/act_{id}/activities`, fields: event_type, object_id, extra_data, actor_name, date_time).

Common mistakes: scaling +50% overnight; swapping creatives in a learning ad set; pausing/unpausing repeatedly; splitting budget so no set clears 50 events; reading "Learning" status as a problem when it is normal.

---

## 6. Budgets: CBO vs ABO

Terminology: Meta now calls CBO "Advantage campaign budget"; ABO = "ad set budget". [K]
- **CBO/Advantage campaign budget**: one budget at campaign level, Meta distributes across ad sets to maximize results. Best when ad sets are similar in intent and you want automated allocation; set ad set spend limits (min/max) sparingly because limits constrain optimization.
- **ABO**: you control each ad set's spend. Best for testing (every concept gets guaranteed spend), for retargeting caps, for geo split with fixed allocation, for low-volume accounts, and for lead-gen across distinct locations.
- Lifetime vs daily: daily budgets can spend up to **25% over** a given day (averaging out over the week; weekly cap = 7 x daily) [K]; lifetime budgets suit fixed-end promotions and use pacing. Spend in Ads Manager can show days above daily budget; this is normal.
- Minimum budgets depend on bid and currency (commonly ~EUR 1/day for link click optimization, higher for conversion optimization) [K].
- Budget pacing and `spend_cap` on the ad account/campaign (`spend_cap`, `amount_spent`) protect client monthly budgets [K].

Heuristics: test budget per concept ~ 3x-5x target CPA over 3-7 days; scaling: +20% every 2-3 days, or duplicate a winning ad set at a higher budget; for low-volume accounts consider "all in one" structure (single campaign, single ad set, many ads).

Italian agency practice: budgets fixed monthly by client contract; weekly pacing review; check budget `remaining`/`spend` vs a straight-line expectation (red flag: under-delivery <70% of planned by mid-month or overspend >110%).

Data to expose: campaign/ad set `daily_budget`, `lifetime_budget`, `budget_remaining`, `spend` vs plan, `delivery_estimate` (audience size, daily outcomes curve), `pacing_type` (standard, no_pacing / day_parting), `min_budget_spend_percentage`, `max_budget_spend_percentage` (spend limits).

Common mistakes: CBO with wildly different audience sizes and no min spend (one ad set hogs spend, tests starve); ABO with many tiny budgets; using lifetime budget without end date logic; comparing CBO ad-set-level ROAS as if budget were equal.

---

## 7. Bidding and optimization strategies [K, S]

Strategies (Marketing API `bid_strategy` enum): `LOWEST_COST_WITHOUT_CAP` (default highest volume), `LOWEST_COST_WITH_BID_CAP` (bid cap), `COST_CAP`, `LOWEST_COST_WITH_MIN_ROAS` (ROAS goal / minimum ROAS) [K].

| Strategy | What it does | When to use | Risks |
|---|---|---|---|
| Highest volume (lowest cost) | Spend full budget for max results, no cost constraint | Default; new accounts, learning, scaling | CPA can drift up as budget scales |
| Cost per result goal (cost cap) | Average CPA <= cap, volume may drop | Stable account with known CPA; protecting margin | Cap too low = under-delivery; set at ~1.2-1.5x observed CPA initially; revisit weekly |
| Bid cap | Max bid per auction, strict | Experienced use, very controlled auctions | Under-delivery very common; rarely first choice |
| ROAS goal (minimum ROAS) | Optimize value, spend only where expected ROAS >= target; requires value-based optimization and purchase value | E-commerce with accurate values and >= ~50 purchases/week | Too high target throttles spend; set below break-even x safety |
| Highest value | Maximize total purchase value within budget | Revenue maximization, mixed AOV | May chase high-AOV buyers; combine with ROAS goal |

Best practice sequence: start highest volume; once CPA is stable for 2-4 weeks, test cost cap at current CPA; use ROAS goal when value signal is clean. Change bid strategy = significant edit (learning reset).

Attribution/optimization alignment: ad sets optimize for conversions *attributed in the chosen window* (default 7-day click + 1-day view for web conversion campaigns; check current default per objective [K]; after Jan 2026 view windows only 1-day view and 1-day engaged-view remain in the API [V][S]).

Persona note: bid strategy is a *tool for cost control*, not a performance trick. If the account is not scaling, the issue is almost always creative, signal or offer.

Data to expose: `bid_strategy`, `bid_amount`/`bid_constraints` (roas_average_floor), `optimization_goal`, `billing_event`, `is_dynamic_creative`, delivery status, `delivery_estimate.estimated_dau`, `estimated_mau`, and `bid_info`.

Common mistakes: cost cap set below market (zero delivery), mixing bid strategies in a CBO, using ROAS goal with unreliable or missing `value`, raising caps then not noticing CPA inflation.

---

## 8. Audiences

### 8.1 Broad / Advantage+ audience
- Meta guidance and practitioners (2024-2026): broad (location + age only, Advantage+ audience on) often wins when the pixel has strong signal, because Andromeda/GEM matches ads to people by creative. [S]
- Add *audience suggestions* (interests, custom lists) only as hints.

### 8.2 Custom audiences
Sources: customer list (hashed email/phone; match rate typically 40-70% [K]), website (Pixel/CAPI events: all visitors, specific pages, time spent, by event), app activity, engagement (video views, IG profile, page, lead form, shopping), offline events, catalog. Retention up to 180 days for website/IG/FB sources (varies; 365 days for list-based/offline) [K]. Minimum audience size ~100 matched users to run; ~1,000+ recommended for stability [K].

### 8.3 Lookalike audiences
Source >= 100 people from a single country (practically 1,000-5,000 for quality); 1%-10% of country population. Choose source by *value* (top 25% purchasers/LTV list, value-based lookalike) rather than "all visitors". With Advantage+ audience on, Meta may expand beyond the lookalike regardless (practitioner measurement: lookalike ad sets with Advantage+ ON had much higher CPA than retargeting ad sets in one account study [S: dollarcommerce.substack.com]). Lookalikes remain useful as a hint and for restricted categories (Special Ad Audiences are replaced by constrained targeting in special categories).

### 8.4 Exclusions
- Exclude: purchasers last 180 days from prospecting (when acquisition-only), existing customers (customer list), current leads/converted contacts, employees, and "visitors 7d" from top-of-funnel if running separate retargeting.
- Note Advantage+ sales/audience constraints: exclusion handling differs (use account-level existing-customer definition for Advantage+ sales; some exclusions are "suggestions" in Advantage+ audience, not strict) [K, verify].
- Geographic targeting for Italy: regions (Lombardia...), cities with radius, or postal codes (CAP); people "living in" vs "recently in" vs "travelling in". For local business use "living in" + radius 10-30 km; CAP targeting is supported in IT [K].

### 8.5 Audience overlap
Audience Overlap tool compares custom audiences; >25-30% overlap between active prospecting ad sets = consolidate or exclude. [K]

### 8.6 Privacy & EU specifics for audiences
- Customer lists require a lawful basis and consent for use in advertising (GDPR); document in contract/DPA. Upload only hashed, and respect opt-outs.
- Custom Audience Terms must be accepted per ad account.

Data to expose: `customaudiences` list (name, subtype, approximate_count_lower/upper_bound, delivery_status, operation_status, retention_days, time_updated), lookalike `lookalike_spec`, ad-set-level `targeting` JSON, `delivery_estimate`. Audience overlap not directly in API (UI-only); can approximate with reach breakdowns.

Common mistakes: lookalike built from page likes; tiny seed (<1000); never refreshing customer lists; retargeting audiences too narrow (frequency > 8 in 7d); not excluding converters from retargeting; layering many interests (narrow targeting hurts learning).

---

## 9. Creative strategy

### 9.1 Formats and specs (confirm in Meta ads guide: https://www.facebook.com/business/ads-guide) [K]
- Image: 1080x1080 (1:1) feed; 1080x1350 (4:5) preferred feed; 1080x1920 (9:16) Stories/Reels. Keep text in safe zones (~14% top / ~35% bottom in 9:16 to avoid UI overlays - confirm).
- Video: 9:16 for Reels/Stories, 4:5 or 1:1 for feed; length 15-30s typical; first 3s decide hold; captions required (most watch muted).
- Carousel (2-10 cards), Collection / Instant Experience, Catalog (dynamic product ads), Advantage+ catalog, Partnership ads (creator/brand), Lead ads forms, Click-to-WhatsApp/Messenger, Reels ads, Story ads, Threads placements (Meta rolled ads into Threads in 2025 [K]).
- Primary text ~125 characters visible before truncation; headline ~40; description rarely shown. Use multiple primary-text/headline variants (up to 5 each) only after the concept is set.

### 9.2 Concept thinking (Andromeda era)
- Andromeda matches ads to people by creative characteristics; near-duplicate ads are treated as one candidate, so **diversity of concept/format/angle/persona** matters more than cosmetic variations. [S: Loomer, Dentsu, Segwise]
- Build a creative matrix: Angle (problem, social proof, offer, education, UGC, comparison) x Format (static, video, carousel) x Hook (first 3s / first line) x Persona/Use case.
- Mix of formats: 60-70% video/Reels-native, rest static/carousel for scalable tests; UGC and creator-style outperform polished studio ads in most DTC categories (practitioner consensus [S]).

### 9.3 Hooks and structure
- First 1-3 seconds: pattern interrupt, visual proof of the outcome, a direct question, bold on-screen text. Hook rate (3-second plays / impressions) benchmark 25-35%+; Hold rate (ThruPlay or 15s / 3-second plays) 20-40% [S, practitioner thresholds, not Meta-published].
- Structure: Hook -> problem/desire -> proof/demo -> offer -> CTA. One message per ad.
- For Italian audiences: native Italian language, local proof (recensioni, spedizione in Italia, pagamento alla consegna / Satispay / Klarna / PayPal where relevant), prices with IVA included for B2C, clear delivery times; use "Scopri di piu"/"Acquista ora" CTAs consistent with the landing page.

### 9.4 Testing frameworks
1. **Concept test (ABO or Advantage+ testing campaign)**: 3-6 concepts, each with 1 hero asset + 1-2 variants; budget EUR >= 3-5x target CPA per concept over 3-7 days; kill when spend >= 2-3x target CPA with no conversion (or CTR/hook < threshold), promote winners to scale campaign.
2. **Iteration test**: take a winner and vary one variable (hook, first frame, headline, offer) to identify the driver.
3. **Meta's A/B test tool** (Experiments): ensures exclusive audience splits and statistical significance (confidence >= 80-90% display).
4. **Dynamic creative / flexible ads**: good for exploring component combinations when volume is low; poorer insight into which element worked.
5. **Cadence**: refresh 3-5 net-new concepts every 2-4 weeks per ~EUR 5-10k/month budget; accounts above EUR 30k/month often need 10+ net-new assets/month [S: practitioner rules].

### 9.5 Fatigue and frequency
- Frequency = impressions / reach. Prospecting: watch >2.5-3 over 7d as caution; retargeting: >5-8 in 7d. [S, practitioner; Meta publishes no hard threshold]
- Fatigue signals: CTR declining 20-30% from peak; CPM rising; CPA rising for same audience; "Ad relevance diagnostics" (quality ranking, engagement rate ranking, conversion rate ranking: Below Average for 2 of 3 = problem) [K]; negative feedback (hides, "report ad") rising.
- Action: introduce new creative concepts (not just recolors), widen audience, cap frequency (reach & frequency buying for awareness only), and rotate.

### 9.6 Creative data to expose
Ad-level: `creative{id,object_story_spec,asset_feed_spec,thumbnail_url,video_id,title,body,call_to_action_type,link_url,url_tags}`, `adlabels`, and insights by `ad_id` with `impressions, reach, frequency, clicks, inline_link_clicks, ctr, inline_link_click_ctr, cpm, cpc, spend, video_play_actions, video_thruplay_watched_actions, video_p25/50/75/95/100_watched_actions, video_avg_time_watched_actions, video_15_sec_watched_actions, video_30_sec_watched_actions, quality_ranking, engagement_rate_ranking, conversion_rate_ranking, actions, action_values, purchase_roas, cost_per_action_type`. Asset-level breakdowns: `image_asset`, `video_asset`, `body_asset`, `title_asset`, `link_url_asset`, `call_to_action_asset`, `description_asset` (see [V] breakdown list) enabling analysis of which asset in a flexible/dynamic ad performs. Derived metrics the server should compute: hook rate = video_play_actions(3s)/impressions (using `video_3_sec_watched_actions` if available [K]), hold rate, thumb-stop ratio, outbound CTR, CPM trend (7d slope), frequency vs CTR decay, % spend on ads older than 30 days.

Common mistakes: testing 10 variations of the same visual; killing ads after 1 day; giving one "winning" ad all budget while its frequency climbs; shipping creative without Italian copy review; text-heavy images; hiding a price/offer that compliance requires.

---

## 10. Measurement

### 10.1 Meta Pixel and Conversions API
- **Pixel** (browser) + **Conversions API (CAPI)** (server) together = "redundant setup" recommended by Meta. Browser loses events to ITP, ad blockers, consent denial; CAPI sends first-party server events. [K, V partly: https://developers.facebook.com/docs/marketing-api/best-practices/omni-optimal-setup-guide]
- Standard events: PageView, ViewContent, Search, AddToCart, AddToWishlist, InitiateCheckout, AddPaymentInfo, Purchase, Lead, CompleteRegistration, Contact, Schedule, SubmitApplication, StartTrial, Subscribe. Required params: `value` + `currency` on Purchase; `content_ids`, `content_type`, `contents` for catalog; `content_category`; `order_id`; `num_items`. [K]
- CAPI mandatory fields: `event_name`, `event_time` (Unix, within 7 days), `action_source` (website, app, physical_store, chat, email, phone_call, system_generated, other), `user_data`; `event_source_url` and `client_user_agent` required for website events [V partial].
- Setup routes: Partner integration (Shopify, WooCommerce, PrestaShop, GTM server-side, Stape, Segment, etc.), CAPI Gateway, direct API/server code. Prefer partner/native integrations for SMB; GTM server-side for custom stacks.

### 10.2 Event Match Quality (EMQ)
- Score 0-10 per event; **target 8+ for Purchase/Lead** (6-8 acceptable, <6 poor). [V: omni setup guide; S]
- Improves with: `em` (hashed email), `ph` (hashed phone, E.164 incl. +39), `fn`, `ln`, `ct`, `st`, `zp`, `country`, `external_id`, `client_ip_address`, `client_user_agent`, `fbc`, `fbp`. Meta weights email, phone, IP, user agent heavily. [V]
- Hash with SHA-256, normalized (lowercase, trimmed; phone digits only with country code). Never hash `fbc`, `fbp`, IP, or user agent.
- `fbc` (click id from `fbclid`, format `fb.1.<ts>.<fbclid>`) is a strong signal: preserve it from landing to checkout.

### 10.3 Deduplication
- Pixel + CAPI events must share the same `event_name` and `event_id` (or `external_id` + `fbp`). Meta dedupes within **48 hours** for web; offline events dedupe over 7 days via `order_id`. [V: omni setup guide]
- Verify in Events Manager -> Overview: events show "Browser and Server" with dedup rate; coverage >= 75% server-side; "Deduplicated" proportion typically 30-60%+ for healthy dual setups. Duplicate Purchase counts inflate ROAS - the #1 audit issue.
- Don't split one order into several events.

### 10.4 Consent Mode / EU
- Pixel/CAPI must respect GDPR/ePrivacy consent in Italy (Garante guidance on cookies). With Consent Mode / `fbq('consent','revoke'|'grant')` the Pixel stays quiet until consent; CAPI events for non-consented users generally should not be sent with personal data (send aggregated/no PII or skip), depending on lawful basis analysis. Maintain CMP (Cookiebot, iubenda, Usercentrics) with Google Consent Mode v2 and Meta consent signals. [K]
- Limited Data Use (LDU) is US-state oriented; not the EU mechanism.
- Cross-link: Italian agencies must document the controller/processor relationship (DPA) with clients; Meta acts as joint controller for Pixel/CAPI data in some cases (Business Tools Terms).

### 10.5 iOS / AEM
- After ATT (iOS 14.5), Meta replaced SKAN-first on web with **Aggregated Event Measurement (AEM)**; 2025+ Meta has moved to Pixel/CAPI-based modelling with limited pre-ATT behaviour. Web events for iOS opt-outs are modelled/delayed; event prioritisation (8 events) was removed from web in 2025 for most advertisers (verify) [K].
- Apps: SKAdNetwork (SKAN 4+) conversion values; use MMP (AppsFlyer, Adjust, Singular).
- Statistical modelling: reported conversions include *modelled* conversions; reported numbers may differ from GA4 by 10-40%.

### 10.6 Attribution settings
- Ad-set-level attribution windows for conversion campaigns: **1-day click, 7-day click (default for most web Sales), 28-day click (some setups), 1-day view, 1-day engaged view**; combined settings like "7-day click + 1-day view" are standard. Since **2026-01-12** the Insights API removed `7d_view` and `28d_view` and many click+view combos; remaining: 1d_click, 7d_click, 28d_click, 1d_view, 1d_ev (engaged view). [V][S: https://ppc.land/meta-restricts-attribution-windows-and-data-retention-in-ads-insights-api/]
- Insights API retention changes (Jan 2026) [V][S]: unique-metric breakdowns and hourly breakdowns limited to **13 months**; frequency breakdown to **6 months**; aggregate totals remain 37 months. MMM breakdowns async only.
- `use_account_attribution_setting=true` returns the ad account default; `action_attribution_windows=["7d_click","1d_view"]` returns specific windows; `use_unified_attribution_setting=true` returns what Ads Manager shows. Without explicit windows, API defaults differ from Ads Manager - a classic data mismatch. [K]
- Incremental attribution (opt-in optimization option): optimizes delivery to people predicted to convert *because* of the ad. Meta-cited summit data: 46% lift improvement across 37 studies [S: https://bir.ch/blog/meta-incremental-attribution]; use cautiously and validate with lift tests.

### 10.7 Conversion Lift, incrementality, MMM
- **Conversion Lift** (Experiments): randomized holdout, Meta reports incremental conversions and iROAS; needs adequate budget/volume (often >= EUR 20-50k over 2-4 weeks per cell; check Meta's power calculator) [K].
- **Geo-lift / holdout** tests (Haus, Measured, Incrmntal, Google CausalImpact) for SMBs when native lift lacks budget; test design: matched markets (Italian regions), 4-6 weeks.
- **MMM**: Meta Robyn (open-source, R) and Meridian-style; Insights supports MMM breakdowns async. Useful for >EUR 100k/month spend; not agency-SMB default.
- Practitioner rule: compare platform ROAS to GA4/CRM revenue; a persistent gap > 30% is a signal to audit tracking.

### 10.8 What the MCP should expose for measurement
- Pixel/dataset (`/{pixel-id}`, `adspixels`): `last_fired_time`, `is_unavailable`, `data_use_setting`, `automatic_matching_fields`, `first_party_cookie_status`, event stats via `/{pixel}/stats?aggregation=event|event_detection_method|device_type|url|custom_data_field` (event_detection_method shows browser vs server) [K].
- Dataset quality: `/{dataset}/server_events_quality` / Events Manager EMQ (Graph endpoint availability varies; `event_match_quality` via Dataset Quality API [K]).
- Diagnostics: events with errors, domain verification (`/{business}/owned_domains`), aggregated event measurement config (iOS), CAPI test events.
- Insights actions with windows (see 12).

Common mistakes: Pixel only; CAPI without dedup (double-counted purchases); purchase value missing/wrong currency (breaks ROAS bidding); event_time stale; wrong `action_source`; ignoring EMQ <6; several pixels firing; GTM tags firing on thank-you page refresh (duplicates); comparing Meta "7d click+1d view" to GA4 last-click; "learning" never ending because conversion event is too rare.

---

## 11. EU specifics (Italy)

### 11.1 Consent and privacy [K]
- GDPR + ePrivacy: cookie/pixel consent via CMP before Pixel fires for tracking; Italian Garante cookie guidelines (2021) require banner with equal-prominence reject, no cookie walls, re-prompt after 6 months at minimum.
- Meta "Business Tools Terms": advertiser warrants it has lawful basis and consent for data sent via Pixel/CAPI/custom audiences.
- Data protection: EU-US Data Privacy Framework valid as of 2026 (challenges ongoing); keep DPA/SCCs with Meta documented.
- Sensitive categories (health, sexual orientation, religion, politics, trade-union) cannot be used for targeting/signals; Meta limits detailed targeting options in EU (removal of sensitive interest targets Jan 2022) and for under-18s (age/location only).

### 11.2 DMA and "less personalised ads" [S]
- The EU Commission found Meta's binary pay-or-consent breached the DMA (April 2025 decision; fine EUR 200M) and Meta committed to offering EU users a choice between fully personalised ads and a limited-data "less personalised ads" experience, rolled out from **January 2026**. [S: https://www.theregister.com/2025/07/03/meta_ec_dma_sulk/ , https://heise.de/-11107480]
- Implication: some EU users see less personalised ads, which reduces data available to optimization; EU CPMs rose and conversions tracked per user may fall, with Meta modelling to compensate. Expect more variance and a stronger case for first-party data/CAPI quality and creative-led targeting. Subscription "no ads" plan exists in EU (since Nov 2023; price reduced in 2024/2025).
- Reporting: Insights does not currently expose per-ad "personalised vs less personalised" split [K, verify]; flag for monitoring.

### 11.3 Political / social issue ads
- Meta **stopped delivering political, electoral and social issue ads in the EU from October 2025** (TTPA regulation). [S: https://www.websiteplanet.com/news/meta-stop-political-ads-eu-october-2025] Agencies serving NGOs, parties, public bodies, advocacy groups must plan alternative channels; ads that Meta judges as "social issue" (immigration, health policy, civil rights, etc.) may be blocked - appeal via Business Help Center.

### 11.4 Special ad categories (Marketing API: `special_ad_categories`) [V partial: https://developers.facebook.com/documentation/ads-commerce/marketing-api/audiences/special-ad-category]
- Values: `CREDIT`/`FINANCIAL_PRODUCTS_SERVICES`, `EMPLOYMENT`, `HOUSING`, `ISSUES_ELECTIONS_POLITICS`, `ONLINE_GAMBLING_AND_GAMING`, `NONE`. Declare at campaign creation; wrong/absent declaration leads to rejection or account restrictions. [K]
- Restrictions in US/CA etc.: no age/gender/ZIP targeting, no lookalikes. EU: Gambling ads require licence (ADM/Italy: Decreto Dignita bans most gambling advertising in Italy - ADM-licensed operators still face very strict limits); financial products require prior authorisation by Meta (Financial Services Verification in EU/UK, advertiser must show authorisation e.g. Banca d'Italia/Consob/IVASS register) [K, verify].
- Italy specifics: Decreto Dignita (2018) bans gambling ads; AGCM rules on influencer/disclosure (#adv), AGCOM influencer guidelines (2024) for influencers with large following; "branded content" must use the paid partnership label.

### 11.5 Other EU/IT rules
- Pricing displays with IVA for B2C (Codice del Consumo); "price crossed" claims must be real (Omnibus Directive: show prior lowest price in last 30 days).
- Health claims (EFSA/Ministero della Salute), cosmetics and supplements claims, weight loss restrictions.
- Age-restricted content (alcohol, vape): age gating 18+ by default; alcohol allowed with targeting 18+/21+ depending on country; tobacco/vape prohibited.
- Under-18 targeting restricted to age + location; no personalised ads for minors in the EU (DSA Art. 28).
- DSA: ad repository (Meta Ad Library), "why am I seeing this ad" transparency.

---

## 12. Insights API surface (what the MCP should expose) [V partial, K]

Base: `GET /v{N}/act_{ad_account_id}/insights` (also on `campaign_id`, `adset_id`, `ad_id`). Use async reports (`POST .../insights` returns `report_run_id`, poll `/{report_run_id}`) for big pulls. Rate limits via business-use-case headers (`X-Business-Use-Case-Usage`, `X-Ad-Account-Usage`); implement backoff and caching (matches the seocli cache posture). Current Marketing API version: check Meta changelog (versions roll ~yearly; each supported ~2 years).

### 12.1 Parameters
- `level`: account | campaign | adset | ad.
- `date_preset` (today, yesterday, last_7d, last_14d, last_28d, last_30d, this_month, last_month, maximum...) or `time_range={"since","until"}`; `time_increment` (1, 7, monthly, all_days).
- `fields`, `breakdowns`, `action_breakdowns`, `filtering`, `sort`, `limit`, `action_attribution_windows`, `use_unified_attribution_setting`, `use_account_attribution_setting`, `action_report_time` (impression | conversion | mixed).
- Note: default attribution and `action_report_time` differ across Ads Manager and API; always return the parameters used with the data so Claude can explain mismatches.

### 12.2 Core fields (by purpose)
- Delivery/cost: `spend, impressions, reach, frequency, cpm, cpp, clicks, cpc, ctr, unique_clicks, unique_ctr, inline_link_clicks, inline_link_click_ctr, cost_per_inline_link_click, outbound_clicks, outbound_clicks_ctr, cost_per_outbound_click, landing_page_views via actions, objective, optimization_goal, account_currency, date_start, date_stop`.
- Conversions: `actions` (action_type: purchase/omni_purchase, offsite_conversion.fb_pixel_purchase, lead, onsite_conversion.lead_grouped, link_click, landing_page_view, add_to_cart, initiate_checkout, view_content, complete_registration, messaging_conversation_started_7d, etc.), `action_values`, `cost_per_action_type`, `conversions`, `conversion_values`, `cost_per_conversion`, `purchase_roas`, `website_purchase_roas`, `mobile_app_purchase_roas`, `cost_per_unique_action_type`, `unique_actions` (13-month limit with breakdowns [V]).
- Video: `video_play_actions, video_30_sec_watched_actions, video_p25/p50/p75/p95/p100_watched_actions, video_thruplay_watched_actions, video_avg_time_watched_actions, video_continuous_2_sec_watched_actions, cost_per_thruplay`.
- Quality: `quality_ranking, engagement_rate_ranking, conversion_rate_ranking` (note: `relevance_score` is not returned with breakdowns [V]).
- Learning/delivery state lives on ad set: `learning_stage_info`, `effective_status`, `issues_info`.
- Awareness/brand: `estimated_ad_recall_rate`, `estimated_ad_recallers`, `cost_per_estimated_ad_recallers` (lift-estimated; limited availability).
- Leads: `actions` with `lead`, plus Lead Ads Graph `/{form-id}/leads` for data and `leadgen_forms` for form definitions (contains PII - restrict, GDPR).

### 12.3 Valid breakdowns [V: https://developers.facebook.com/documentation/ads-commerce/marketing-api/insights/breakdowns]
Demographics: `age, gender, country, region, dma`; Placement/device: `publisher_platform, platform_position, impression_device, device_platform`; Time: `hourly_stats_aggregated_by_advertiser_time_zone`, `hourly_stats_aggregated_by_audience_time_zone` (13-month retention [V]); Actions: `action_type, action_device, action_destination, action_target_id, action_reaction, action_video_sound, action_video_type`; Assets: `image_asset, video_asset, body_asset, title_asset, description_asset, link_url_asset, call_to_action_asset`; Others: `product_id`, `frequency_value` (6-month retention [V]), `place_page_id`, `skan_*` for iOS app campaigns, `mmm` (async only [V]), `dynamic_creative_asset`.
- Combination limits: only some permutations are valid (age+gender OK; publisher_platform+platform_position+impression_device OK; video_* fields cannot combine with hourly) [V]. The MCP tool should validate combos locally and give a clear error rather than forwarding an opaque API error.
- Type-1 breakdowns (region, dma, hourly) don't return unsupported off-Meta metrics; type-2 (action_device, product_id...) return off-Meta web metrics without breakdown values [V]. Document this; the server should note limitations in tool descriptions.
- Don't request `app_store_clicks`, `newsfeed_*`, `relevance_score` with breakdowns [V].

### 12.4 Other endpoints
- `/act_{id}/campaigns|adsets|ads|adcreatives|customaudiences|adimages|advideos|activities|adrules_library|ads_volume|delivery_estimate|reachestimate|insights|adspixels|product_catalogs|owned_pixels`.
- `/act_{id}?fields=name,currency,timezone_name,account_status,disable_reason,amount_spent,spend_cap,balance,min_daily_budget,business,funding_source_details,is_prepay_account,tax_id_status,owner,agency_client_declaration`.
- `/{business-id}/owned_ad_accounts|client_ad_accounts|owned_pixels|owned_domains`.
- Ads Library API (`/ads_archive`) for competitor research in the EU (all active ads visible in EU with delivery countries; includes ad content, dates, and EU reach for ads shown in the EU). Useful for creative inspiration and competitor benchmarking; not for performance data.
- Meta MCP landscape: community servers exist (e.g. pipeboard-co/meta-ads-mcp, glama.ai listing) exposing insights, campaigns, audiences. Meta also publishes agentic tooling announcements (verify current official MCP status before positioning seocli against it). [S]

### 12.5 Permissions/tokens
`ads_read` (reporting), `ads_management` (write), `business_management`, `pages_read_engagement`, `leads_retrieval`, `catalog_management`. System-user tokens for agencies; long-lived user tokens expire ~60 days. Advanced access via App Review. Never log tokens (seocli constitution IV). Consider read-only default scope for the MCP.

### 12.6 Errors worth special handling
- Code 17/4/613 rate limits; 190 invalid token; 100 invalid param; 2635 deprecated version; subcode 1487xxx for policy. Unavailable metric for attribution window after Jan 2026 changes -> must degrade gracefully.

---

## 13. Policies and ad rejections [K, S]

Governing docs: Meta Advertising Standards (https://transparency.meta.com/policies/ad-standards/) and Commerce Policies.
Common reasons for rejection in Italian accounts:
- **Personal attributes** ("Hai il diabete?", "Sei in sovrappeso?"): copy that asserts or implies knowledge of a person's attributes (health, finances, ethnicity, religion, sexual orientation). Rewrite in neutral, benefit-first language.
- **Health and wellness claims**: before/after images, unrealistic weight loss, miracle cures; supplements must avoid disease claims.
- **Misleading claims / sensationalism**: fake countdown, "gratis" when not free, exaggerated results.
- **Landing page issues**: broken pages, mismatch with ad, non-functional checkout, missing privacy policy, auto-download, interstitial pop-ups, domains not verified.
- **Prohibited/restricted content**: gambling (needs permission; Italian ban), alcohol (age-gate), cryptocurrency (prior approval), adult content, weapons, tobacco/vape, unsafe supplements, multi-level marketing flagged as "unrealistic income".
- **Circumventing systems**: cloaking, redirect chains, repeated near-duplicate ads after rejection, hostile appeal.
- **Low-quality / engagement bait**.
- **Brand/IP**: unauthorised logos, "Vendita di prodotti griffati" without proof; celebrity likeness (scam) - strong enforcement.
- **Ad account restrictions**: payment failures, unusual spending, policy strikes (restricted ad account, "restricted advertising access"), identity verification / "Business Verification" needed; account quality in Account Quality (https://business.facebook.com/accountquality).

Process:
1. Check `effective_status`, `ad_review_feedback`, and Account Quality.
2. Edit the ad (not just resubmit) to fix cause; request review if you believe it's a mistake (appeal within 180 days).
3. Avoid creating many near-identical ads while one is under appeal. Keep a rejection log per client.
4. Persistent problems: separate Business Portfolios per client risk; spread accounts; but avoid circumvention. Domain verification + 2FA + at least two admins to avoid lockout.
5. Italian regulation overlay: AGCM/IAP (Istituto dell'Autoregolamentazione Pubblicitaria) code, health/nutrition claims, influencer disclosures.

MCP exposure: `ads` filtered by `effective_status in [DISAPPROVED, WITH_ISSUES, PENDING_REVIEW]` with `ad_review_feedback` (global/placement-specific reasons and `ad_review_feedback.global`), `issues_info` (error_code, error_summary, level, error_message), account `disable_reason`, `account_status` codes (1 ACTIVE, 2 DISABLED, 3 UNSETTLED, 7 PENDING_RISK_REVIEW, 8 PENDING_SETTLEMENT, 9 IN_GRACE_PERIOD, 100 PENDING_CLOSURE, 101 CLOSED, 201 ANY_ACTIVE, 202 ANY_CLOSED) [K].

---

## 14. Auditing an account (checklist, thresholds, red flags)

Format: Check -> pass threshold -> red flag. Thresholds are practitioner heuristics unless tagged; adapt to client margin and vertical.

### 14.1 Foundations (access and compliance)
- Ownership: client owns Business Portfolio/Page/Pixel/catalog; agency is partner with minimal rights. Red: agency owns assets, single admin, no 2FA.
- Business verification complete; domain verified; Account Quality green; payment method valid, spending limit set, currency EUR, time zone Europe/Rome. Red: restricted account, unsettled balance, wrong time zone (breaks daily reporting and day comparisons).
- Special ad category declared where required (financial services, employment, housing, politics). Red: finance clients without verification.

### 14.2 Tracking and data quality
- Pixel/dataset firing (last_fired < 24h); CAPI active; dedup rate present; events: ViewContent, AddToCart, InitiateCheckout, Purchase (with value & currency) / Lead. Red: Purchase value zero or missing; events only from browser; double Purchase counts; Pixel on staging.
- EMQ: Purchase >= 8 (ok 6-8, red <6). Coverage of server events >= ~75% of browser events [S].
- Compare Meta purchases vs backend/GA4 orders: within 15-30% explained by attribution/consent; red: Meta > 100% of real orders (duplicates) or <40%.
- Custom conversions / aggregated event config sensible; consent banner functional (test with reject).
- UTM completeness: 95%+ of ads with `url_tags`. Red: ads without UTMs (GA4 attributes to direct).

### 14.3 Structure
- Learning status: share of ad sets Learning Limited; red if > 30% of spend in Learning Limited ad sets.
- Number of ad sets vs budget: budget per ad set >= ~EUR 20-50/day (prospecting e-commerce), or >= 5-10x CPA/day for conversion goals. Red: >10 ad sets under EUR 10/day.
- Overlap: >30% audience overlap in active ad sets.
- Duplicated ad sets/campaigns; zombie campaigns (active with 0 spend 14 days).
- Objective/optimization alignment: Sales -> Purchase; Leads -> Lead (quality-fed); no Traffic with "Link clicks" for conversion goals.

### 14.4 Audiences
- Prospecting excludes purchasers/customers (acquisition campaigns) unless Advantage+ sales with existing customer definition; retargeting windows: ATC/IC 7-14d, viewers 30d, buyers 30-180d upsell.
- Broad vs narrow: red when interests stack to <500k audience for conversion goals; custom audience age (>180d unrefreshed lists).
- Geo targets match business footprint; language targeted only when needed.
- Frequency: prospecting 7d <3; retargeting 7d <6-8; red above.

### 14.5 Creative
- Active ads per ad set: 4-10 distinct concepts (Andromeda era: more variety); red: 1-2 ads or >30 ads without differentiation.
- Creative age: share of spend on ads > 45 days old (red > 60% with falling CTR); new concepts launched per month vs budget band.
- Formats: includes 9:16 Reels/Stories assets; text overlay safe zones; captions; Italian copy; compliance-friendly claims.
- Performance diagnostics: CTR (link) benchmarks (all industries 0.8-1.5% typical for feed; <0.5% weak; Reels lower) [S practitioner]; hook rate <20% weak; CPM trend +30% WoW red; quality rankings Below Average.
- Landing page: speed (LCP < 2.5s), mobile-first, ad-to-page message match, price/IVA clear, trust signals, checkout friction; connect to SEO/CRO audit. LPV/click ratio < 70% -> slow page or accidental clicks (red).

### 14.6 Bidding and budgets
- Bid strategy defaults to highest volume unless a clear cap is justified; caps not starving delivery (delivery <80% of budget red).
- Budget pacing vs plan (0.9-1.1 of expected; red outside).
- Frequent budget edits >20% (see change log).
- Scale rules documented.

### 14.7 Results vs business
- Blended MER = total revenue / total ad spend (all channels) vs break-even; platform ROAS vs break-even ROAS; CPA vs target; new customer share; CAC vs LTV. Red: Meta-reported ROAS > 3x GA4 data-driven ROAS with no explanation.
- Lead gen: CPL, cost per qualified lead (CPQL), lead->appointment->sale rates; speed-to-lead (< 5-15 min). Red: leads not contacted > 1 hour, instant forms with no friction (junk leads), no CRM feedback (offline conversion upload via CAPI).
- Trend: 28d vs prior 28d on spend, CPM, CTR, CVR, CPA, frequency.

### 14.8 Hygiene and governance
- Naming consistency, change log, weekly report cadence, budget approvals, rejection log, creative library, test backlog, experiment results recorded.
- Policy risk: exposure to personal-attribute copy, health claims, UGC rights (creator usage rights contracts; partnership ads permissions).

### 14.9 Scoring approach for the MCP
Return per-section pass/warn/fail with evidence (metric, value, window, threshold, entity ids), so Claude can explain. Avoid a single opaque score. Provide `thresholds` object so the persona/agency can override per client (vertical, AOV, margin).

---

## 15. Reporting KPIs for agencies

### 15.1 Layers
1. **Business outcomes** (client-facing headline): revenue (from store/GA4/CRM), ROAS/MER, orders, new customers, CAC, leads->qualified->customers, cost per qualified lead, appointments/calls, booked revenue.
2. **Platform efficiency** (diagnostic): spend, CPM, CTR (link), CPC (link), CVR (landing->purchase), CPA/CPL, platform ROAS, AOV, frequency, reach.
3. **Funnel leading indicators**: LPV rate, ATC rate, IC rate, checkout completion; video hook/hold; engagement quality.
4. **Creative performance**: per concept/hook: spend share, CPA, hook rate, hold rate, thumbstop, CTR, CVR; fatigue flags.
5. **Quality of data**: EMQ, dedup rate, learning-limited %, tracking coverage.

### 15.2 Formulas
- ROAS = purchase value / spend; Break-even ROAS = 1 / gross margin (or contribution margin after shipping, fees, returns).
- MER (marketing efficiency ratio) = total revenue / total marketing spend; aMER for new customers = new customer revenue / spend.
- CAC = spend / new customers; payback = CAC / (monthly gross profit per customer).
- CPL, CPQL = spend / qualified leads; Lead quality rate = qualified / total leads.
- CPM = spend / impressions x 1000; CTR (link) = inline_link_clicks / impressions; CVR = conversions / link clicks (or LPV).
- Hook rate = 3-sec plays / impressions; Hold rate = ThruPlay / 3-sec plays (define clearly).
- Frequency = impressions / reach; incremental reach.
- Spend pacing = spend-to-date / (budget x elapsed fraction).

### 15.3 Benchmarks (use with strong caveats; Italy, 2025-2026 practitioner ranges, not Meta-published) [S]
- CPM: EUR 5-15 in Italy for broad e-commerce prospecting (rises in Q4; higher for finance/B2B EUR 15-40).
- CTR link: 0.8-1.5% feed; 0.3-0.8% Reels/Stories; lead ads CTR 1-2%.
- CVR (landing->purchase) e-commerce 1-3%; lead pages 5-15%; instant form CPL typical EUR 5-25 B2C local; EUR 30-150 B2B.
- Frequency prospecting 1.5-2.5 per week.
- Use client's own history (28/90-day) as primary benchmark rather than industry numbers.

### 15.4 Cadence and format
- Daily: spend/pacing/anomaly check (spend spike, zero delivery, disapprovals).
- Weekly: performance vs target, creative review, learning status, decisions & next tests.
- Monthly: business review, MER/CAC, budget allocation across channels, incrementality learnings, roadmap.
- Always show period-over-period and vs target; annotate changes (launches, edits, promos, seasonality: Saldi, Black Friday, Ferragosto, Natale).
- Italian client communication: clear Italian, avoid jargon, link ROAS to euro profit.

### 15.5 Reporting pitfalls
Mixing attribution windows between periods; comparing Ads Manager default and API default; reporting reach summed across days (non-additive); summing unique metrics across breakdowns; time zone mismatches; counting results of different event types together; ignoring currency conversion (EUR vs account currency); reporting platform ROAS as incremental truth.

---

## 16. Paid social in a full-funnel strategy with SEO/GEO and Google Ads

### 16.1 Role differences
- **SEO/GEO** = capture existing demand and be cited by AI answer engines; slow compounding; no media cost.
- **Google Ads (Search/Shopping/PMax)** = capture high-intent demand now.
- **Meta paid social** = create and shape demand (prospecting), nurture (retargeting), and recover (cart abandoners, win-back); strong on visual, impulse and local services; also lead gen.
- Interdependence: Meta-driven brand/awareness grows branded search (verify in GSC: branded impressions/clicks trend), direct traffic, and AI mention likelihood; SEO content supplies landing pages and proof assets; Google Ads brand/non-brand capture follows social-led demand.

### 16.2 Practical synergies
1. **Audience flow**: SEO pages (high-intent organic visitors) feed retargeting pools (site visitors by content cluster); Meta can promote top-performing organic articles to warm pools. Exclude Search converters from prospecting.
2. **Keyword -> creative angle**: use GSC queries and People Also Ask, SERP questions to drive hooks and objections in ads; use Meta comment/DM FAQs to feed FAQ content for SEO and GEO (FAQ schema, answer-first sections).
3. **Branded search lift**: track branded query volume (GSC) and Google Ads brand impression share during Meta pushes; set up geo or time-based lift analysis. Do not claim causality without a test.
4. **Landing page cohesion**: one CRO program across Google/Meta/organic; match message; Core Web Vitals; schema; trust signals; Italian localisation.
5. **Offer testing**: test offers/angles in Meta (cheaper fast feedback) then reuse winning messaging in Google Ads headlines and SEO title/meta copy.
6. **Budget allocation**: baseline - capture channels first (brand search, shopping) then expand prospecting when MER is healthy. For new brands with no demand, Meta carries more weight; for search-led categories (emergency plumber, locksmith), Google first and Meta for retargeting/brand.
7. **GEO angle**: AI engines (ChatGPT, Gemini, Perplexity, AI Overviews) cite sources with authority and consistent entity info; paid social supports entity building (reviews, UGC, creator mentions, PR) but doesn't directly influence citations; track "AI referral" sessions in GA4 and brand mentions.
8. **Measurement stack**: GA4 + server-side tracking + CRM; use MER and blended CAC across channels, compare with each platform's reporting; run holdouts or geo tests for Meta; monitor assisted conversions (GA4 data-driven attribution + "Conversion paths").
9. **Retargeting synergy with email/CRM**: customer lists from CRM -> Meta/Google Customer Match; suppression of converted users across channels saves spend.
10. **Local businesses**: Google Business Profile + local SEO + Meta radius ads + reviews creative; WhatsApp click-to-chat bridging.

### 16.3 What the persona recommends in a client brief
- State which channel does which job and which KPI judges it (e.g. Meta: new-customer CAC & aMER; Google Search: branded/non-branded CPA; SEO: non-branded clicks and conversions; GEO: citation share).
- Don't judge Meta on last-click GA4 alone: it undervalues prospecting; don't judge on platform view-through either: it overvalues retargeting.

### 16.4 Cross-tool opportunities for seocli
- Join GSC (queries, landing pages) with Meta insights by landing URL (UTM `utm_content`/URL) to show organic vs paid performance per page.
- Flag landing pages receiving paid traffic with poor CWV/indexing/canonical issues (SEO audit tools) -> paid efficiency fix.
- Flag pages with high organic impressions but low CTR as candidates for paid promotion/test of hooks (message testing).
- Tie brand search trend (GSC) to Meta spend flights in reports.

---

## 17. Suggested seocli MCP tool surface for Meta (capability-based names, no vendor leak in tool names per constitution V; Meta is an exception only because it is the user's connected account)

Read-only first (ads_read):
1. `list_ad_accounts` - accounts accessible, status, currency, timezone, spend cap.
2. `get_ad_account_health` - account_status, disable_reason, payment/balance, Account Quality-style signals, business verification, domain verification.
3. `get_structure` - campaigns/ad sets/ads tree with status, budgets, objective, bid strategy, learning status, issues.
4. `get_performance` - Insights with level, date range, time_increment, breakdowns validated, attribution windows explicit, result echoing parameters used.
5. `get_creative_performance` - per ad/asset with hook/hold/fatigue metrics, age, frequency, ranking.
6. `get_learning_status` - ad sets in Learning/Learning Limited with event counts and last significant edit.
7. `get_audiences` - custom/lookalike audiences with size, status, retention, last updated; overlap approximations.
8. `get_tracking_health` - pixel/dataset status, event stats by detection method, EMQ, dedup, domain verification, consent flags.
9. `get_change_history` - activities log with diff of significant edits.
10. `audit_account` - runs section 14 checks with thresholds and evidence (pass/warn/fail).
11. `get_pacing` - monthly budget vs spend projection per campaign.
12. `get_policy_issues` - disapproved/issues ads with reasons and suggested fixes.
13. `search_ad_library` (EU Ad Library) - competitor ads by page/keyword/country.
14. `compare_paid_vs_organic` - join with Search Console by landing page / brand query (cross-tool).

Write tools (later, gated; ads_management): create/pause/resume campaigns and ads, adjust budgets with significant-edit guard (warn when change >20% or learning would reset), upload customer lists, rename. Require explicit confirmation and dry-run output; log every change.

Design rules for the tools:
- Always echo `date_range`, `timezone`, `currency`, `attribution_setting` used.
- Return IDs + names, paginate (cursor `after`), cache short TTL (Insights ~15-60 min; structure ~5 min).
- Descriptions in Italian per project convention; parameters/JSON keys in English.
- Handle API deprecations (removed attribution windows, retention limits) by clear errors, e.g. "finestra 7d_view non piu disponibile dal 12/01/2026".
- Treat PII (lead form contents) as sensitive: don't return by default; require explicit tool and consent note. Never log tokens.

---

## 18. Common mistakes (consolidated, ranked by frequency in agency audits)

1. Weak or duplicated tracking (no CAPI, no dedup, missing purchase value) -> optimization on bad data and inflated ROAS.
2. Over-segmentation: too many ad sets, none out of learning.
3. Too little creative diversity; refreshing cosmetics rather than concepts; running one hero ad until fatigued.
4. Editing too often / large budget jumps -> perpetual learning.
5. Judging on too little data or on the wrong attribution window; mismatching Ads Manager vs API vs GA4.
6. Retargeting pools not excluded from prospecting, buyers not excluded from acquisition; Advantage+ ROAS read without separating existing customers.
7. Optimizing for cheap events (clicks/engagement) while wanting sales/leads; no lead quality feedback loop.
8. Landing page poor (speed, message match, checkout), blamed on the ad.
9. Compliance neglect: personal attributes, health claims, special categories, consent management, political/social-issue content in EU after Oct 2025.
10. No incrementality mindset: scaling on attributed ROAS alone.

---

## 19. Open items to verify before encoding as hard rules in skills/tools

- Exact current Marketing API version and any 2026 field renames (Advantage+ flags, `learning_stage_info` stability).
- Current default attribution window per objective in Ads Manager vs API after the Jan 2026 change.
- Whether CAPI Dataset Quality API exposes EMQ per event programmatically (Graph endpoint names).
- Advantage+ creative settings available in the EU and any per-region restrictions.
- DMA "less personalised ads" rollout impact on delivery/reporting and any new Insights breakdown.
- Financial services verification requirements for Italian clients (which registries accepted).
- Meta's official MCP / agentic tooling status as of Oct 2026, and the Ads Manager "Advantage+ campaign" consolidation timeline (Meta announced plans to unify Advantage+ campaign types; verify at https://developers.facebook.com/docs/marketing-api/changelog).
- Practitioner thresholds (frequency, hook rate, creative volume) are heuristics: keep configurable per client.
