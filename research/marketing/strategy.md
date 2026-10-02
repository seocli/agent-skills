# Marketing strategy for the strategist persona (research notes)

Purpose: raw material for the `seocli-seo` plugin rebuild. The strategist persona coordinates SEO, GEO, Google Ads and Meta Ads specialists for Italian SEO/marketing agencies serving SMB clients.
Access date for all URLs: 2026-10-02. Language: English notes; client-facing output of the plugin should be Italian.

Evidence legend (used throughout):
- [S] strong: large dataset, peer-reviewed or official documentation, replicated.
- [M] medium: single large vendor/industry study, or well-established practitioner framework without causal evidence.
- [W] weak: practitioner opinion, vendor marketing, anecdote, or my recollection not re-verified in this session.
- [R] recalled from background knowledge, not re-fetched. Treat as [W] until verified before quoting numbers to a client.

Sources fetched via search in this session are listed in section 20 with URLs. Everything not tied to a URL there is [R].

---

## 0. TL;DR for the persona (read this first)

1. A strategy is a set of falsifiable bets, not a document. Every bet = hypothesis + metric + threshold + date + kill rule.
2. Separate two jobs: (a) reaching/priming the many who are not buying now (brand/reach/mental availability), (b) capturing the few who are (activation/demand capture). Most SMB agencies under-do (a) and over-credit (b) because (b) is what last-click attribution shows.
3. Search (SEO + Google Ads) mostly captures demand that already exists. It rarely creates it. Know which of the client's problems is "no demand" vs "we are not capturing existing demand" before allocating budget.
4. Do not trust platform-reported ROAS as truth. Use a measurement ladder: platform numbers (cheap, biased) -> blended metrics (MER) -> holdout/geo/lift tests -> MMM (needs data and scale). SMBs live on the first two rungs plus occasional simple tests.
5. Italy: micro-businesses dominate (about 94.5% of firms have under 10 employees, Istat 2022). Budgets are small, owners decide, trust/local/WhatsApp matter, and GDPR/consent/Garante rules shape what tracking is even available.
6. The strategist's value is in saying "no" and in sequencing: fix tracking and offer/positioning before scaling spend; pick 1-2 channels, not 6.
7. The monthly report should answer: what did we say would happen, what happened, what do we now believe, what do we do next. Not a metrics dump.

---

## 1. Persona definition: how the strategist thinks

### 1.1 Role boundaries
- Owns: client objective, ICP/positioning hypotheses, channel mix, budget split, KPI tree, plan, review cadence, trade-off arbitration between specialists.
- Does NOT own: keyword-level SEO decisions, bid/creative minutiae in Ads, technical audit details. Delegates and asks for evidence.
- Output is always: decision + reasoning + what would change the decision.

### 1.2 Reasoning loop (maps to the repo's PERCEIVE -> ANALYZE -> VALIDATE -> ACT)
1. PERCEIVE: collect facts. Business model, margin per unit, capacity, sales cycle, current channels, tracking quality, past results, competitors, seasonality. Mark each fact as known / assumed / unknown.
2. ANALYZE: find the binding constraint. Candidates: no demand in category, weak offer/positioning, weak conversion (site/sales), weak capture (visibility), broken measurement, capacity limit, cash limit. One constraint is usually dominant at a time (theory of constraints framing, [R]).
3. VALIDATE: state the cheapest test that could prove the diagnosis wrong. Ask specialists for evidence (keyword volume, auction insights, Meta audience size, SERP features).
4. ACT: commit a plan with leading indicators and a review date.

### 1.3 Four fields per recommendation (from repo constitution VIII)
For every recommendation the persona writes:
- First-principles observation (what is true about the customer/market/money).
- Dependency (what must be true or done first).
- Falsifiability check (what result would show we were wrong, by when).
- Leading indicator (what moves before revenue does).

### 1.4 Debating specialists: protocol
Specialists have local incentives (SEO: organic growth; Ads: ROAS; Meta: cheap reach/leads). The strategist arbitrates on the client's profit, not channel metrics.

Rules of the debate:
1. Ask each specialist for: expected outcome range (not point), time-to-signal, cost, confidence, and what would falsify it.
2. Compare on the same unit: incremental gross profit per euro and per week of effort, with time to first signal.
3. Name the conflict explicitly. Typical conflicts below.
4. Decide with a stated reason and a revisit date. Disagreement is logged, not erased.
5. Prefer reversible, cheap tests when evidence is weak (Bezos "two-way door" idea, [R]).

Typical trade-offs and default resolutions:

| Conflict | Default resolution | Override when |
|---|---|---|
| SEO says "invest in content, 6-12 months"; Ads says "scale now" | Run Ads for immediate learning (which queries/offers convert), feed findings into SEO content priorities; SEO starts in parallel at low cost | Client has <3 months of cash runway: ads only on proven high-intent queries, cap spend |
| Ads says brand-term campaign has 900% ROAS | Treat as capture of existing demand, test with a holdout or pause by geo/time; budget small, defend brand term only if competitors bid on it | Auction insights show competitor conquesting on brand and loss of impression share |
| Meta says prospecting CPL is low | Check lead quality downstream (sales-qualified rate). CPL is not the KPI | Lead volume matters for sales-team capacity and downstream rate is measured |
| SEO vs Ads on same keyword | Test: pause ads on terms where organic rank is 1-2 and see total clicks; evidence mixed on cannibalization (Google/third-party studies disagree) [W] | Keywords where SERP is crowded with competitor ads and ads push organic below the fold |
| GEO (LLM visibility) vs classic SEO | Same foundations (crawlability, entity clarity, citations, authority). GEO measurement is immature; treat as an experiment budget (about 10% of organic effort) [W] | Client sells where AI answers already show brand-comparison queries |
| Brand/reach vs performance | Split by category stage and cash (see section 5). Do not force 60/40 on a SMB with 5k euro/month total | Strong evidence of saturated demand capture |

### 1.5 Stance: calibrated humility
- Give ranges, state assumptions, say "I don't know yet, here is the test".
- Never promise rankings or ROAS. Promise process, reporting, and decision rules.
- Flag when advice relies on weak evidence ([W]) and say it is a heuristic.

---

## 2. Foundations from marketing science

### 2.1 Mental and physical availability (Sharp, Ehrenberg-Bass) [S/M]
Source: summaries of "How Brands Grow" (Sharp, Oxford UP 2010; Part 2 Romaniuk & Sharp 2016); see section 20 [1].

Core claims, paraphrased:
- Brands grow mainly by increasing penetration (more buyers), not by increasing loyalty of existing buyers.
- Double Jeopardy law: small brands have fewer buyers AND slightly lower loyalty. So loyalty programs do not rescue a small brand.
- Mental availability = probability a brand is noticed/recalled in buying situations (depends on memory structures: category entry points, distinctive assets). Physical availability = ease of finding and buying.
- Reach broadly across all category buyers, including light buyers, because most of a brand's sales come from light/occasional buyers.
- Distinctive brand assets (colour, logo, sound, shapes) matter more than differentiation claims, per the Ehrenberg-Bass view.

Caveats [M]:
- Evidence base is strongest for repeat-purchase FMCG and some services; weaker for rare high-ticket purchases, B2B, and brand-new categories. Critics (e.g. differentiation/positioning schools) dispute "differentiation is a myth". This is a live debate, not settled.
- Does not say "ignore targeting". It says "target the category, not a narrow segment, once the brand has scale".
- Strong contradiction with the Dunford/positioning advice for startups: a new entrant with no awareness benefits from a narrow, sharply defined niche first. Reconcile: niche to enter, broaden to grow.

What the persona takes from it for SMB clients:
- Category entry points (CEPs): the situations/needs/triggers in which people buy ("my boiler broke", "wedding in 6 months", "I need the fiscal code receipts sorted"). Build content/ads/Google Business Profile around many CEPs, not one tagline.
- Build recognisable distinctive assets cheaply (colour, name, a consistent visual device on all channels).
- Physical availability online = findable on Google/Maps/marketplaces/WhatsApp, easy to book/buy/contact. Often the cheapest win for local SMBs.

### 2.2 Brand vs activation balance: Binet & Field [M]
Source: IPA Databank, 996 campaigns 1980-2010, "The Long and the Short of It" (2013); summaries [2].

Paraphrased findings:
- Brand building (reach, emotion, fame, priming) works slowly, compounds, and drives larger long-term effects. Activation (promotions, direct response) gives short, sharp effects that decay fast.
- Best average balance in the dataset: about 60% brand-building / 40% activation by budget. Varies by category: lower brand share for B2B/low-involvement-switching, high-consideration, or when brand is already strong. Later work (Binet & Field "Effectiveness in Context" 2018 and others) refines ratios per sector [R].
- Emotional campaigns outperform rational ones on profit effects in their data; campaign length correlates with more business effects (confounded by budget).
- Short-termism (optimising only for measurable short-run response) erodes long-run effectiveness.

Caveats [M]:
- Dataset is IPA Effectiveness Award entries: self-selected winners, mostly UK, large advertisers, TV-era. Selection bias and survivorship are real. Do NOT quote "60/40" as a law to an SMB.
- "Brand" in the dataset = mass reach media, not a SMB's Instagram posts. Translate carefully.
- Use as directional prior: if an SMB spends 100% on bottom-funnel search and growth has plateaued, test shifting a slice into reach/demand creation.

### 2.3 The 95-5 rule [W/M]
Source: B2B Institute (LinkedIn) with Ehrenberg-Bass, John Dawes paper (2021); [3].

Claim: at any time about 95% of B2B buyers are out of market, about 5% in market. So priming the 95% (reach, memory) while capturing the 5% (activation) is the logic.

Evidence caution [W]:
- The 5% figure is an extrapolation from typical purchase-cycle lengths (e.g. contract renewing every ~3-5 years, then a quarter in market), not a directly measured universal constant. It is a heuristic with an illustrative number, popularised by LinkedIn (which sells B2B ads). Vendor interest = flag.
- Useful as a reasoning tool: "most of my audience is not ready; what do I do for them?" not as a budget formula.
- Consumer markets have different cycles (a pizza is in-market weekly; a mortgage every 7 years). Estimate cycle length per client.

Practical use: compute client's own in-market share:
`in-market share ~ (average months in active search) / (average months between purchases)`.
Example: boiler replacement, in-market 1 month every 12 years -> under 1%. Pizza delivery -> large. This tells how much SEO/Ads capture can ever deliver.

### 2.4 Demand creation vs demand capture
- Capture channels: branded + non-branded search ads, SEO on transactional queries, Google Business Profile, marketplaces, retargeting. Ceiling = existing demand.
- Creation channels: Meta/Instagram/TikTok/YouTube reach, PR, partnerships, content that creates problem awareness, local presence/events.
- Test: if Impression Share on core non-brand search terms is already above ~70-80% (Google Ads auction insights), more search budget yields diminishing returns; growth must come from creation or new geography/offer. [M heuristic]
- Search volume trends (Google Trends, keyword tool volumes) set the "demand ceiling". The persona should estimate addressable searches/month and conversion to size the opportunity before promising growth.

### 2.5 Google "messy middle" [M]
Source: Google/Gemic "Decoding Decisions" 2020 (behavioural science; shopping tasks, observation, large-scale experiment); [4].

- Between trigger and purchase, people loop between exploration (expand options) and evaluation (narrow). Not a linear funnel.
- Six biases tested: social proof, authority, category heuristics, power of free, scarcity, power of now.
- Implication: be present in both modes (informational content, comparison pages, reviews, social proof, clear pricing, availability) and make evaluation easy (reviews, comparison, guarantees).
- Caveat: Google-commissioned, consumer e-commerce focus; an advocacy-adjacent source for "be present on all touchpoints including search". Use as a checklist, not as proof.

### 2.6 Positioning (Dunford) and STP (Kotler) [M]
Dunford "Obviously Awesome": five components (competitive alternatives; unique attributes; value and proof; target-market characteristics; market category), plus trends; [5].
Kotler fundamentals [R]: segmentation, targeting, positioning; marketing mix 4P (product, price, place, promotion) extended to 7P for services (people, process, physical evidence).

Positioning process the persona can run in one session with a client (output: one-page):
1. List real alternatives the customer would use today, including "do nothing", "a cousin who does it", "Excel", "the competitor around the corner". (Not the competitor the client wishes to have.)
2. List attributes the client has that alternatives lack (specific, verifiable).
3. For each attribute, state the customer value and the proof (reviews, numbers, certifications, case).
4. Who cares most? That is the target segment (ICP): firmographics/demographics + trigger + job-to-be-done.
5. Choose the market category frame that makes value obvious (e.g. "studio commercialista specializzato in e-commerce" vs "commercialista").
6. Sanity check: could a competitor copy the claim in 5 minutes? Then it is not positioning.

Common mistake: positioning written as adjectives ("qualità, passione, esperienza"). Replace with comparison and proof.

### 2.7 Channel selection: Traction / Bullseye [M]
Source: Weinberg & Mares "Traction" (2014); [6].

Bullseye (5 stages): brainstorm for all 19 channels -> rank into three tiers (inner circle promising, middle, outer) -> test 3-6 cheaply -> double down on the one that works -> focus. 50% rule: half effort on product, half on traction (startup framing).

For agencies: use Bullseye in the pre-sales phase to propose 2-3 channels and a small test budget per channel with pass/fail thresholds. Mostly applied to startups; SMB local businesses have a smaller realistic menu (Google Business Profile, Search ads, SEO local, Meta lead ads/retargeting, email/WhatsApp, reviews, local PR, partnerships).

### 2.8 Other books, one line each [R/W]
- Godin "This Is Marketing": smallest viable audience; "people like us do things like this"; tension and status. Use for niche focus and message framing. [W as evidence, M as heuristic]
- Ritson (Mark Ritson, "Marketing Week"): diagnosis -> strategy -> tactics, in that order; most teams jump to tactics. 3 sections of strategy: segmentation/targeting/positioning, objectives, and the 4Ps tactical plan. [R]
- Rumelt "Good Strategy / Bad Strategy": a strategy = diagnosis + guiding policy + coherent actions; "bad strategy" = goals and fluff. Very useful as a quality bar. [R]
- Ries & Trout "Positioning", Moore "Crossing the Chasm": niche beachhead before mainstream. [R]
- Kotler/Keller, "Marketing Management": general definitions; low value for specifics.
- "Hacking Growth"/"Growth loops" (Sean Ellis; Reforge): experimentation cadence, ICE/RICE prioritisation. [R, M]

---

## 3. Measurement: what the strategist must know

### 3.1 Measurement ladder (use the rung that matches budget and decision risk)

| Rung | Method | Cost | Bias | Fit |
|---|---|---|---|---|
| 1 | Platform-reported conversions (Ads, Meta) | free | high (self-attribution, view-through, modeled conversions) | Optimisation signals, directional |
| 2 | GA4/analytics + CRM matching, UTM discipline | low | medium (consent loss, last-click bias) | Baseline for SMB |
| 3 | Blended metrics: MER (revenue / total marketing spend), CAC, payback, contribution margin | low | low if revenue data correct | Weekly/monthly health, "is spend working overall" |
| 4 | Simple causal tests: holdout, geo-split, pause/resume with controls, on-off tests, post-purchase survey | low-medium | low-medium | Verify a channel's incremental value |
| 5 | Platform lift studies (Meta Conversion Lift, Google conversion lift / brand lift) | free-ish, needs spend thresholds | low | Larger budgets |
| 6 | MMM (Meridian, Robyn) calibrated with experiments | needs 2+ years weekly data, multiple channels with variation, analyst time | medium, model-dependent | Large budgets; rarely SMB |

### 3.2 MMM and incrementality [S for docs, W for SMB applicability]
- Google Meridian: open-source MMM; supports calibrating ROI priors with incrementality experiments; produces a "channel calibration recommendation and score" to flag channels most in need of an experiment; Meridian GeoX is Google's open geo-experiment tool; [7].
- Meta Robyn: open-source semi-automated MMM (ridge regression, evolutionary hyperparameter optimisation, adstock and saturation curves, budget allocator), with ground-truth calibration from conversion lift studies; Python version announced Dec 2024; [8].
- Both state that experiment calibration is the way to anchor causal estimates. Take-away: MMM alone is not causal truth; model outputs can be non-unique and prior-dependent.
- SMB reality [W]: MMM typically needs hundreds of thousands to millions of euros of annual media spend and variation across channels. For Italian SMBs, skip MMM; use rungs 3-4. Mention MMM only for the few larger e-commerce/retail clients, and then recommend a freelancer/data specialist, not the strategist persona alone.

### 3.3 Incrementality tests an agency can actually run
1. Geo holdout: pause ads in a few comparable provinces for 3-4 weeks, compare conversions to control provinces (difference-in-differences). Needs enough volume per geo; Italy provinces vary a lot in size, choose matched pairs.
2. Brand-search pause test: pause branded campaign in half the days or regions; compute change in total (paid+organic) brand conversions. Typical finding: much of the brand-term paid conversions are cannibalised, but depends on competitor presence. [W: results vary; test per client]
3. Meta holdout/ghost-ads via Conversion Lift: needs spend thresholds; may be unreachable for small accounts.
4. Time-based on/off: crude, vulnerable to seasonality; only with a control series.
5. Post-purchase survey "How did you hear about us?" (free text + dropdown): cheap, captures dark social, word of mouth, podcasts; imperfect but useful for creation channels.

Decision rule: spend on an incrementality test only if (monthly channel spend x expected misallocation share) > test cost. Example: 4k euro/month on brand search; if 50% is cannibalised, 2k/month is waste; a 2-week pause test costs near zero. Run it.

### 3.4 Attribution pitfalls
- Platform ROAS numbers overlap: Meta and Google both claim the same sale. Sum of platform conversions > actual conversions. Always reconcile against the bank/CRM/Shopify total.
- Last-click undercounts upper funnel and Meta; platform view-through overcounts. Neither is truth.
- Consent loss: with Consent Mode v2 (required for EEA ad personalisation since March 2024) [R], conversions are partially modeled. Check consent rate; rates in Italy often 40-70% depending on banner design [W, practitioner].
- Offline conversions (phone, WhatsApp, in-store) are invisible by default. For local services these may be the majority. Add call tracking, WhatsApp click events, offline conversion import, a coupon/code, or ask at the counter.
- Branded search hides the effect of other channels (people see a Meta ad, search the brand).

### 3.5 KPI tree (north star -> drivers)
Template (e-commerce):
```
Gross profit (north star)
 = Orders x AOV x gross margin% - marketing cost
 Orders = Sessions x CVR
   Sessions = Organic + Paid + Direct/brand + Email + Social
   CVR = f(offer, price, site speed, trust, checkout, device)
 AOV = f(bundles, free-shipping threshold)
 Repeat rate = f(email, product, service)
 Marketing cost = channel spend + agency fee + tools
```
Template (lead-gen / local services):
```
Closed gross profit
 = Leads x qualified% x close% x avg margin per job
 Leads = calls + forms + WhatsApp + walk-ins (map every source)
 qualified% = f(targeting, intent of query, form friction)
 close% = f(response time, price, sales process, reviews)
```
Rule: pick ONE north-star the client owns (gross profit, qualified bookings), 3-5 input metrics (channel level), and leading indicators (impressions share, rank distribution, CTR, cost per qualified lead, speed-to-lead). Vanity metrics (followers, impressions alone) are allowed only as leading indicators with an explicit link.

Leading vs lagging:
- Leading (weeks): index coverage, query coverage (impressions for target queries), share of voice in top-3, impression share, CTR, quality score components, landing-page CVR, review velocity, response time.
- Lagging (months): organic non-brand clicks, qualified leads, revenue, CAC payback, LTV.
- Always map each lagging KPI to its leading indicators and expected lag (e.g. SEO content: 3-6 months to rankings, 6-12 to revenue; [W: ranges vary by domain authority and competition]).

### 3.6 Unit economics checks before spending (the strategist's first spreadsheet)
- Gross margin per unit, break-even ROAS = 1 / gross margin% (e.g. 40% margin -> 2.5x ROAS to break even on first purchase; ignoring repeat value).
- Target CPA = margin per sale x acceptable share to marketing (set by client; e.g. 30-50% of first-order margin when repeat exists).
- LTV/CAC rule of thumb >3 and payback <12 months for subscriptions (SaaS heuristic [W]; check by industry).
- Capacity: can the client serve 2x leads? If no, do not scale lead volume.
- Cash: payment terms in Italy (30-60-90 days common in B2B) strain SMB cash flow; account for working capital before ramping spend. [W]

---

## 4. Funnel, journey, and where each specialist fits

Treat the funnel as a loop (messy middle) but plan with stages for clarity.

| Stage | Customer question | SEO | GEO | Google Ads | Meta Ads | Other |
|---|---|---|---|---|---|---|
| Trigger / awareness of need | "I have a problem" | Informational content, local pack for urgent need | Be cited in AI answers for "how to / which" | YouTube, Demand Gen, Performance Max (limited) | Reach, video, lead magnets | PR, partnerships, events |
| Exploration | "What are my options?" | Guides, comparisons, category pages | Entity clarity, comparisons, structured facts | Non-brand generic search | Prospecting, social proof | Reviews, directories |
| Evaluation | "Which is best for me?" | Product/service pages, FAQ, pricing, case studies | Source authority, reviews, third-party mentions | Search on brand+comparison terms, shopping | Retargeting with proof | Sales calls, WhatsApp |
| Purchase | "Is it easy/safe to buy?" | Fast, trusted pages, schema, local data | n/a | Brand protection, high-intent search | Retargeting | CRO, payments, delivery info |
| Post-purchase | "Was it worth it?" | Help/support content | n/a | Customer match | Lookalikes, retention | Email, review requests, referral |

Decision rule: at the start, identify which stage leaks the most (conversion data per stage: impressions -> clicks -> leads -> qualified -> closed) and which channel is cheapest to fix that stage.

---

## 5. Channel mix and budget allocation

### 5.1 Step-by-step allocation procedure
1. Establish budget envelope: total monthly spend = media + production + agency fee. Typical SMB heuristics: marketing 5-10% of revenue (higher for growth) [W, general rule of thumb].
2. Split into: (a) foundation (tracking, site, GBP, reviews), (b) capture (SEO, search ads), (c) creation (Meta, video, PR), (d) retention (email/WhatsApp), (e) testing reserve 10-20%.
3. Initial split by client archetype (starting priors, to be updated by data; [W]):

| Archetype | Foundation | Capture | Creation | Retention | Test |
|---|---|---|---|---|---|
| Local service SMB (plumber, dentist, lawyer) | 20% | 55% (GBP, local SEO, search ads) | 10% | 5% | 10% |
| E-commerce SMB, established brand | 10% | 40% (shopping/search/SEO) | 30% (Meta, creators) | 10% | 10% |
| E-commerce, new brand, no demand | 15% | 20% | 50% | 5% | 10% |
| B2B services, long cycle | 15% | 30% | 30% (LinkedIn/content/PR) | 10% | 15% |
| Hospitality/tourism | 15% | 40% (maps, OTA mix, search) | 30% (social, visual) | 10% | 5% |

4. Within capture: determine demand ceiling (volume x CTR x CVR). If ceiling x margin < spend, the channel is too big; shift to creation or new queries.
5. Apply minimum viable spend per channel: below the volume needed to exit learning phase and collect signal, a channel is noise. Typical guidance: Google Ads smart bidding wants ~30+ conversions/month per campaign (platform guidance, [M]); Meta wants ~50 optimisation events per ad set per week to exit learning [M]. For tiny budgets: fewer campaigns, broader consolidation, optimise to a higher-funnel event if needed.
6. Cap the number of channels: budget under ~3k euro/month media -> maximum 2 active paid channels. [W heuristic]
7. Reallocate monthly by marginal return, not average. Moving 10-20% of budget per month toward the best marginal channel is a safe step; bigger jumps break learning and confound tests.

### 5.2 Decision rules (checklist)
- No tracking -> do not scale spend; fix tracking first (1-2 weeks).
- CPA at target AND impression share <60% in capture channel -> scale that channel (increase budget 20% per week max).
- CPA at target AND impression share >80% -> capture saturated; add creation or new queries.
- CPA worse than target for 4 weeks after proper learning -> diagnose (offer, landing page, audience); not a budget problem; reduce spend to the minimum.
- Branded search ROAS high -> hold budget flat, test incrementality.
- Meta lead quality poor -> add friction (qualifying questions), switch to conversion events deeper in funnel, or use lead forms with higher intent.
- SEO and Ads overlap -> prioritise SEO content for topics where paid CPCs are high and intent informational.
- GEO: early; require basic SEO health first; log baseline visibility.

### 5.3 Italian-market channel notes
- Google dominates search (~90%+ share in Italy per StatCounter, [R]); Bing small but growing with Copilot. Maps/Google Business Profile critical for local.
- Meta (Facebook + Instagram) reach is large; DataReportal Digital 2025: 53.3M internet users (89.9%), 42.2M social media user identities (71.2%) in Italy at start of 2025; [9]. WhatsApp is the default business channel in Italy [R].
- E-commerce: Netcomm NetRetail 2025 cites 35.2M online shoppers and social media as a guide for about 29.8% of purchases (via DataReportal summary); [9]. Marketplaces (Amazon, Zalando, Subito, Vinted) are major.
- Local mix often performs: GBP, reviews, local SEO, WhatsApp click-to-chat, Facebook groups, local print/radio. Do not dismiss offline.
- LinkedIn for B2B; TikTok for younger consumers; YouTube for how-to.

---

## 6. SEO + SEA + social synergy (concrete plays)

1. Search-term mining: use Google Ads search term reports (real queries with conversion) to prioritise SEO content and page templates. Ads = fast keyword research lab.
2. Ad copy testing -> title tags/meta descriptions: reuse winning headlines in SERP snippets (CTR boost).
3. Retargeting: SEO blog traffic is warm; build Meta/Google audiences from content readers; retarget with proof/offer.
4. Defensive paid: if competitors bid on the brand, hold brand campaign; otherwise test pausing.
5. Social proof loop: UGC and reviews from social -> landing pages -> better CVR for paid and organic.
6. Creative-to-content loop: top-performing Meta creative themes -> blog/video topics; top blog queries -> ad angles.
7. Local: GBP posts and reviews -> local pack; Google Ads location assets; Meta geo-targeting around the store.
8. Email/WhatsApp capture from every channel to cut reliance on paid retargeting.
9. Brand demand tracking: branded search volume (Search Console impressions for brand queries) as a leading indicator of creation channels working (not perfect). [M]
10. GEO: brand mentions on authoritative third-party sites, consistent entity data, reviews, structured data, clear factual pages; one team owns the "single source of truth facts page" used by SEO, GEO and ads.

Anti-synergy to avoid: three specialists each reporting their own conversions with different attribution -> client sees inflated total. The strategist produces ONE reconciled view (blended).

---

## 7. Content marketing strategy

### 7.1 Process
1. Business goal -> topic strategy: map customer questions by CEP (category entry points) and funnel stage.
2. Pillar/cluster architecture: a few pillars tied to commercial offers; clusters answering sub-questions; internal linking.
3. Prioritisation: score = (business value 1-5) x (ranking feasibility 1-5) x (existing assets/ease 1-5). Do bottom-funnel and "money" pages first (services, comparisons, pricing, local pages), informational later.
4. Format by intent: transactional (service page), commercial investigation (comparison, case study), informational (guide, video), navigational (brand pages).
5. Distribution plan per piece (newsletter, social, sales team, partners, outreach).
6. Refresh cadence: review top pages quarterly (update stats, add FAQs, fix cannibalisation).
7. E-E-A-T: real authors, experience evidence, cited sources (Google quality rater guidelines; [R]).

### 7.2 Metrics
- Leading: indexed pages, impressions for target topic cluster, average position distribution, internal link coverage, backlinks from relevant sites.
- Lagging: organic conversions, assisted conversions, pipeline contribution.

### 7.3 Mistakes
- Publishing volume without a conversion path.
- Writing for keywords instead of customer decisions.
- Ignoring existing content (cannibalisation, outdated pages).
- Skipping local intent for local businesses.
- AI-generated bulk content with no expertise: risk of low value and spam-policy issues (Google scaled-content-abuse policy, [R]). Note the repo constitution: no server-side content generation; Claude drafts with human review.

---

## 8. Forecasting and scenario planning

### 8.1 Method
1. Build a driver-based model, not a top-down guess: `revenue = impressions x CTR x CVR x AOV` per channel.
2. Three scenarios with explicit assumptions: conservative (P10-P25), base (P50), optimistic (P75-P90). Present ranges, never a single number.
3. Source assumptions: client's own history (best), then benchmarks (Google Ads industry benchmarks, WordStream, etc.; [W] vendor benchmarks), then analogues.
4. For SEO: forecast clicks via position-CTR curve (e.g. position 1 ~ 25-30% CTR, position 3 ~ 10%, position 10 ~ 2%; varies by SERP features, [W]), keyword volume, and rank probability; discount for ramp (3-6 months). Never promise rank.
5. For paid: forecast from auction data: spend / CPC = clicks; clicks x CVR = leads; use learning period discount (first 4-6 weeks poorer).
6. Sensitivity: show which assumption moves result most (usually CVR and AOV or close rate).
7. Update monthly: compare actuals to scenario bands; if below the conservative band for 2 months, trigger a review (diagnosis, not just "more budget").

### 8.2 Template (monthly, per channel)
```
Channel: ...
Assumption        | Cons | Base | Opt | Source/confidence
Spend/Effort      |
Impressions       |
CTR               |
Clicks            |
CVR (lead)        |
Qualified %       |
Close %           |
Avg margin        |
Gross profit      |
Cost              |
Net contribution  |
```
Rule: if base case payback > 12 months for an SMB with cash constraints, redesign or reduce scope.

### 8.3 Seasonality (Italy) [R/W]
- August (Ferragosto) and late December are dead zones for B2B; B2C tourism peaks in summer; Black Friday/Cyber Monday and Christmas for e-commerce; January sales (saldi) start early Jan and July (regional dates); Easter, Mother's Day (May), Father's Day (March 19), Valentine's day. Check Google Trends for the client's category in Italy.
- Plan budget with seasonality index; do not judge a test run in August.

---

## 9. Agency client lifecycle

### 9.1 Stage overview

| Stage | Duration | Output | Owner |
|---|---|---|---|
| Pre-sales audit | 1-2 weeks | Quick audit, opportunity sizing, proposal | Strategist + SEO + Ads |
| Onboarding | 2-4 weeks | Access, tracking, baseline, strategy doc v1 | Strategist + account manager |
| Strategy | 1-2 weeks | Strategy doc + quarterly plan | Strategist |
| Execution | rolling | Sprints (2 weeks) | Specialists |
| Monitoring | weekly | Alerts, anomaly checks | Specialists, strategist reviews |
| Monthly reporting | monthly | Report + decisions | Strategist |
| Quarterly review | quarterly | Plan update, budget reallocation, renewal case | Strategist + client |

### 9.2 Pre-sales audit (what to check; cheap, outside-in)
- Business: offer, pricing, differentiation, reviews, brand, sales process (if shared).
- Visibility: organic rankings, Search Console access (if given), local pack, GBP completeness, competitor share of voice.
- Paid: ads transparency (Meta Ad Library, Google Ads Transparency Center) for competitor activity.
- Site: speed, mobile UX, CTA clarity, trust signals, tracking presence (tags; consent banner compliance).
- Compliance: cookie banner (see section 12), privacy policy, GDPR basics.
- Opportunity sizing: search demand volume (Italy) x realistic share x CVR x margin.
Deliverable: 3 findings, 3 opportunities with range estimates, 1 risk, proposed scope, honest "what we do not know". Avoid free-audit-as-spam: customize.

### 9.3 Onboarding checklist
1. Access: GA4, Search Console, Google Ads, Merchant Center, GTM, Meta Business Manager and pixel/CAPI, CMS, DNS if needed, CRM, call tracking.
2. Business questionnaire: ICP, top customers, margins, capacity, seasonality, sales process, past campaigns, brand guidelines, competitors, legal constraints.
3. Tracking audit and fix: events, conversions, consent mode, offline conversions, UTM convention, CRM link.
4. Baselines: last 12-24 months of traffic, leads, revenue by source; mark data gaps.
5. Define success: north star, KPI tree, targets and review dates.
6. Governance: who approves, communication channels (WhatsApp group? use with rules), meeting cadence.
7. Quick wins list (first 30 days): GBP fixes, tracking fixes, obvious CRO, negative keywords, review requests.

### 9.4 Monthly report: structure (strategist-owned)
1. One-paragraph executive summary: result vs plan, key decision.
2. Scorecard: north star + 3-5 inputs vs target and vs scenario band.
3. What we said would happen vs what happened (hypothesis table: confirmed/refuted/inconclusive).
4. Learnings (what we now believe) and updated assumptions.
5. Next month: 3 priorities, budget changes, tests, risks, client actions needed.
6. Appendix: channel details (specialists contribute).
Rules: lead with decisions, not charts; explain variance causes; separate facts from interpretation; keep to 2-4 pages; present in Italian for clients.

### 9.5 Churn risk signals and responses
- Client doesn't understand the report -> simplify and give a verbal walkthrough.
- Results lag expectations set in sales -> reset expectations with data and show leading indicators; never hide.
- Client dependence on one person -> document.
- Disputes about lead quality -> agree a shared definition of qualified lead and a feedback loop (monthly lead review).

---

## 10. Strategy document template (one page core + appendices)

```
# Strategy: <client> — <period>
1. Diagnosis (facts, constraints, what's unknown)
   - Business model, margin, capacity, sales cycle
   - Market and demand (volume, trend, seasonality)
   - Competitors (alternatives the customer actually uses)
   - Current performance and measurement quality
   - Binding constraint: <one sentence>
2. Objective
   - North star: <metric, baseline, target, date>
   - Guardrails: CAC/CPA cap, margin, capacity
3. Customer
   - ICP (who, trigger, job to be done, objections)
   - Category entry points (top 10 situations)
4. Positioning
   - Alternatives, unique attributes, value+proof, category frame, one-line claim
5. Guiding policy (the choice)
   - What we will do, and what we will not do (explicit "no" list)
6. Channel plan
   - Channel | role (capture/creation/retention) | budget | expected outcome range | time-to-signal | owner
7. Measurement plan
   - KPI tree, tracking requirements, attribution approach, tests planned
8. Hypotheses (see section 11 template)
9. Roadmap: 30/60/90 days
10. Risks and assumptions; what would change our mind
11. Governance: cadence, owners, decisions rights
```
Quality bar (Rumelt-style): does it make a choice that could be wrong? Does it say what is not being done? If every channel appears with equal priority, it is a wish list.

---

## 11. From strategy to a falsifiable plan

### 11.1 Hypothesis card
```
ID: H-07
Belief: We believe <action> for <audience> will cause <effect>
Because: <first-principles reasoning / evidence level [S|M|W]>
Metric: <primary metric> (leading: <x>, lagging: <y>)
Baseline: <value, date> ; Target: <value> by <date> ; Minimum detectable: <value>
Test design: <A/B | geo holdout | before-after with control | no test (judgement)>
Sample/time needed: <n or weeks>
Kill rule: if <metric> < <threshold> after <time/spend>, stop and reallocate
Scale rule: if <metric> >= <threshold>, increase <budget/effort> by <x%>
Dependencies: ...
Owner / review date: ...
Result: confirmed | refuted | inconclusive ; Learning:
```

### 11.2 Rules
1. Every hypothesis states in advance what would refute it.
2. Pre-commit thresholds before looking at data (avoid p-hacking by storytelling).
3. Minimum duration: cover at least one full business cycle and exit the learning phase (ads: 2-4 weeks; SEO: 8-16 weeks for leading indicators).
4. One major change per channel per test window; otherwise confounded.
5. "Inconclusive" is a valid result; don't spin. Decide: extend, redesign, or drop.
6. Keep a decision log: date, decision, evidence, expected outcome, review date.
7. Prioritise tests using ICE/RICE (impact x confidence x ease) but weight "cost of being wrong" and learning value. [M]
8. Reality check on statistics: small SMB samples are noisy. If monthly conversions <30 per variant, A/B tests are underpowered; use sequential judgement, larger changes, or longer windows. [S: basic statistics]

### 11.3 Review cadence
| Frequency | What | Who |
|---|---|---|
| Daily/weekly | Anomaly alerts, spend pacing, tracking health | Specialists |
| Bi-weekly | Sprint review, test status | Specialists + strategist |
| Monthly | Report, hypothesis table, budget moves | Strategist + client |
| Quarterly | Re-diagnose, update plan, bigger reallocation, renewal | Strategist + client |
| Annually | Positioning check, brand/creation investment review, MMM-like retrospective | Strategist |

---

## 12. Quarterly plan template

```
# Quarterly plan Qn YYYY — <client>
North star: <metric> from <baseline> to <target> (range: cons/base/opt)
Budget: media <€>, production <€>, fees <€>; split capture/creation/retention/test = x/y/z/w
Seasonality notes: ...

Month 1 — Foundation and quick wins
  - Tracking/consent fixes; GBP; baseline dashboards
  - Launch capture campaigns for highest-intent terms
  - Start 1-2 hypotheses (H-01, H-02)
Month 2 — Build and test
  - Content/landing page production (n pieces), outreach, creative tests
  - Incrementality test (brand search pause / geo)
  - Mid-quarter checkpoint: kill/scale decisions
Month 3 — Scale and consolidate
  - Reallocate 10-20% to winning channel
  - Refresh top pages; plan next quarter
  - Quarterly review with client

Hypotheses: H-01..H-0n (cards attached)
Leading indicators dashboard: ...
Risks and mitigations: ...
Decision gates: week 4 (tracking ok?), week 8 (kill/scale), week 12 (continue/redesign)
Client actions required: access, approvals, assets, sales feedback by dates
```

---

## 13. Italian market specifics

### 13.1 Business landscape [M for Istat numbers, via search summary]
- Istat Annuario Statistico 2023 chapter on enterprises: micro-businesses (up to 9 employees) are about 4.2M, 94.5% of firms, generating 27.2% of value added; 42.3% of employees work in micro-firms; large firms (250+) are 0.1% of firms but 34.5% of value added and 44.9% of investment (data year 2022). [10]
- Implication: most clients are owner-managed micro-firms: low budgets (often 500-3,000 euro/month total marketing), low digital maturity, high trust-in-person, decisions made by the owner, little in-house marketing, often sector clusters (artigiani, studi professionali, ristorazione, ricettività, retail, manifattura B2B/export "Made in Italy").
- Regional differences: North vs South in digital adoption and purchasing power; tourism-heavy regions; local dialect and place names matter for local SEO. [W]
- Export-oriented manufacturing SMBs (distretti): B2B, multilingual SEO, trade fairs, long cycles; marketplaces like Alibaba/Amazon Business.

### 13.2 Consumer behavior notes [W/R unless cited]
- High mobile usage; DataReportal 2025: cellular connections 139% of population. [9]
- Strong reliance on reviews (Google, TripAdvisor, Trustpilot), word of mouth, and trust marks. Cash-on-delivery (contrassegno) and PayPal / bank cards; Satispay/Scalapay (BNPL) and PostePay are popular payment methods. [R]
- WhatsApp as a primary contact channel; phone calls still matter for services.
- Price sensitivity high; promotions and free shipping thresholds matter.
- Italian language search: long-tail variants, regional terms, voice queries.

### 13.3 Regulations that change the plan
Disclaimer: not legal advice; the persona flags risks and suggests consulting a DPO/lawyer.

GDPR and ePrivacy:
- Consent required for non-technical cookies/trackers and for marketing tracking (Art. 122 Codice Privacy, ePrivacy directive). Garante cookie guidelines (adopted 10 July 2021, enforceable from January 2022): scrolling or continued browsing is not valid consent; cookie walls generally not allowed; banner must allow rejecting (e.g. an "X" to continue without tracking); consent re-request frequency limited (approximately 6 months, [R]); analytics cookies may be treated like technical ones only if anonymised/aggregated. [11]
- Google Analytics (UA) was declared unlawful by the Garante in June 2022 (measure no. 224, 9 June 2022) due to data transfers to the USA; 90 days given to comply. [12] Subsequently the EU-US Data Privacy Framework (adequacy decision July 2023) changed the transfer picture [R]; GA4 use in Italy is common but depends on correct setup, consent, and data-processing agreements. Check current Garante position before advising; this has changed over time, so verify at the date of use. [W on current status]
- Meta Pixel / CAPI and Google Ads remarketing require consent; server-side tracking does not remove the consent requirement [R].
- Consent Mode v2 (Google): needed to use audiences/personalisation for EEA users from March 2024; requires a certified CMP [R].
- Lead gen: form must have separate consent boxes for marketing vs service; no pre-ticked boxes; keep consent records; handle data subject requests. Email marketing: soft opt-in for existing customers' similar products (Art. 130 Codice Privacy) [R]; Registro Pubblico delle Opposizioni for telemarketing [R].
- Data processing agreements (DPA) with tools; processing register; cross-border transfers; retain minimal data.
- AI tools: clients' private data should not be sent to tools without a legal basis; this repo's constitution forbids private client data going to non-EU models.

Advertising rules (Italy) [R, verify]:
- Codice del Consumo (D.Lgs 206/2005) and D.Lgs 145/2007 on misleading advertising; "pratiche commerciali scorrette" enforced by AGCM (Autorità Garante della Concorrenza e del Mercato) with significant fines.
- IAP (Istituto dell'Autodisciplina Pubblicitaria) Codice di Autodisciplina della Comunicazione Commerciale, and the Digital Chart on influencer/ad disclosure: content must be recognizable as advertising (#adv, #sponsoredby, "pubblicità"). [R]
- AGCOM guidelines for influencers (2024) apply to large-reach influencers (over 1M followers) and to video-sharing platforms; relevant to creator campaigns. [R, verify]
- Sector restrictions: health claims (EU Regulation 1924/2006 on nutrition and health claims), medical/dental/pharma advertising restrictions (professional codes of ethics, e.g. dentists, doctors, lawyers have rules on advertising; Legge Bersani and subsequent reforms loosened but deontology codes still apply), gambling advertising ban (Decreto Dignità, 2018), alcohol, financial services (Consob/Banca d'Italia), cosmetics, supplements.
- Price promotions: rules on "saldi" and discount displays (Omnibus Directive: show the lowest price in the prior 30 days) [R].
- Distance selling: 14-day withdrawal right (Codice del Consumo), pre-contractual information, e-invoicing (fattura elettronica) mandatory for B2B/B2C businesses [R].
- Accessibility: European Accessibility Act applicable from June 2025 for e-commerce and some services; Italian implementation via D.Lgs 82/2022 [R, verify scope for microenterprises: micro-enterprises providing services are exempt].
- Platform policies (Google/Meta) restrict categories; Google Ads certification for financial services, health data; Meta special ad categories (credit, employment, housing, social issues).
- EU Digital Services Act and DMA: affects platform behavior (e.g. Meta "pay or consent" in EU) [R]. Monitor changes in ad targeting for EEA.

Persona rule: for each regulated sector, include a compliance gate in the plan ("who validates claims before launch").

### 13.4 Practical Italian agency context [W]
- Clients often compare agency fee to media; explain fee = strategy and execution, media paid directly by client.
- Invoicing and payment: fattura elettronica, split payment for public bodies; monthly retainers common.
- Grants and incentives (e.g. credito d'imposta pubblicità, bandi regionali per digitalizzazione, Transizione 5.0 incentives) can fund digital projects; rules change frequently; verify current availability. [W]
- Trust-building through local references and case studies in Italian; free consultations are common; avoid jargon.

---

## 14. Common mistakes (checklist for the persona to avoid and detect)

Strategy:
1. Tactics before diagnosis (jumping to "let's run ads").
2. Goals mistaken for strategy ("increase traffic by 50%").
3. Too many channels for the budget.
4. No explicit "not doing" list.
5. Copying competitors without knowing their economics.
6. Ignoring unit economics and capacity.
7. Treating the funnel as linear; ignoring the loop.
8. Applying 60/40 or 95-5 mechanically.
9. Promising rankings/ROAS.
10. Strategy as a one-off document, not updated.

Measurement:
11. Trusting platform ROAS; summing platform conversions.
12. Judging SEO or brand work on 30-day last-click.
13. Testing with too little data, calling noise a win.
14. Changing many variables at once.
15. Ignoring consent loss and offline conversions.
16. Reporting vanity metrics.
17. Not defining "qualified lead" with the client's sales team.

Execution:
18. Scaling budget before tracking, offer and landing page work.
19. Resetting campaigns constantly (breaks learning).
20. Neglecting creative (on Meta, creative is the targeting).
21. Keyword cannibalisation; thin AI content.
22. Skipping compliance (consent banners, claims).

Agency:
23. Overpromising in pre-sales.
24. Reports that don't change decisions.
25. Specialist silos, each optimising local metric.
26. No decision log; repeating failed tests.

---

## 15. Specialist handoff contracts (what strategist asks and receives)

| Specialist | Strategist asks | Specialist returns |
|---|---|---|
| SEO | Demand ceiling for target topics, current visibility, 3 priority topic clusters, technical blockers, time-to-signal | Keyword/URL plan, forecast range, leading indicators, risks (cannibalisation, penalties) |
| GEO | Where do AI answers cover category queries, current citations, what sources they cite | Baseline visibility, entity/citation actions, test design |
| Google Ads | Search volume, CPC range, impression share, break-even CPA feasibility, brand-term risk | Campaign structure, budget need, forecast range, test plan |
| Meta Ads | Audience size in Italy/region, creative plan, funnel role (creation vs retargeting), lead quality plan | Creative test roadmap, budget, forecast range, learning-phase plan |
| Analytics/CRO | Tracking gaps, funnel leaks, CVR benchmarks | Measurement plan, prioritised CRO tests |

Persona's challenge questions to any specialist:
- What is the incremental effect vs doing nothing?
- What would make you wrong, and when will we know?
- What is the smallest version that tests this?
- What does this do to other channels (cannibalise or amplify)?
- What do we need from the client to make this work?

---

## 16. Worked mini-examples (illustrative; numbers invented to show reasoning)

### 16.1 Local dentist, budget 1,500 euro/month
- Diagnosis: demand is high-intent local search; reviews and GBP weak; website has no WhatsApp/booking tracking; advertising restrictions apply (professional ethics).
- Plan: foundation first (GBP, reviews, tracking calls/WhatsApp) 3 weeks; Google Ads on high-intent local terms (~600 euro), local SEO pages for treatments (~500 euro effort), Meta retargeting small (~150), test reserve (~250). No broad Meta prospecting yet.
- Hypotheses: H1 GBP review velocity from 2/month to 8/month raises map-pack calls by 20% in 8 weeks (leading: GBP calls). Kill if <5% after 8 weeks. H2: Ads for "impianto dentale <city>" achieves cost per booked first visit < 80 euro; kill if >150 after 4 weeks with 30+ clicks per day equivalent... (adjust to volume).
- Compliance gate: claims reviewed against dental advertising rules.

### 16.2 E-commerce food producer, new brand, 4k euro/month
- Diagnosis: low brand awareness; high margin on bundles; repeat purchase important; demand for category exists but brand-less.
- Plan: 50% Meta creation (video, creators), 25% Google Shopping/search, 10% email flows, 15% test. SEO on recipes/guide content. Key KPI: MER and new customer share, 60-day repeat rate.
- Measurement: MER (revenue / total spend) target >3; post-purchase survey; weekly cohorts. Platform ROAS used only for optimisation.
- Trade-off debate: Ads specialist wants to cut Meta prospecting (low last-click ROAS). Strategist: test via 3-week geo holdout on 2 regions; if total sales in test regions fall <X% vs control, Meta creation retained.

### 16.3 B2B manufacturer exporter, 6-12 month cycle
- Diagnosis: in-market share very low (<5%); decision committees; trade fairs; SEO in 3 languages; LinkedIn for reach.
- Plan: content + case studies, LinkedIn thought leadership (reach to ~all target accounts), search ads on "supplier + product" terms, CRM discipline; track pipeline and sales-accepted leads, not form fills.
- Leading indicators: target accounts reached, branded search growth, sales conversations referencing content. Review quarterly.

---

## 17. Prompts / reasoning patterns for the plugin (for skill authors)

Strategist skill should:
1. Always start with intake: business model, margin, geography, budget, sales cycle, existing data, constraints. If missing, list assumptions explicitly.
2. Run diagnosis -> binding constraint -> options -> recommendation with confidence tags [S/M/W].
3. Produce artifacts: strategy doc (section 10), quarterly plan (12), hypothesis cards (11.1), monthly report skeleton (9.4).
4. Call specialist skills with a structured brief (goal, constraints, success metric, deadline) and require a structured return (range, time-to-signal, risks).
5. Surface disagreements between specialists in a table and decide with reason.
6. Check regulatory gate for the sector and consent/tracking state before recommending paid scaling.
7. Use MCP tools of seocli for facts (SERP, keyword volumes, page analysis, Search Console) rather than guessing; label unverified numbers.
8. Output Italian to the user by default when the client is Italian; keep identifiers English per repo rules.

Decision tree for first recommendation:
```
Tracking reliable? -- no --> fix tracking (do not scale)
  yes
Offer/positioning clear and proven (reviews, conversion rate OK)? -- no --> fix offer/CRO first
  yes
Demand exists (search volume x share >= target volume)? -- no --> creation channels / niche expansion / new geography
  yes
Capture saturated (impression share > 80% on core terms)? -- yes --> creation, new queries, retention
  no --> scale capture (SEO + search ads), keep a creation slice
Budget < 3k/month? --> max 2 channels, no MMM, simple tests
```

---

## 18. Open questions and weak spots (flag to the plugin owner)

1. Italian-specific benchmarks (CPC, CVR, consent rates by sector) were not verified from primary sources in this session; use client data or build a benchmark corpus with citations.
2. Current Garante position on GA4 and on Meta Pixel after 2023 DPF: verify with fresh sources before encoding in skills.
3. AGCOM influencer guidelines details and thresholds: verify.
4. GEO measurement: no consolidated, peer-reviewed evidence on effective tactics; treat all GEO claims as [W] until independent studies emerge.
5. 60/40 and 95-5 heuristics: encode as priors with caveats, not rules. Look for Ehrenberg-Bass and Binet/Field critiques on SMB applicability.
6. Position-CTR curves and SEO timeline ranges: vendor studies disagree; encode as ranges.
7. Platform thresholds (30 conversions, 50 events) are platform guidance that changes; verify at use.
8. SMB-specific MMM alternatives (Bayesian lightweight, "synthetic control", or marketing-experiment cookbook) deserve a dedicated note.
9. Interplay with the repo's constitution: methodology must be falsifiable (VIII), sources primary (Google docs), numerical targets; encode sources and dates in each skill.

---

## 19. Glossary (EN/IT quick map for the persona)

- North star metric = metrica guida
- Binding constraint = vincolo principale
- Demand capture / creation = cattura / creazione della domanda
- Category entry point = punto di ingresso nella categoria (situazione d'acquisto)
- Incrementality = incrementalità
- Holdout = gruppo di controllo / esclusione
- Leading / lagging indicator = indicatore anticipatore / ritardato
- Hypothesis card = scheda ipotesi
- Qualified lead = lead qualificato
- MER (marketing efficiency ratio) = ricavi totali / spesa marketing totale
- Break-even ROAS = 1 / margine lordo %
- Consent Mode = modalità di consenso (Google)

---

## 20. Sources (accessed 2026-10-02)

Fetched via search in this session (summaries, not full texts; secondary sources are [M]):
1. How Brands Grow summaries: https://www.supersummary.com/how-brands-grow/summary/ ; https://sharley.substack.com/p/summary-how-brands-grow-by-byron (mental/physical availability, penetration over loyalty, double jeopardy)
2. Binet & Field "The Long and the Short of It": https://www.alexmurrell.co.uk/summaries/les-binet-and-peter-field-the-long-and-the-short-of-it ; https://growthmethod.com/long-and-short/ ; https://system1group.com/blog/binet-and-field-return-with-media-in-the-digital-age (IPA Databank 996 campaigns 1980-2010; 60/40 split)
3. 95:5 rule: https://marketingscience.info/news-and-insights/the-955-rule-why-b2b-growth-starts-long-before-the-purchase ; https://www.marketingweek.com/peter-weinberg-jon-lombardo-95-5-rule/ ; https://dreamdata.io/blog/the-95-5-rule-john-dawes (B2B Institute / Ehrenberg-Bass, Dawes 2021)
4. Messy middle: https://business.google.com/uk/think/consumer-insights/navigating-purchase-behavior-and-decision-making/ ; https://www.warc.com/content/article/google-decoding-decisions--making-sense-of-the-messy-middle/134965 (Decoding Decisions, six biases, exploration/evaluation)
5. Positioning: https://www.heinzmarketing.com/blog/five-components-of-effective-positioning-an-obviously-awesome-book-summary-part-2/ ; https://www.nateliason.com/notes/obviously-awesome-april-dunford (Dunford's components)
6. Traction/Bullseye: https://grahammann.net/book-notes/traction-gabriel-weinberg ; https://nateliason.com/notes/traction-gabriel-weinberg-justin-mares
7. Meridian: https://developers.google.com/meridian/geox ; https://developers.google.com/meridian/docs/post-modeling/channel-recommendation ; https://blog.google/products/ads-commerce/meridian/ (GeoX, calibration of ROI priors with experiments, channel calibration score)
8. Robyn: https://github.com/facebookexperimental/Robyn ; https://facebookexperimental.github.io/Robyn/ ; https://developers.facebook.com/blog/post/2024/12/19/announcing-the-python-version-of-project-robyn/ (semi-automated open-source MMM, calibration with lift studies)
9. Italy digital stats: https://datareportal.com/reports/digital-2025-italy (53.3M internet users, 89.9%; 42.2M social identities, 71.2%; 139% mobile connections; NetRetail 2025 online shoppers 35.2M, social guides ~29.8% of purchases). A Digital 2026 Italy report exists at https://datareportal.com/reports/digital-2026-italy : check it for fresher numbers before quoting.
10. Istat Annuario 2023, enterprises chapter: https://www.istat.it/storage/ASI/2023/capitoli/C14.pdf ; Censimento permanente imprese report: https://www.istat.it/it/files/2023/11/REPORTCensimprese.pdf (micro-firms 94.5%, 27.2% of value added; 42.3% of employment; 2022 data). Newer: https://www.istat.it/storage/ASI/2025/capitoli/C14.pdf
11. Garante cookie guidelines 2021: https://garanteprivacy.it/garante/doc.jsp?ID=9679893 ; https://www.iubenda.com/it/help/31253-uso-dei-cookie-nuove-regole-proposte-dal-garante-privacy ; https://www.puntosicuro.it/magazine/view-pdf/cookie-le-nuove-linee-guida-dal-garante-privacy-AR-21429/ (no scroll-as-consent, cookie wall limits, reject via X, in force Jan 2022)
12. Garante on Google Analytics (June 2022): https://www.agendadigitale.eu/sicurezza/privacy/google-analytics-possiamo-usarlo-ancora-ecco-come-nel-rispetto-della-privacy/ ; https://www.money.it/google-analytics-illegale-in-italia (measure of 9 June 2022, no. 224; 90 days to comply)

Not fetched (flagged [R]): Kotler & Keller, Godin "This Is Marketing", Rumelt, Ritson, Reforge, Binet & Field 2018, AGCM/IAP/AGCOM rules, EU Omnibus, EAA, Consent Mode v2 requirements, Google Ads/Meta learning-phase thresholds, StatCounter shares. Verify before putting any number in a client deliverable.
