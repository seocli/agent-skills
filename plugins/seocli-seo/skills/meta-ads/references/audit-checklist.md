last_verified: 2026-10-02
volatile: true

# Meta: audit sequence, checklist, KPIs, reporting, SEO link

Thresholds are `[H]` unless stated; adapt to margin and vertical and prefer the client's own history.
Account data tools are not available yet: an audit today is a guided interview plus method, with
unmeasurable checks listed under NOT ASSESSED. Findings follow `methodology`. Output per section is
pass / warn / fail with evidence (metric, value, window, threshold, entity), never one opaque score.

## Sequence

1. Business goal and unit economics (margin, order value, lifetime value, lead-to-sale rate, sales cycle).
2. Signal quality (section 2 below) before any performance reading.
3. Structure and learning, then audiences, then creative, then bidding and budget, then results vs business.

## Questions to ask the user

- Margin, target CPA or ROAS, monthly budget, sales cycle? Break-even ROAS = 1 over gross margin.
- Who owns the business portfolio, page, Pixel, catalogue, domain? How many admins; two-factor on?
- Pixel and server events live? Which events, which values? CMP and consent behaviour (test rejecting)?
- Which attribution setting do reports use? What does analytics or the back end say for the same period?
- How fast are leads contacted, and does the CRM feed quality back?
- Any rejections, restricted account notices, special-category products (finance, health, alcohol)?
- Recent site, price or catalogue changes; recent edits to budgets or audiences?
- Needed later from the data capability: structure tree with learning status, insights with explicit windows,
  creative-level performance, audience list, tracking health, change log, policy issues.

## Checklist

### 1 Foundations
- Client owns assets; agency partner access; 2+ admins; two-factor on. Red: agency owns assets.
- Business and domain verification done; payment valid, spend limit set, EUR, time zone Europe/Rome.
  Red: restricted account, unsettled balance, wrong time zone.
- Special category declared where required.

### 2 Tracking and data quality
- Pixel fired in last 24 hours; server events active; deduplication present; events ViewContent, AddToCart,
  InitiateCheckout, Purchase with value and currency or Lead. Red: zero or missing value, browser only,
  double Purchase, test site tagged.
- Match quality: Purchase 8+ (red under 6); server coverage about 75%+ of browser events.
- Meta purchases vs back end: explainable within 15-30%; red above 100% or under 40%.
- Consent banner works when rejecting. UTM on 95%+ of ads (else analytics shows direct).

### 3 Structure
- Learning Limited share: red if over 30% of spend.
- Budget per ad set at least EUR 20-50 a day for ecommerce prospecting, or 5-10 times CPA; red: 10+
  ad sets under EUR 10 a day.
- Overlap above 30%; duplicated ad sets; zombie campaigns.
- Objective and optimisation event aligned (Sales to Purchase; Leads to quality-fed lead; no link-click goals for conversion aims).

### 4 Audiences
- Acquisition excludes customers (except automated campaigns with the existing-customer definition);
  retargeting windows sane; interests do not shrink prospecting under about 500k for conversion goals;
  lists refreshed (red: unrefreshed over 180 days); geo matches footprint.
- Frequency: prospecting under 3 in 7 days; retargeting under 6-8.

### 5 Creative
- 4-10 distinct concepts per ad set (red: 1-2, or 30+ undifferentiated).
- Creative age: red if over 60% of spend is on ads older than 45 days with falling CTR.
- 9:16 assets present; captions; safe zones; Italian copy; compliant claims.
- Diagnostics: link CTR under 0.5% weak (feed typical 0.8-1.5%, Reels lower) `[U]`; hook rate under 20% weak;
  CPM +30% week over week red; quality rankings below average.
- Landing page: speed (largest content paint under 2.5 s), mobile, message match, price with IVA clear,
  trust, checkout friction (see `technical-seo`). Landing page views under 70% of clicks: slow page or accidental clicks.

### 6 Bidding and budgets
- Highest volume unless a cap is justified; delivery under 80% of budget with a cap is red.
- Pacing 0.9-1.1; budget edits above 20% are logged; scaling rules documented.

### 7 Results vs business
- Blended efficiency (total revenue over total marketing spend) vs break-even; platform ROAS vs
  break-even ROAS; CPA vs target; new-customer share; acquisition cost vs lifetime value.
- Lead gen: cost per lead and per qualified lead, lead-to-appointment-to-sale; speed-to-lead under
  5-15 minutes; red: not contacted within an hour, junk instant forms, no CRM feedback loop.
- Trend: last 28 days vs prior 28 on spend, CPM, CTR, conversion rate, CPA, frequency.

### 8 Governance
- Naming, change log, weekly report cadence, rejection log, creative library, test backlog, results recorded.
- Policy risk exposure; creator usage rights contracts.

## KPIs and reporting

- Layers: business outcomes (revenue, return, orders, new customers, acquisition cost, qualified
  leads, booked revenue); platform efficiency (spend, CPM, link CTR, link CPC, conversion rate, CPA,
  platform ROAS, frequency, reach); funnel indicators (landing page view rate, cart rate, checkout
  completion, hook and hold); creative per concept; data quality (match quality, deduplication,
  learning-limited share, coverage).
- Formulas: ROAS = purchase value over spend; blended efficiency = total revenue over total
  spend; new-customer efficiency = new-customer revenue over spend; acquisition cost = spend over new
  customers; payback = acquisition cost over monthly gross profit per customer; CPM = spend over
  impressions times 1000; link CTR = link clicks over impressions; hook rate = 3-second plays over
  impressions; hold rate = ThruPlay over 3-second plays.
- Italian benchmark ranges `[U]` (practitioner, not Meta): CPM EUR 5-15 for broad ecommerce prospecting
  (Q4 higher; finance and B2B 15-40); link CTR 0.8-1.5% feed, 0.3-0.8% Reels and Stories; ecommerce
  landing-to-purchase 1-3%; lead page 5-15%; instant-form CPL EUR 5-25 B2C local, 30-150 B2B. The
  client's 28/90-day history comes first.
- Cadence `[H]`: daily spend, delivery and rejections check; weekly performance, creative, learning,
  decisions and tests; monthly business review, allocation, learnings, roadmap. Show period over period
  and vs target; annotate launches, edits, promotions and seasonality.
- Pitfalls: mixing attribution windows across periods; UI vs API defaults; reach summed across days;
  unique metrics summed across breakdowns; time zone and currency mismatches; mixing event types.

## Full-funnel and SEO link

- Roles `[H]`: SEO and GEO capture existing demand and citations; Google search ads capture
  high-intent demand now; Meta creates and shapes demand, nurtures and recovers.
- Synergies: organic visitors feed retargeting pools; search queries drive hooks and objections; ad
  comments and messages feed FAQ content; track branded search in Search Console during Meta
  flights and use geo or time holdouts, never claim causality without a test; one conversion-rate
  programme across channels; test offers on Meta then reuse winners in search ad headlines and titles;
  CRM lists for customer match and cross-channel suppression.
- Budget order: capture channels first (brand search, shopping), then prospecting when blended
  efficiency is healthy; new brands lean on Meta, search-led categories (emergency trades) start with Google.
- Brief: say which channel does which job and which KPI judges it; do not judge Meta on last-click
  analytics alone or on view-through alone.
- Landing pages receiving paid traffic with poor speed, indexing or canonical issues are paid
  efficiency fixes (see `technical-seo`).

## Sources

See `sources.md`.
