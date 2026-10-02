# SEO Analyst Playbook (research for the seocli plugin)

Access date for all URLs: 2026-10-02. All text is paraphrased. Confidence tags:
[P] = primary/official doc fetched or surfaced in search; [A] = academic paper; [B] = book/author
description (TOC or public summary only, no book text reproduced); [H] = practitioner heuristic,
treat as a prior to be tested, never as a fact. Numbers tagged [H] must be re-derived on the
client's own data before being quoted to a client.

Server-side data vocabulary used below (what the seocli MCP server is expected to expose):
- GSC = Search Console performance data (query, page, country, device, date, searchAppearance)
- GA4 = Analytics Data API / BigQuery export (landing page, session source/medium, key events)
- SERP = live or cached SERP snapshots (organic, features, owners, AI answer presence)
- CRAWL = site crawl (status, indexability, canonicals, titles, headings, links, word count, schema)
- HIST = stored time series per (site, query, page, date) kept by the server beyond GSC's 16 months

Cross-cutting rule: every figure in a deliverable carries (source, date range, filter, row count,
sampling/anonymization caveat). A number without those is not a finding.

---------------------------------------------------------------------------------------------
## 0. Source map (what each source is good for)

| Source | URL | Use | Tag |
|---|---|---|---|
| Google, Performance report metrics (impressions, clicks, position) | https://support.google.com/webmasters/answer/7042828 | metric definitions | [P] |
| Google, Performance report "About the data" | https://support.google.com/webmasters/answer/17011364 | aggregation, anonymization, freshness | [P] |
| Google, Search Console report aggregation (property vs page) | https://support.google.com/webmasters/answer/7576553 | why chart totals differ from table totals | [P] |
| Google, Search Analytics API "all your data" guide | https://developers.google.com/webmaster-tools/v1/how-tos/all-your-data | 50K rows/day/search type, dropped rows, searchAppearance two-step | [P] |
| Google, GA4 engagement rate / bounce rate | https://support.google.com/analytics/answer/12195621 | engaged session = >10 s, or key event, or >=2 views | [P] |
| Google, GA4 data thresholds | https://support.google.com/analytics/answer/9383630 | withheld rows, demographics | [P] |
| Google, GA4 BigQuery export vs UI | https://developers.google.com/analytics/blog/2023/bigquery-vs-ui | why export and UI numbers differ | [P] |
| Broder 2002, A Taxonomy of Web Search | https://cse.hkust.edu.hk/zsearch/qualify/DistributedSearch/ATaxonomyOfWebSearch.pdf | navigational / informational / transactional | [A] |
| Jansen, Booth, Spink, user intent of web queries | https://faculty.ist.psu.edu/jjansen/academic/pubs/jansen_user_intent.pdf | automatic intent classification, ~74% accuracy | [A] |
| Brodersen et al. 2015, CausalImpact | https://arxiv.org/abs/1506.00356 | BSTS counterfactual | [A] |
| Enge, Spencer, Stricchiola, The Art of SEO 4th ed. (O'Reilly, 2023) | https://www.oreilly.com/library/view/-/9781098102609/ | 15 chapters: keyword research, content, technical, local, privacy, SEO research and study | [B] |
| Schwartz, Product-Led SEO (public summaries) | https://www.shortform.com/pdf/product-led-seo-pdf-eli-schwartz | "what are users trying to accomplish" over "what keywords exist"; build tools/templates/directories | [B] |
| Jantsch and Singleton, SEO for Growth | https://ducttapemarketing.com/seo-growth/ | SEO as one tactic inside a marketing strategy; strategy and mindset | [B] |
| CausalImpact for SEO walkthrough | https://www.jcchouinard.com/causalimpact-for-seo | practitioner use with GSC | [H] |
| CTR study summaries 2025 (AI Overviews effect) | https://ahrefs.com/blog/ai-overviews-reduce-clicks-update , https://outerboxdesign.com/articles/seo/ctr-based-on-organic-position/ | CTR drift | [H] |

Notes on the books: only tables of contents and public summaries were reachable. The Art of SEO 4th
ed. is a 15-chapter reference (keyword research is around chapter 6, local search ch. 12, data
privacy ch. 13, "SEO Research and Study" ch. 14, future of SEO ch. 15) [B]. Product-Led SEO frames
SEO as a by-product of solving a user job, which changes opportunity sizing: judge a topic by the
product tie-in, not only by volume [B]. SEO for Growth places SEO inside the wider funnel [B].
Where this file gives thresholds they come from official docs [P], papers [A], or are labeled [H].

---------------------------------------------------------------------------------------------
## 1. Data foundations an analyst must know before any analysis

### 1.1 GSC semantics [P]
- Impression: the link was in a result the user could have seen; for some result types it may need
  scrolling or expanding. Click: user clicked through to the site from Google Search. CTR = clicks /
  impressions, always recomputed from sums, never averaged from row CTRs.
- Position is the position of the TOPMOST result of the site for that query/impression, averaged
  over impressions. Consequences:
  - Position is an impression-weighted average. Weighted mean = sum(position_i * impressions_i) /
    sum(impressions_i). Never take a plain mean of row positions.
  - A page that ranks 3 for one query and 40 for another has a blended position that describes
    neither. Always analyze position at the (query, page) grain, never page-wide.
  - Position is only for Google Search results (not Discover, not Google News tab separately
    unless the searchType is selected).
- Aggregation: grouped by query/country/device/date aggregates by PROPERTY; grouped by page or
  searchAppearance aggregates by PAGE. Hence chart totals can differ from table totals [P
  7576553]. Consequence for the MCP server: request totals in a separate call with no query or page
  dimension, and label every table with the grouping used.
- Anonymized queries: rare queries are withheld for privacy; they count in totals but have no
  query string. Practitioner estimates of the hidden share range roughly 30-50% of clicks on some
  sites [H]; measure it per site: hidden_share = 1 - sum(clicks over query rows) / total clicks.
  Report it in every query-level analysis.
- Freshness: newest data is preliminary (dotted line in the UI), may change within hours; the API
  exposes dataState (final vs all). Typical practice: exclude the last 2-3 days from trend
  comparisons [P all-your-data guide says data typically available after 2-3 days].
- Retention: 16 months [P via search results; Search Engine Land, Feb 2018 announcement
  https://searchengineland.com/google-search-console-analytics-api-now-has-16-months-of-data-300430].
  HIST must snapshot daily to enable >16 month analysis and year-over-year after month 16.
- API row limit: max 50K rows per day per search type, sorted by clicks; adding page and/or query
  dimensions may drop data [P]. Pagination step 25,000 rows; request one day at a time to avoid
  quota and to maximize retrieval [P].
- Search appearance requires a two-step query: first list appearances alone, then filter by one
  [P].
- Search types are separate universes: web, image, video, news, discover, googleNews. Never sum
  them without saying so.

### 1.2 GA4 semantics [P]
- Engaged session: lasts longer than 10 seconds, OR has a key event, OR has 2+ page/screen views.
  Engagement rate = engaged sessions / sessions. Bounce rate = 1 - engagement rate (not the UA
  definition). first_visit, first_open, session_start do not count as engaging events.
- Thresholding: rows may be withheld when demographics or demographic-based audiences are used
  (and when Google Signals is on); the threshold is unpublished [P]. BigQuery raw export is not
  thresholded [search result summary of Google docs].
- Sampling: standard reports are unsampled; explorations and some API queries can be sampled when
  the request exceeds the property's quota (10M events for standard properties per query in
  Explorations [H, verify in docs before quoting]).
- UI vs BigQuery numbers differ (modeling, thresholds, late-arriving events, user identity space)
  [P bigquery-vs-ui]. State which one the number came from.
- Organic channel = default channel group "Organic Search" based on source/medium rules. It counts
  Google organic AND Bing, DuckDuckGo, etc. unless filtered to source = google. For GSC-to-GA4
  reconciliation filter to google / organic.
- Attribution: GA4 sessions are attributed by last non-direct click inside the session window;
  data-driven attribution applies to key events in attribution reports, not to session counts.
  "Landing page + session source/medium" is the right pair for SEO.
- Consent mode / cookie refusal removes a share of users from GA4; GSC clicks are consent-free.
  The ratio GA4 organic sessions / GSC clicks is therefore a health metric (typically below 1.0;
  [H] a stable ratio matters more than its level).

### 1.3 Reconciliation check (run before trusting either source)
1. Same period, same property, google/organic only.
2. ratio = GA4 organic landing sessions / GSC web clicks. Track by week.
3. A step change in the ratio (> +/-20% [H]) means tracking, consent, redirect or tagging change,
   not an SEO change. Stop and investigate before interpreting traffic.
4. Landing page URL normalization: strip query strings/fragments, lowercase host, same
   trailing-slash policy on both sides, resolve to canonical.

### 1.4 Common data errors (all areas)
- Averaging CTR or position instead of re-deriving from sums.
- Comparing GSC clicks (clicks) with GA4 sessions as if equal.
- Using position from a page-grouped table as if per query.
- Reading the last 2 days as a drop.
- Ignoring the anonymized share, so "top queries" looks like the whole picture.
- Comparing 28 days with 28 days without aligning weekdays (use 28 or 91 days = whole weeks).
- Mixing search types, countries or devices across periods.
- Forgetting site-level events: migration, tracking change, GSC property change (domain vs URL
  prefix), Google core updates (check the dated list at https://status.search.google.com/ [P]).

---------------------------------------------------------------------------------------------
## 2. Keyword research and search intent classification

### 2.1 Purpose and principle
Keyword research quantifies demand and maps it to pages that can satisfy the task behind the query.
The Art of SEO treats keyword research as a core early chapter feeding content, architecture and
measurement [B]; Product-Led SEO adds that the unit of planning is the user job, with keywords as
evidence of the job [B].

### 2.2 Method steps
1. Seed set. Sources, in order of reliability:
   a. GSC queries of the client (already-earning demand, free, real).
   b. Client product/service taxonomy, internal site search, sales and support questions.
   c. Competitor ranking keywords (SERP / third-party index).
   d. Autocomplete, People Also Ask, related searches (SERP snapshots).
   e. Paid search term reports if available (conversion proof).
2. Expand: modifiers (best, vs, near me, price, how to, template, for <audience>), entity
   variants, misspellings, language/locale variants. Keep a `source` column on every keyword.
3. Normalize: lowercase, trim, singular/plural and stop-word variants into a "keyword group" only
   if their top-10 SERPs overlap (see 2.5), never by string similarity alone.
4. Enrich with metrics from the data layer:
   - search volume (provider estimate, monthly, per location/language),
   - keyword difficulty or SERP strength proxy (see 2.6),
   - CPC (commercial value proxy),
   - current rank and URL (GSC / SERP),
   - SERP features present, SERP intent labels (section 2.4),
   - trend (12-24 month volume series or GSC impressions).
5. Classify intent (2.3, 2.4).
6. Map keyword -> page (existing page, new page, or "no page": SERP does not want this format).
7. Size (section 9) and prioritize (section 10).
8. Write the keyword plan with owner, target URL, intent, volume, current position, target
   position, expected clicks, and review date.

### 2.3 Intent taxonomy (academic base)
- Broder 2002 [A]: navigational (reach a known site), informational (learn something, a set of
  results satisfies), transactional (reach a site where a further interaction happens: buy,
  download, sign up, search a database). Broder's AltaVista analysis found ranges of about 20-24.5%
  navigational, 39-48% informational, 22-36% transactional (as relayed by secondary summaries;
  verify against the paper before quoting) [A].
- Jansen et al. [A]: hierarchical sub-classes under each, automatic classifier with about 74%
  accuracy on a 1.5M query log; over 80% of web queries informational, about 10% each navigational
  and transactional in their sample. Lesson: automatic query-only classification tops out near
  three quarters correct, so SERP evidence must break ties.
- Practitioner 4-class mapping used in SEO tooling [H]: informational, navigational, commercial
  investigation, transactional. Treat as a UI label set; keep the academic 3 as the root.
- Modern additions [H]: local intent (map pack), freshness/news intent (QDF), visual/video intent,
  "do it with an AI answer" intent, brand-vs-category-vs-problem distinctions.

### 2.4 Classification procedure (query text + SERP evidence)
Step A. Rule features on the query:
- Navigational: contains a known brand/site/product-name or "login", "app", "download" for a
  brand; query equals an entity that owns a site.
- Transactional: buy, price, order, coupon, deal, near me, hire, quote, subscribe, free trial,
  download, pricing, cost, cheap.
- Commercial investigation: best, top, vs, review, alternatives, comparison, "for <use case>".
- Informational: what, why, how, guide, examples, tutorial, definition, symptoms, ideas.
Step B. SERP evidence (overrides step A when they conflict):
- Dominated by product/category pages or shopping units -> transactional.
- Dominated by listicles and comparison pages -> commercial investigation.
- Dominated by guides, forums, encyclopedic entries, videos, PAA -> informational.
- Brand's own sitelinks at position 1 -> navigational for that brand.
- Map pack present -> local intent.
- Mixed SERP (e.g., 4 of each type): label "mixed", create separate pages or choose the type that
  matches your page and flag the risk.
Step C. Store: label, confidence (0-1), evidence list (features + top-10 type counts), date.
SERP intent drifts, so re-label at least quarterly [H].

### 2.5 Keyword grouping by SERP overlap
- For two keywords A and B with top-10 URL sets SA and SB: overlap = |SA intersect SB| / 10.
- Heuristic [H]: overlap >= 3-4 of 10 shared URLs suggests one page can target both; <= 1 suggests
  separate pages. Most SEO tools use thresholds in this range; validate on 20 known pairs per
  site.
- Use URL-level overlap, not domain-level, for page decisions; domain overlap measures competitor
  sets.

### 2.6 Difficulty and feasibility
- Third-party "difficulty" scores are link-profile-biased proxies; they are not Google metrics.
  Prefer a transparent local model: median referring domains of ranking top-10 pages, share of
  top-10 held by strong brands, presence of exact-match/entity pages, freshness of top-10 content,
  your own authority in the same topic cluster (your GSC position for sibling queries) [H].
- Feasibility test via the client's own data: if the site already ranks 8-20 for 5+ siblings of the
  target keyword, feasibility is high; if there are zero impressions in the topic, treat as new
  territory with a long horizon [H].

### 2.7 Thresholds and rules of thumb [H unless stated]
- Long tail: individually low volume but collectively large; use group volume, not single keyword.
- Volume estimates carry high error; use ranges and prefer GSC impressions for owned queries.
- Do not drop a keyword for "volume 0-10" if intent is strongly commercial; low-volume exact
  matches can be the highest-converting pages.
- Branded demand is not an SEO growth target; separate it (section 5.7).

### 2.8 Data needed from the MCP server
- GSC: queries with clicks/impressions/ctr/position by page, device, country.
- SERP: top-10 organic URLs, feature flags, snippet text, PAA questions, related searches, AI
  answer presence, for the target location/language/device.
- HIST: keyword volume series and rank history.
- CRAWL: existing pages' titles/H1/word count for mapping.
- Provider volume and difficulty (with provider-agnostic field names per project constitution; the
  tool surface should never expose the vendor name).

### 2.9 Common analyst errors
- Treating volume as traffic (position-1 share of volume is nowhere near 100%; see section 9).
- Classifying intent from the query text only (74% ceiling [A]).
- Targeting one keyword per page when SERP overlap says the group is one topic, or merging
  keywords with different SERPs into one page.
- Ignoring locale: volume for "pizza" differs per country; always attach location and language.
- Chasing head terms with no product tie-in (Product-Led SEO critique [B]).
- Counting the same demand twice via overlapping keyword variants when summing opportunity.
- No refresh date on SERP-derived labels.

---------------------------------------------------------------------------------------------
## 3. Topic clustering and hub-and-spoke architecture

### 3.1 Concept
A topic cluster is a set of keywords/pages sharing the same underlying subject and user job. The
hub (pillar) page targets the broad topic and links to spoke pages that target specific
sub-intents; spokes link back to the hub and to relevant siblings. Value: internal linking
concentrates relevance, avoids cannibalization through explicit role assignment, and makes
reporting at topic level possible [H as a practice; The Art of SEO covers site architecture and
internal linking as core topics [B]].

### 3.2 Method
1. Collect candidate keywords (section 2) plus the site's current query set from GSC.
2. Cluster by SERP overlap (2.5), producing "page-level groups" (one future or current page each).
3. Aggregate groups into topics by semantic/entity similarity AND shared-ranking-URLs graph:
   build a graph where nodes = keyword groups, edge weight = shared top-10 URLs across groups;
   run community detection (Louvain/label propagation) [H].
4. Name each topic by its dominant entity + job ("compare VPN protocols").
5. Choose hub per topic: highest-volume broad group whose SERP type is a guide/category; the hub
   targets breadth, spokes target specific intents (how-to, comparison, template, price, local).
6. Check coverage: for each topic list intents from the taxonomy (2.3) and mark which have a
   page. Gaps = candidate new pages. Competitors' topic coverage (section 5) calibrates "complete".
7. Specify internal links: spoke -> hub with descriptive anchor (the hub's primary query form),
   hub -> every spoke, spoke <-> spoke only when the user would logically continue.
8. Measure at topic level: sum clicks/impressions across the topic's pages; impression-weighted
   position; count of ranking queries; share of topic demand captured.

### 3.3 Formulas
- Topic demand = sum over keyword groups of max(volume in group) (use group max, not sum, to avoid
  double counting).
- Topic share of voice = sum of expected clicks from your ranking positions / sum of expected
  clicks if position 1 for all groups [uses CTR curve, section 6.3].
- Coverage = groups with an assigned, indexable, ranking page / total groups.
- Link integrity: for each topic, % spokes linking to hub, % hub linking to spokes, orphan count
  (CRAWL).

### 3.4 Thresholds [H]
- A topic with fewer than ~5 distinct intent groups probably does not need a hub; one page with
  sections can win.
- Hub depth: keep spokes within 3 clicks of the home page; orphan pages = no internal inlinks.
- If two spokes' top-10 SERP overlap > 5 shared URLs, merge them.

### 3.5 Data needed
GSC (query-page map), SERP (overlap), CRAWL (internal link graph, anchors, depth, orphans),
HIST (topic time series).

### 3.6 Errors
- Clustering by string similarity (misses synonyms, merges homographs).
- Building a hub with no distinct intent (duplicate of a spoke).
- Linking everything to everything (dilution, no hierarchy).
- Reporting only page-level metrics; the cluster is the unit of strategy.
- Forcing a hub where the SERP rewards a tool, directory or template (Product-Led SEO view [B]).

---------------------------------------------------------------------------------------------
## 4. SERP feature analysis

### 4.1 Purpose
Features change who gets the click. A rank-1 organic result under an AI answer, shopping carousel,
and video pack can earn a fraction of the historic CTR. Analysis answers: what appears, who owns
it, what does it do to organic CTR, can we win the feature, and is the keyword worth the effort.

### 4.2 Method steps
1. Pull SERP snapshots for the target keyword set, per location/device/language (mobile and desktop
   differ).
2. Extract feature inventory per keyword: AI-generated answer/overview presence and cited
   sources, featured snippet (paragraph/list/table), People Also Ask, video pack, image pack, map
   pack, shopping/product units, top stories/news, sitelinks, knowledge panel, reviews/rich
   results, "discussions and forums", ads count and positions.
3. Pixel/position accounting: record the organic position of the first organic result in screen
   pixels or "organic rank index after features" [H]. A rank of 1 with 700 px of features above is
   effectively position 4-5 for attention.
4. Ownership: who holds each feature (domain), is it you, and how stable (repeat snapshots over
   2-4 weeks).
5. Win-ability per feature:
   - Featured snippet: you rank in top 10 (typically top 5 [H]) for the query and can answer
     concisely (40-60 word paragraph, list, or table) near a heading mirroring the query.
   - PAA: add question-form H2/H3 with concise answers; low click value, but brand coverage.
   - Video/image packs: only if production is feasible.
   - Map pack: depends on Business Profile, proximity, reviews, not on site SEO alone (The Art of
     SEO covers local search in its own chapter [B]).
   - Rich results: structured data eligibility; check Google's current eligibility docs at
     https://developers.google.com/search/docs/appearance/structured-data/search-gallery [P].
   - AI answers: no feature to "optimize" directly; track citation presence as a monitored
     metric and keep pages crawlable/snippet-eligible [H].
6. Estimate feature impact on CTR using the client's own GSC: compare CTR at the same position for
   queries with vs without the feature (needs SERP flags joined to GSC rows). Third-party studies
   say AI answers cut position-1 CTR by roughly half in some samples (a 2025 study reported -58%
   for position 1 in December 2025; other studies reported 47-65% falls) [H, access 2026-10-02,
   https://ahrefs.com/blog/ai-overviews-reduce-clicks-update]. Use as a prior, fit locally.
7. Decide: pursue feature, pursue organic only, pursue different keyword, or deprioritize
   (zero-click risk).

### 4.3 Formulas
- Feature-adjusted expected CTR(p) = base_CTR(p) * (1 - feature_penalty), penalty estimated per
  feature class from the client's GSC; default penalties flagged [H] and shown as ranges.
- Feature opportunity score = P(win) * incremental clicks * value per click. P(win) from rank
  proximity and content fit, not from optimism: 0.5 if already in top 3 with matching format, 0.2
  if rank 4-10, 0.05 otherwise [H priors].
- Zero-click risk = share of keyword group whose GSC CTR at position <= 3 is < 5% [H threshold].

### 4.4 Data needed
SERP (features with owners, repeated snapshots, location/device), GSC (clicks/impressions/position
by query, searchAppearance for rich results), CRAWL (structured data presence, heading structure,
answer-like passages), HIST (feature presence over time).

### 4.5 Errors
- Reading position as attention position ignoring features above.
- Single snapshot conclusions (features and personalization vary; take 3+ snapshots).
- Ignoring device (mobile SERPs are feature-heavier).
- Chasing a featured snippet that cannibalizes a high-CTR organic listing (when the snippet answers
  fully, clicks can fall) [H].
- Treating AI answer citations as clicks.
- Naming the SERP-data vendor in client deliverables (violates capability-based naming principle
  in the project constitution).

---------------------------------------------------------------------------------------------
## 5. Competitive and content-gap analysis

### 5.1 Define the competitor set properly
- Business competitors (sell the same thing) differ from SERP competitors (appear for your target
  queries: publishers, marketplaces, forums, Wikipedia). Build both lists.
- SERP competitor discovery: for the client's top N keywords (N 100-500), count domain appearances
  in top 10, weight by position (e.g., weight = 11 - rank). Rank domains by weighted frequency.
- Pick 3-5 direct and 3-5 SERP competitors; exclude giants that never compete for the content
  type (list them separately as "ceiling benchmarks").

### 5.2 Keyword gap
1. Competitor ranking keywords (SERP/third-party index) vs your GSC queries.
2. Gap types:
   - Missing: competitor ranks top 20, you have no impressions.
   - Weak: you rank 11-50, competitor top 10.
   - Strong shared: both top 10 (defend).
   - Unique: you only (protect, check why).
3. Filter: intent matches your offer, volume above floor, difficulty achievable (2.6), not
   brand-of-competitor queries.
4. Aggregate to topics (section 3) to see strategic gaps, not just keyword lists.

### 5.3 Content gap (page-level)
For the target query with top-3 competitor pages:
- Format (guide, tool, comparison, category), length, headings, entities mentioned, questions
  answered, media, structured data, freshness date, internal links in.
- Compute entity/subtopic coverage matrix: rows = subtopics extracted from top-10 pages (heading
  text clustering), columns = pages; mark covered. Subtopics covered by >= 60% of top-10 pages and
  missing from yours are table stakes [H]; subtopics covered by < 20% but valuable are
  differentiators.
- Content gap is a hypothesis, not proof of cause: ranking depends on links, brand, UX; state
  confidence accordingly.

### 5.4 Link gap (when relevant)
Referring domains pointing to >= 2 of 3 competitors but not you are outreach targets; check
relevance and link type (editorial, directory, resource). The Art of SEO treats link building as a
major chapter [B]. Use referring-domain counts and topical relevance, not a single authority score.

### 5.5 SERP share of voice
SoV(domain) = sum over keywords (volume_k * CTR(position_k)) / sum over keywords volume_k, with
CTR from the curve in 6.3. Track monthly with fixed keyword set. Weighted visibility index, not
real traffic.

### 5.6 Technical/competitive parity check (CRAWL on competitors, public pages only)
Page types and templates, indexed page count estimate (site: operator is rough), speed signals,
structured data types used, internal link structure of top pages.

### 5.7 Product-led lens [B]
Ask which competitor assets earn links and rankings that are not articles: calculators, templates,
directories, data pages, integrations. If the leaders are tool pages, content-gap = tool-gap.

### 5.8 Thresholds [H]
- Gap keyword is "worth a page" if group volume >= max(50, 0.5% of topic demand) and it matches a
  funnel stage where the client converts.
- A competitor's top-10 page is "beatable" if >=3 of the 10 are weak (low referring domains,
  stale > 24 months, thin) or are forums/Q&A (signals unsatisfied intent).

### 5.9 Data needed
SERP (top-10 across keyword set), competitor keyword index (provider-agnostic), CRAWL (client and
competitor pages), backlink data (referring domains), GSC (client baseline), HIST.

### 5.10 Errors
- Gap lists with thousands of rows and no prioritization or topic grouping.
- Comparing with the wrong competitor (huge brand, different intent).
- Assuming gap = opportunity: many gap keywords are off-strategy.
- Using competitor traffic estimates as facts; they are modeled [H].
- Ignoring that competitor rankings may come from links/brand, not content.
- Copying competitor structure instead of surpassing the SERP's unmet need.

---------------------------------------------------------------------------------------------
## 6. GSC analysis toolkit

### 6.0 Standard extracts
- E1: date x total (no other dimensions) for accurate totals [P].
- E2: query x page x date (weekly granularity for retention) for diagnostics, truncated by
  the 50K rows/day cap [P], so store daily and mark truncation.
- E3: page x date; E4: query x date; E5: country x device x date; E6: searchAppearance.
- Always store raw daily rows; derive weekly/monthly in the server, never overwrite raw data.
- Compare windows: last 28 days vs previous 28 days; last 28 vs same 28 days last year (aligned
  weekdays); exclude the last 2-3 preliminary days [P].

### 6.1 Striking-distance queries
Definition [H]: queries where the site ranks roughly 8-20 (often 11-20 for "page 2"), with enough
impressions that moving to top 5 yields meaningful clicks.
Steps:
1. Take (query, page) rows for the last 90 days (stabilizes position; avoid 7-day noise).
2. Keep rows with impressions >= threshold (default 100 over 90 days; scale with site size; at
   least 30 [H]) and position between 8 and 20.
3. Exclude branded queries (6.7) and queries whose best page is irrelevant (check intent).
4. Estimated gain = impressions * (CTR_curve(target_pos) - CTR_actual).  Target position 3 (or 5)
   as default scenario; show conservative and optimistic.
5. Diagnose why it is stuck: (a) page is not the best intent match (cannibalization candidate),
   (b) thin content vs SERP leaders, (c) title/meta not aligned, (d) weak internal links, (e)
   weak external links, (f) SERP has features pushing organic down.
6. Action list per row: refresh content, adjust title, add internal links from topically related
   high-authority pages, add sections for missing subtopics, consolidate competing URLs.
Position bands to be careful with: position 11-20 averaged over impressions can hide a query
swinging between rank 4 and rank 40 (check daily variance; if std of daily position > 8 [H] it is
unstable, not "page 2").

### 6.2 Position-1-to-3 low CTR (title/snippet opportunity)
Rows with position <= 3 and CTR below the expected band at that position (use site-specific curve
6.3, flag if actual < 0.6 * expected [H]) and impressions >= threshold. First check SERP
features above (4). Then snippet: title mismatch, truncated title, missing brand/benefit, date
absent, rich results missing.

### 6.3 CTR curves by position
Method:
1. Build the site's own curve: for each rounded position bucket (1,2,...,10, then 11-20), compute
   CTR = sum clicks / sum impressions over non-branded, non-feature-affected queries, desktop and
   mobile separately, and by intent type.
2. Require >= 1,000 impressions per bucket [H] or merge buckets; report confidence interval:
   CTR +/- 1.96 * sqrt(CTR*(1-CTR)/impressions) (binomial approximation, assumes independence;
   conservative flag when queries cluster).
3. Remove branded queries; they inflate position-1 CTR (branded CTR can exceed 50-70% [H]).
4. Compare with published curves only as sanity checks:
   - 2025 industry summaries report position 1 organic CTR around 19% (down from ~28% in 2024),
     position 2 ~12.6%, with lower-position ranks 6-10 rising ~30% in one study as users shifted
     [H, source: https://marketing4ecommerce.net/en/seo-organic-positioning-no-longer-guarantees-success/
     and Ahrefs summary above, accessed 2026-10-02]. These are dataset-specific; do not use them as
     defaults for a client without a local fit.
5. Use the curve for: sizing (section 9), CTR-anomaly detection, SoV (5.5), forecasts.
6. Refit quarterly; CTR drifts with SERP layout and AI answers.

Position caveat: GSC position is the average of topmost-result positions; bucketing by rounded
average position blurs the curve. For crisp curves use daily data and queries with low position
variance, or use SERP-tracker exact ranks.

### 6.4 Cannibalization
Definition: more than one URL of the same site competing for the same query intent, causing
alternating rankings, diluted signals or lower combined performance. Not every multi-URL query is
cannibalization: sitelinks-style double listings can be healthy [H].
Detection:
1. For each query in the last 90 days, list URLs with impressions share. Candidate if >= 2 URLs
   each with >= 20% of query impressions [H] and query impressions >= 100.
2. Time pattern: plot URL share by week; flip-flopping (URL A top, then B, then A) is the signature
   of true cannibalization.
3. Intent check: are both URLs satisfying the same intent per SERP (2.4)? If one is informational
   and one transactional and the SERP is mixed, it may be legitimate.
4. Impact: compare combined clicks to the best-URL-only counterfactual; look at average position
   of the pair vs the best single URL.
Actions: consolidate (merge + 301 + update internal links), differentiate (retarget one URL to a
different intent), canonicalize (when duplicates), de-optimize internal anchors pointing at the
wrong URL, or accept.
Errors: assuming any multi-URL query is harmful; consolidating pages that serve different
intents; ignoring that query-level GSC rows are truncated and anonymized (some competition is
invisible).

### 6.5 Query and page decay (content decay)
Method:
1. Weekly clicks per page over >= 13 months (use HIST beyond 16).
2. Seasonally adjust: compare to same weeks last year (YoY) or fit a seasonal decomposition
   (STL; period 52 weeks) [H].
3. Flag decay when trailing 12-week clicks are < 70% of prior 12-week clicks AND YoY decline > 20%
   AND impressions stable or falling (CTR/position diagnosis below) [H thresholds].
4. Diagnose with the 2x2 on (impressions, position, CTR):
   - impressions down, position flat: demand fell (seasonality, trend) or SERP features
     removed visibility;
   - position down, impressions flat/up: lost ranking (competitors, staleness, technical,
     update);
   - CTR down, position flat: snippet/feature change (AI answer, new feature, competitor titles);
   - all flat but clicks down: tracking or data issue; check totals E1.
5. Action: refresh with new data, add missing subtopics, re-promote internally, update date
   honestly, fix technical, or retire/redirect.
6. Prioritize with section 10 using recoverable clicks = (prior clicks - current clicks).

### 6.6 Seasonality
- Use >= 2 full years (HIST) to estimate a seasonal index per query/topic:
  index_m = mean(clicks in month m) / mean(clicks across months) per year, averaged across years.
- Detrend before seasonal estimate when growth is strong (log transform, STL or ratio-to-moving
  average).
- Compare Google Trends-style relative demand only as a cross-check [H].
- Use: schedule content refreshes 8-12 weeks before peaks [H], baseline expectations, avoid
  mis-reading seasonal dips as decay, and time experiments away from peaks.
Errors: using 16-month GSC window for annual seasonality (only ~1.3 cycles); comparing a peak
month with a trough month; calendar-month comparisons without day-count/weekday normalization.

### 6.7 Brand vs non-brand
1. Build the brand regex: brand name, spelling variants, misspellings, domain name, product names
   (include anchored variants). GSC supports regex filters ("custom (regex)") using RE2 syntax
   [P, https://support.google.com/webmasters/answer/7576553 area docs; verify exact page]. Example:
   `(?i)(acme|ac me|acmee|acme\.com)` ; exclude with the "doesn't match regex" option.
2. Classify each query: brand, brand+modifier (navigational-ish), generic-non-brand, competitor.
   Maintain the regex as a versioned config; review the top 200 queries manually first.
3. Report separately: brand clicks reflect existing awareness (and ads, PR, offline); non-brand
   reflects SEO acquisition. Total traffic growth driven by brand is not SEO growth.
4. Metrics: non-brand clicks, non-brand impressions, non-brand share of total, non-brand
   average position (impression-weighted), non-brand CTR by bucket.
5. Anonymized queries bias the split: brand queries are rarely anonymized (high volume), so
   non-brand is under-observed; report the hidden share.
6. Branded demand tracking as a leading indicator of off-site marketing effect.

### 6.8 Other useful GSC analyses
- Query-intent mix: classify top queries (2.4) and report clicks by intent.
- Question queries: filter regex `^(how|what|why|when|where|who|which|can|does|is|are)\b` for
  informational content ideas [H].
- Page-level indexing health: pair performance data with the URL Inspection and indexing reports
  (Pages report): impressions=0 pages that should rank.
- Country/device skews: mobile vs desktop CTR/position; wrong-country ranking (hreflang issue).
- New vs lost queries: queries with impressions in period B but not A and vice versa (list sizes,
  clicks affected).
- Page groups: group URLs by template with regex (e.g., `/blog/`, `/category/`), report per
  template; the basis of split tests (section 12).
- Update-impact windows: compare 14 days before and 14-28 days after a dated core update, by
  query class; avoid attributing without controls.

### 6.9 Data needed from the MCP server
GSC (E1-E6, daily, with dataState), HIST (>16 months), SERP (exact rank and features for
striking-distance validation), CRAWL (page content/titles for diagnosis), brand regex config.

### 6.10 Errors (GSC specific)
- Treating position as precise rank.
- Declaring decay from a short noisy window or from preliminary data.
- Using query data as complete (anonymization + 50K cap).
- Running one huge multi-dimension query and trusting totals.
- Ignoring brand inflation in CTR curves.
- Calling "page 2" opportunities without checking intent match.
- Counting a URL change (migration) as lost queries.

---------------------------------------------------------------------------------------------
## 7. GA4 for SEO

### 7.1 Core report for organic landing pages
Dimensions: landing page + query string (cleaned), session source/medium (google / organic),
device category, country. Metrics: sessions, engaged sessions, engagement rate, average engagement
time per session, key events and key event rate, revenue (if ecommerce), new users.
Join to GSC by normalized URL (1.3).

### 7.2 Method steps
1. Filter to session source = google AND medium = organic for GSC comparability; separately view
   the full Organic Search channel.
2. Landing page table: sessions, engagement rate, key event rate, revenue/session. Segment by
   template (page groups).
3. Opportunity combos:
   - high GSC clicks + low engagement rate -> intent/UX mismatch or tracking issue;
   - high engagement + low key event rate -> weak CTA/offer or conversion not instrumented;
   - high key event rate + low traffic -> scale candidates (rankings, internal links);
   - sessions decreasing while GSC clicks flat -> consent/tracking change.
4. Engagement as content-quality proxy: compare to site median by template; use engagement rate
   (>10 s, 2 views, or key event [P]) with care: a short informational answer page can satisfy
   users and still show low engagement. Prefer average engagement time and scroll/CTA events for
   decisions [H].
5. Conversion analysis: key events attributed by landing page ("session-scoped" landing page
   dimension) and by first user source for acquisition views. Use assisted paths in the
   attribution reports for the role of organic in multi-touch journeys (organic often assists
   rather than closes).
6. New-vs-returning for organic: organic as discovery vs navigation.
7. Site search and internal search terms: unmet content demand.
8. BigQuery export for depth: event-level queries, no thresholding, ability to compute
   landing-page-level conversion paths and custom attribution; remember export vs UI differences
   [P].

### 7.3 Attribution limits (must be stated in reports)
- Session attribution is last non-direct click; organic receives credit only when it is the last
  non-direct touch. Direct, email, or paid later in the path steal credit.
- Cross-device and consent loss undercount organic; "(not set)" landing page appears for some
  sessions.
- GSC clicks and GA4 sessions differ for legitimate reasons (consent, bounces before load,
  redirects, bots, multiple clicks, in-app browsers).
- GA4 does not give per-query data for organic (keyword is not provided); queries come only from
  GSC at the page level. Query-to-conversion linkage is therefore modeled via landing page [H].
- Imported GSC data in GA4 (Search Console integration) is a separate report set with its own
  limitations; do not mix with session metrics.
- Data retention for event-level data in the UI Explorations is limited (2 months default or up to
  14 months if configured) [H, verify in Admin docs]; use BigQuery export for long history.
- Thresholds and sampling in Explorations: report when they apply [P 9383630].

### 7.4 Formulas
- Organic conversion rate (landing page) = key events from organic sessions landing there /
  organic sessions.
- Value per organic session = revenue (or lead value * lead rate) / sessions.
- Click-to-session ratio = GA4 organic sessions / GSC clicks (monitoring only).
- Expected organic conversions from a rank change = delta clicks * landing page conversion rate
  (use the conversion rate observed over >= 100 sessions or shrink toward the template median:
  CR_shrunk = (conv + k * CR_template) / (sessions + k), k around 100 [H]).

### 7.5 Data needed
GA4 Data API or BigQuery (landing page, source/medium, key events, revenue, engagement metrics),
GSC (for join), CRAWL (templates and CTAs), HIST.

### 7.6 Errors
- Using bounce rate as in UA; GA4 bounce is just 1 - engagement rate [P].
- Reading engagement rate on small samples; thresholds and noise.
- Treating "Organic Search" as only Google.
- Judging informational pages on conversion directly (they feed assisted conversions).
- Landing page URL join without normalization, or counting sessions where the landing page is
  (not set).
- Using Explorations with high-cardinality dimensions leading to "(other)" rows.
- Claiming SEO caused revenue without a control (see section 12).

---------------------------------------------------------------------------------------------
## 8. Forecasting

### 8.1 Philosophy
Forecasts are ranges with assumptions, not promises. A good forecast lists: baseline, assumptions,
levers, scenarios (conservative/base/optimistic), update cadence, and the metrics that falsify it.

### 8.2 Baseline (do-nothing) forecast
1. Series: weekly non-brand clicks (or topic-level), >= 2 years where possible (HIST).
2. Model: seasonal-trend decomposition or Holt-Winters / ETS / Prophet-style additive model
   [H]; fit on log clicks when variance grows with level.
3. Backtest: hold out the last 12 weeks, compute MAPE/sMAPE; require MAPE < 20% [H] for a
   topic-level forecast, otherwise widen intervals and say "directional".
4. Prediction intervals at 80% and 95%; report 80% to clients.
5. Exclude anomaly weeks (migration, outage, core update) or model them as intervention dummies.

### 8.3 Incremental (uplift) forecast from actions
Bottom-up opportunity model per keyword/page group:
- Current clicks_c; impressions I (use average monthly, de-seasonalized);
- Target position p_t (scenario), CTR_curve(p_t) from 6.3, share of volume reachable;
- Incremental clicks = I * CTR(p_t) - clicks_c, with ramp.
- Ramp (time to effect) [H]: content refresh on existing URL 4-12 weeks to first signal; new page
  indexing days to weeks and ranking maturity 3-6+ months; technical fixes a few weeks after
  recrawl; link-driven gains months. State the lag as an assumption, not a guarantee.
- Probability weighting: expected incremental clicks = P(reach target) * incremental clicks.
  P from historical hit rate of similar actions on this site (track it in HIST: of N refreshes,
  how many improved position by >= 3 within 90 days). Default priors [H]: 0.3-0.5 for refresh
  within striking distance, 0.1-0.25 for new pages on competitive terms.
- Add cannibalization correction: if the page replaces others, subtract existing clicks for
  replaced URLs.
- Add feature correction (section 4.3) and AI-answer exposure.

### 8.4 Conversions and revenue
Incremental conversions = incremental clicks * CR(landing group). Revenue = conversions * AOV (or
lead value * close rate). Show in ranges. Payback = cost / monthly incremental gross margin.

### 8.5 Sizing the market: TAM/SAM/SOM for search
- Total addressable search demand = sum of group volumes (deduplicated) for the topic across
  intents the client serves.
- Serviceable = those where the client could rank (difficulty and fit).
- Obtainable = serviceable * achievable CTR share in the planning horizon (e.g., SoV target).
- Always show click potential at positions 1, 3, 5 so stakeholders see the sensitivity.

### 8.6 Pre-sales / prospect sizing (no GSC access)
Use SERP + provider volumes + CRAWL to estimate: current visibility (SoV vs competitors), total
demand in the prospect's topics, gaps, and quick wins. Label all numbers "modeled, +/- wide".
Never present modeled traffic as measured. Ask for GSC read access in the next step to replace
estimates.

### 8.7 Errors
- Linear extrapolation of a seasonal series.
- Forecasting on 16 months or fewer and presenting annual seasonality.
- Using volume x position-1 CTR as the opportunity (ignores features and branded mixing).
- Compounding best-case assumptions; no probability weighting.
- No holdout/backtest, no interval.
- Counting the same impressions in multiple opportunities.
- Promising a date for rankings.
- Ignoring demand decline (AI answers, market shrink) in the baseline.

---------------------------------------------------------------------------------------------
## 9. Opportunity sizing recipes (quick reference)

| Opportunity | Formula | Inputs |
|---|---|---|
| Striking distance | I90/3 per month * (CTR(3 or 5) - CTR_now) | GSC query-page 90 d, curve |
| Low-CTR top positions | I * (CTR_expected - CTR_actual) | GSC, curve |
| New page for gap keyword group | volume_group * CTR(p_t) * P(reach) | provider volume, SERP, difficulty |
| Content refresh (decayed) | clicks_prior - clicks_now, discounted by P(recover) | HIST |
| Cannibalization fix | clicks(best single URL position) - clicks(combined now), if positive | GSC pairs |
| Featured snippet | I * (CTR_snippet - CTR_current) * P(win) | SERP features, local CTR |
| Internal linking boost | uplift estimate from split test or analog; mark [H] | CRAWL, GSC |
| CTR / snippet rewrite | I * delta CTR, delta from test (section 12) | GSC |
Rules: net of overlaps (do not add up opportunities that touch the same query); show low/base/high;
report time-to-effect.

---------------------------------------------------------------------------------------------
## 10. Prioritization frameworks

### 10.1 ICE / RICE variants (impact x confidence / effort) [H]
- ICE: score = Impact * Confidence * Ease (each 1-10), or Impact * Confidence / Effort.
- RICE (Reach, Impact, Confidence, Effort): score = Reach * Impact * Confidence / Effort, from
  product management (Intercom), well suited since Reach = affected impressions/clicks.
SEO mapping:
- Reach = monthly impressions or clicks touched (from GSC).
- Impact = expected relative change in clicks or conversions (from section 9, not a guess).
- Confidence = evidence level, calibrated:
  - 1.0: prior test on this site showed it (section 12);
  - 0.8: strong documented causal mechanism + analogous wins;
  - 0.5: plausible, mixed evidence;
  - 0.2: speculative.
- Effort = person-days (content, dev, design), plus risk (migration) as a multiplier.
- Add a value multiplier for business fit (money pages x2, informational x1) [H].

### 10.2 Scoring procedure
1. List candidate actions with owner, URLs affected, hypothesis (section 14).
2. Compute expected incremental value EV = Reach_effective * Impact * Confidence * value_per_click.
3. Priority = EV / Effort (value per person-day). Sort descending.
4. Constraints: dependencies (technical fixes before content on the same templates), capacity,
   seasonal windows, risk.
5. Portfolio balance: reserve ~70% quick/known wins, ~20% growth bets, ~10% experiments [H
   heuristic].
6. Recheck monthly: update confidence from realized results (Bayesian-ish: success rates per
   action type on this site).

### 10.3 Triage buckets
- Fix now (blocking: indexation, noindex errors, broken canonical, 5xx, traffic-draining
  redirects).
- Quick wins (titles for top-3 low CTR, striking distance refresh, internal links).
- Strategic builds (clusters, tools, programmatic pages).
- Monitor (insufficient data).
- Stop doing.

### 10.4 Errors
- Impact based on volume rather than expected clicks.
- Confidence inflated by tool recommendations (tool "issues" are not impact).
- Ignoring effort of reviews and approvals.
- Forgetting dependency order (publishing content before fixing crawl/indexation).
- Prioritizing by ease only (low-impact backlog).
- No re-scoring after results.

---------------------------------------------------------------------------------------------
## 11. KPI trees and reporting

### 11.1 KPI tree (outcome -> drivers -> levers)
Level 0 Business outcome: organic revenue / leads / signups (GA4 key events with value).
Level 1: Organic conversions = organic sessions * conversion rate.
Level 2 (traffic): organic sessions ~ GSC clicks * click-to-session ratio.
Level 3: clicks = impressions * CTR. Impressions = sum over queries of demand * visibility (rank,
indexed pages). CTR = f(position, SERP features, snippet quality).
Level 4 (levers): number of indexed useful pages, coverage of intents, rankings by position band,
snippet quality, SERP feature wins, brand demand.
Level 5 (leading indicators): crawl and indexing health, impressions on new pages, rank
distribution movement (count of queries in top 3/top 10), internal link coverage, referring
domains growth, Core Web Vitals pass rate (CrUX).
Conversion branch: landing page conversion rate = f(intent match, offer, UX, speed).
Always separate brand vs non-brand at level 2-3.

### 11.2 Metric hygiene
- Lagging: conversions, revenue, clicks. Leading: impressions, rank distribution, indexed pages,
  links, crawl rate.
- Normalize for seasonality (YoY) and for working-day count.
- One north-star: non-brand organic conversions (or qualified clicks if conversion data is poor).
- Rank distribution: counts of tracked queries by band (1-3, 4-10, 11-20, 21-50, 51+), more
  robust than "average position".
- Visibility index = SoV (5.5).
- Quality: indexed vs submitted ratio (Pages report), % of pages with impressions, % of pages
  with clicks.

### 11.3 Monthly report structure (agency standard) [H, synthesized]
1. Executive summary (<= 150 words): outcome vs target, 3 wins, 3 risks, decisions needed.
2. Scorecard: KPIs with MoM and YoY, target, status (traffic-light with rule thresholds
   pre-agreed, e.g., green >= target, amber 80-99%, red < 80%).
3. Traffic and conversions: non-brand vs brand; top gaining/losing pages and queries with
   explanations (not just lists).
4. Rankings and visibility: rank distribution, SoV vs competitors, SERP feature wins/losses.
5. Work done: actions shipped with URLs and dates; link to hypotheses (section 14).
6. Results of experiments/changes: test, expectation, outcome, decision.
7. Technical health: indexation, errors, CWV, crawl budget notes, changes since last month.
8. Next month plan: prioritized backlog (10.2), expected impact and leading indicators, owners.
9. Appendix: methods, data caveats (sampling, anonymization, freshness), definitions.
Rules: explain variance (not dashboards only); each chart answers one question; every claim traces
to data; forward-looking items state their falsifiers.

### 11.4 Anomaly explanation checklist (when traffic moves)
1. Data issue? (E1 totals, GA4 ratio, tracking change, preliminary data)
2. Seasonal? (YoY, index)
3. Site change? (deploys, migration, robots/noindex, canonical, templates, CMS update)
4. Indexation/crawl? (Pages report, server logs, response codes)
5. Competitor/SERP change? (SERP snapshots, features, new entrants)
6. Google update? (dated status page) [P]
7. Demand change? (impressions vs position vs CTR 2x2 from 6.5)
8. Brand/PR/ads effect? (brand vs non-brand)
Report the evidence for/against each, ranking by likelihood.

### 11.5 Pre-sales audit (prospect, limited access) structure
1. Context and goals (one question list to the prospect).
2. Visibility snapshot: SoV vs 3-5 competitors on 100-300 target keywords (SERP).
3. Technical crawl summary (CRAWL): index/canonical/robots issues, template inventory, speed
   proxies, structured data, internal link depth, duplicate/near-duplicate clusters.
4. Content and topic coverage vs competitors (sections 3, 5).
5. Quick wins list (5-10) with expected impact ranges and effort.
6. Opportunity sizing (8.5, 8.6) with explicit modeled-data label.
7. Proposed 90-day plan, KPIs, falsifiers, and data requests (GSC, GA4 read access).
8. Risks and assumptions (migration risk, AI answers, YMYL, resources).
Keep it to ~10 pages; the aim is a decision, not an inventory.

### 11.6 Errors
- Vanity KPIs (rank for 5 hand-picked keywords, total impressions).
- Not separating brand.
- Reporting tool "SEO scores" as outcomes.
- Monthly noise explained with stories without checks.
- Reports with no next step or no owner.
- Promising ranks; reporting only wins.

---------------------------------------------------------------------------------------------
## 12. Experimentation: SEO split tests and causal inference

### 12.1 Why SEO tests differ
Google cannot be randomized per user; randomization is across PAGES (a template group) not
visitors. Treatment is applied to a subset of similar pages, controls untouched; the outcome is
organic clicks/sessions per page over time. Typical durations 2-8 weeks given crawl and ranking
lag [H, source https://inblog.ai/glossary/seo-split-testing and tool vendors, 2026-10-02].

### 12.2 Requirements
- Large page population on a shared template: [H] at least ~100 pages per arm with decent
  traffic, or the test lacks power; thousands is better. Total test+control organic sessions of at
  least a few thousand per week [H].
- Same intent/seasonality profile across arms; randomize after stratifying by pre-period traffic
  (stratified randomization / matched pairs).
- No concurrent changes to the tested template or links; freeze the test group.
- Technical: change shipped identically (server-side, so Googlebot sees it); avoid
  cloaking-like differences between bot and users.

### 12.3 Procedure
1. Hypothesis (section 14): "Adding X to title of category pages raises CTR at stable position".
2. Choose metric: clicks (GSC) as primary; secondary impressions, CTR, position, sessions,
   conversions. Pre-register the primary metric and the decision rule.
3. Sample: all pages in the template; remove outliers and brand-dominant pages; require pre-period
   traffic > minimum.
4. Randomize: sort by pre-period clicks, pair adjacent, assign randomly within pairs.
5. Pre-test checks: A/A test (no change) over the pre-period to measure the false positive rate
   and the distribution of the metric; verify parallel trends (correlation of arm series > 0.9
   [H]).
6. Run: apply treatment; keep running until the pre-specified length (minimum 2-3 weeks plus
   ramp; do not peek and stop on first significance).
7. Analyze: counterfactual model.
   - Difference-in-differences (simple) or
   - CausalImpact (BSTS) [A: https://arxiv.org/abs/1506.00356]: model the treatment arm's daily
     clicks as a function of control arm series (synthetic control) + local trend + seasonality;
     predict the counterfactual after the intervention; the pointwise and cumulative difference
     with credible intervals estimates the causal effect. Assumptions: control series not
     affected by the intervention; relationship between treatment and control is stable over time;
     covariates predicted well in the pre-period [A]. Practical SEO walk-through:
     https://www.jcchouinard.com/causalimpact-for-seo [H].
   - Alternative: per-page clicks regression with page fixed effects and time fixed effects
     (two-way fixed effects), clustered by page.
8. Decision: ship if the credible interval for relative lift is above 0 and above a minimum
   effect (practical significance), or interval excludes a harm threshold; else "no effect" or
   "inconclusive" (report power).
9. Log in HIST: test id, arms, dates, effect, interval, decision, learned prior for confidence in
   section 10.

### 12.4 When you cannot split (small sites, one page)
- Before/after with synthetic control from similar pages or from GSC segments unaffected by the
  change; use CausalImpact with several controls (non-affected pages, non-brand impressions of
  other topics, same query on another country if applicable).
- State low confidence; treat as evidence, not proof.
- Time-series interventions to avoid: coinciding core updates, seasonality shifts, holiday weeks.
- Use an "event study" plot: lift by week since change.

### 12.5 Power and effect size
Minimum detectable effect depends on variance of the pre-period residuals. A rough rule from A/A
tests: simulate fake treatments on historical data to estimate MDE at 80% power [H]. SEO traffic is
noisy; 5-10% lifts are hard to detect unless the sample is large [H].

### 12.6 Metric choices
- Clicks per page (GSC) mixes impressions and CTR; for title tests CTR conditional on position is
  the mechanism metric, but clicks is the decision metric.
- For ranking tests (content/links) impressions and position are leading signals.
- Sessions from GA4 are noisier and attribution-limited; use for secondary checks.

### 12.7 Common errors
- Comparing before/after without control (confounded by seasonality, updates).
- Unequal groups, not stratified, or arms with different trends.
- Peeking and stopping early.
- Changing multiple things at once on the treatment arm.
- Interference: internal links between arms, shared templates, cannibalization across arms.
- Ignoring that Google may roll out SERP changes mid-test (affects both arms; difference-based
  estimates are robust, absolute ones are not).
- Treating a non-significant result as "no effect" with no power analysis.
- Over-generalizing a test on one template to another.
- CausalImpact on a single pair with poor pre-period fit (check pre-period MAPE and posterior
  predictive checks).

---------------------------------------------------------------------------------------------
## 13. Technical-adjacent analyst checks (data-side, used to qualify other analyses)
Included because most analyses depend on these facts being true.

- Indexation: submitted vs indexed (Pages report), pages with impressions / pages in sitemap;
  pages with zero impressions over 90 days that matter -> investigate (quality, duplicate,
  canonical, discovered-not-indexed).
- Canonical/redirect consistency: CRAWL check that canonical target returns 200 and is indexable;
  redirect chains > 1 hop flagged.
- Duplicates: near-duplicate clusters by shingled content hash; parameter URLs consuming
  crawl.
- Internal links: inlinks count, anchor text diversity, depth, orphan pages; link equity
  modeling with PageRank-style calculation on the internal graph [H]: PR = (1-d)/N + d * sum(PR_j
  / outdeg_j), d = 0.85 as a classic default.
- Performance: CrUX field data (LCP <= 2.5 s, INP <= 200 ms, CLS <= 0.1 at p75 are Google's
  "good" thresholds [P, https://web.dev/articles/vitals]) -- verify current values at that URL.
- Mobile parity, structured data validity, hreflang reciprocity, robots.txt blocking of assets.
- Logs (if available): Googlebot hit rate by template, crawl waste share.
Errors: treating tool "issue counts" as impact; auditing without checking what actually has
impressions; ignoring templates.

---------------------------------------------------------------------------------------------
## 14. Falsifiable recommendations (project principle: 4 fields)

Every recommendation (and plan item) is written with four fields. The constitution requires them;
this section defines how to fill them well.

1. OBSERVATION (first-principle observation): a measured fact with source and window, stated
   without interpretation. Pattern: "[Metric] for [scope] is [value] over [dates] (source: GSC
   query-page, 90 d, final data), versus [benchmark/prior period/expected]." Include sample size.
2. DEPENDENCY: what must be true for the action to work, and what must happen first. Include
   technical prerequisites (indexable, crawlable), resource prerequisites, external dependencies
   (SERP intent unchanged, no core update in the window), and other recommendations it depends
   on.
3. FALSIFICATION CHECK: the specific test that would prove the hypothesis wrong, defined in
   advance: metric, scope, control, window, threshold. Pattern: "If, N weeks after shipping,
   [metric] on [scope] has not exceeded [control/baseline] by at least [x%] (credible interval
   excludes zero), the hypothesis is rejected and we revert/redirect effort." Must be possible to
   fail; "traffic will improve" is not falsifiable.
4. LEADING INDICATOR: an early signal (days to 2-3 weeks) that predicts the lagging outcome, with
   an expected direction and threshold, and the data source. Examples: indexation of the page in
   <7 days; impressions on target queries rising within 2 weeks; CTR on the page-query pair; rank
   distribution moves into top 10; internal inlink count; crawl hits.

### 14.1 Quality rubric (score each 0-2)
- Observation: cites source/window/n? quantified?
- Dependency: explicit, ordered, checkable?
- Falsification: has metric, control, window, threshold, and a pre-committed action on failure?
- Leading indicator: measurable earlier than the outcome, different from the outcome, with a
  threshold?
- Reject recommendations scoring < 6/8.

### 14.2 Template
```
ID: R-014
Recommendation: Rewrite titles of /category/ pages ranking 1-3 with CTR below site curve.
Observation: 48 category pages, 120k impressions in 90 d (GSC, dataState=final), avg position 2.4,
  CTR 3.1% vs site curve 8.9% at position 2-3 (95% CI 8.4-9.4). 31 pages show a rich-result-less
  snippet; no AI answer on 70% of sampled SERPs.
Dependency: titles editable via template (dev 2 d); no competing URL (cannibalization check
  done, none); SERP features unchanged during test; GSC access continues.
Falsification: split test, 24 treated vs 24 control (stratified), 4 weeks, primary metric clicks per
  page; reject if lift < +5% or the 80% credible interval includes 0 after 4 weeks.
Leading indicator: page-query CTR at stable position (+1 pp within 14 days on treated arm),
  Google rewriting rate of titles in SERP snapshots (should fall).
Expected impact: +4.0k to +9.0k clicks/month (P=0.5), value per click 0.80 EUR.
Review date: 2026-NN-NN
```

### 14.3 Rules for the tool/agent
- Never present a recommendation without all four fields; if data is missing, say "insufficient
  data" and list what to fetch.
- Prefer recommendations with a runnable falsifier from existing data (GSC/GA4) over those needing
  new instrumentation.
- Separate fact (observed) from inference (hypothesis) in language: "observed", "suggests",
  "assumed".
- Keep a registry of recommendations with outcomes (HIST); compute hit rate by type and feed it to
  Confidence in section 10.
- When evidence contradicts a recommendation, record "rejected by evidence" and report it.
- Distinguish correlation windows: an update on date D inside the window invalidates naive
  before/after claims.
- State the counterfactual explicitly: what would have happened without the action.

### 14.4 Errors
- Observation that is a conclusion ("content is thin").
- Falsifier that is vague or has no threshold/time.
- Leading indicator identical to outcome.
- Dependency list omitting the SERP-intent assumption.
- Success defined after the fact.
- Not recording failed hypotheses.

---------------------------------------------------------------------------------------------
## 15. Method-to-data matrix (what the MCP server needs)

| Analysis | GSC | GA4 | SERP | CRAWL | HIST | Notes |
|---|---|---|---|---|---|---|
| Keyword research | queries, pages | site search | top 10, features, PAA | titles/H1 | volume series | provider volume, location/language |
| Intent classification | queries | - | top-10 types, features | page type | label history | store confidence+evidence |
| Topic clustering | query-page | - | URL overlap | link graph | topic series | graph clustering |
| SERP features | searchAppearance, CTR | - | features, owners, repeat | schema | feature history | device/location |
| Competitive gap | baseline | - | competitor top-10 | competitor pages | SoV | backlink data |
| Striking distance | query-page 90 d | - | exact rank | titles, headings | position variance | threshold on impressions |
| CTR curve | position buckets | - | features join | - | quarterly refit | branded excluded |
| Cannibalization | query x page x week | - | intent check | canonical | URL share series | truncation caveat |
| Decay | page/query weekly | landing sessions | rank changes | last modified | >16 months | YoY and seasonality |
| Seasonality | monthly | - | - | - | >= 2 years | detrend |
| Brand vs non-brand | regex split | - | - | - | brand regex versions | anonymization caveat |
| GA4 landing analysis | join clicks | landing x source x events | - | CTA presence | - | normalization |
| Forecast | baseline | CR | features | - | >= 2 y | backtest |
| Prioritization | impact inputs | value | - | effort proxies | hit rates | EV/effort |
| Split test | clicks per page | sessions, key events | - | template flags | arms, dates | A/A first |
| Reporting | all | all | SoV | health | MoM/YoY | caveats appendix |

Capability naming for tool surface (project principle V): use names like "search performance",
"page analytics", "SERP snapshot", "site crawl", "history store"; never provider names in tool
text. Google services connected by the user (Search Console, Analytics) are the stated exception.

---------------------------------------------------------------------------------------------
## 16. Consolidated threshold table (all [H] unless marked; calibrate per site)

| Item | Default | Why |
|---|---|---|
| Analysis window for rank-based lists | 90 days | stabilizes position average |
| Exclude recent days | last 2-3 days | preliminary data [P] |
| Min impressions, striking distance | >= 100 per 90 d (>= 30 for small sites) | noise |
| Striking distance band | position 8-20 | page-2 and low page-1 |
| Position variance flag | daily std > 8 | unstable ranking |
| CTR curve bucket min impressions | >= 1,000 | CI width |
| Low-CTR flag | actual < 0.6 * expected curve value | snippet problem candidate |
| Cannibalization candidate | >= 2 URLs each >= 20% of query impressions, query >= 100 impr | multi-URL |
| Decay flag | 12-wk clicks < 70% of prior 12-wk AND YoY < -20% | seasonality-aware |
| SERP overlap for same page | >= 3-4 shared URLs in top 10 | grouping |
| Merge spokes | overlap > 5 | duplicate intent |
| GA4 vs GSC ratio alert | step change > 20% | tracking issue |
| Split test pages | >= 100 per arm, A/A first | power |
| Split test duration | 2-8 weeks, pre-registered | ranking lag |
| Forecast backtest | MAPE < 20% topic-level | else directional |
| Seasonality data | >= 2 full years | cycle estimation |
| Recommendation quality | >= 6/8 on rubric | falsifiability |
| Portfolio mix | 70/20/10 | known/growth/experiments |
| Engaged session [P] | > 10 s, or key event, or >= 2 views | GA4 definition |
| Web Vitals good [P, verify] | LCP <= 2.5 s, INP <= 200 ms, CLS <= 0.1 (p75) | web.dev |
| GSC retention [P] | 16 months | store daily in HIST |
| GSC API cap [P] | 50K rows/day/search type | store daily, mark truncation |
| Anonymized share | measure per site | report always |

---------------------------------------------------------------------------------------------
## 17. Master list of common analyst errors (checklist for the agent's self-review)

Data
1. Averaged ratios (CTR, position) rather than recomputing from sums.
2. Mixed aggregation levels (property vs page) in one table.
3. Treated truncated/anonymized query data as complete.
4. Used preliminary data or short windows.
5. Compared unaligned periods (weekdays, holidays) or mixed search types/devices/countries.
6. Joined GA4 and GSC without URL normalization; treated them as equal measures.
7. Used UI numbers and BigQuery numbers interchangeably.

Interpretation
8. Confused correlation with cause; no counterfactual.
9. Ignored brand effects, seasonality, updates, site changes.
10. Misread engagement metrics (bounce redefinition; short-answer pages).
11. Over-trusted third-party volume/difficulty/traffic estimates.
12. Used generic CTR curves instead of local, current ones; ignored SERP features and AI answers.
13. Over-reported small-sample percentages.

Recommendations
14. No prioritization by value/effort; tool-issue lists as plans.
15. No falsifier, leading indicator or dependency (section 14).
16. Merged or split pages against SERP evidence.
17. Promised rankings, traffic or timelines.
18. Failed to separate quick wins from strategic bets; no dependency order.

Reporting
19. Dashboards without explanation; no next steps; no caveats appendix.
20. No record of past recommendations and their outcomes.

---------------------------------------------------------------------------------------------
## 18. Open items for verification before shipping into skills
- Confirm GA4 Explorations event limits and retention values on the current Google docs.
- Confirm the exact GSC help URLs for regex filter (RE2) and "About the data" text, and the
  dataState values in the current API reference.
- Confirm web.dev Core Web Vitals thresholds at the time of release.
- The 2025 CTR figures are secondary summaries (Ahrefs, GrowthSRC via aggregator posts);
  re-fetch the original studies if any figure will be quoted to clients.
- Broder's percentage ranges came through a secondary summary; check against the PDF.
- Book content was assessed from tables of contents and public summaries only; no chapter text was
  read. Claims tagged [B] describe the books' scope, not specific numbers.
- All [H] items are priors for the agent; each should be replaced by site-specific measurements
  stored in HIST as the product matures.
