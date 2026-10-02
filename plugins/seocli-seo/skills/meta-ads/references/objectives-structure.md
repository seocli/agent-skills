last_verified: 2026-10-02
volatile: true

# Meta: objectives, Advantage+, structure, learning, budgets, bidding

Tags: `[G]` primary source re-verified (ids in `sources.md`), `[H]` heuristic default, `[U]` reported
or unverified. Meta renames UI labels and defaults often: confirm in the live account.

## Objectives (outcome-based)

Six objectives replaced the older eleven: awareness, traffic, engagement, leads, app promotion,
sales `[U]` (secondary sources; API enum names carry an `OUTCOME_` prefix `[U]`).

| Objective | Use when | Notes |
|---|---|---|
| Awareness | reach, brand, local launch, event | control frequency; judge reach, CPM, recall lift, not return |
| Traffic | amplify content or landing pages | optimise for landing page views, not link clicks; low intent |
| Engagement | video views, messages, profile visits | cheap interactions are not value; warm-up pools |
| Leads | forms, calls, messaging, website | instant forms give volume but low quality; feed quality back from the CRM |
| App promotion | installs, in-app events | needs SDK or measurement partner; iOS limits |
| Sales | ecommerce, bookings, purchases, offline | default for revenue; catalogue ads and Advantage+ sales sit here |

- Mismatch red flags `[H]`: Traffic optimising link clicks for a client who wants sales; Engagement
  reporting return; Leads optimising "leads" with no quality loop; mixed objectives in one funnel
  without separate budgets.
- Local Italian businesses: messaging and call objectives; speed-to-lead in minutes.

## Advantage+ (automation suite)

- Pieces `[U]` (names shift): Advantage+ sales campaigns (successor of the shopping automation),
  app and leads variants, Advantage+ audience (your suggestions are hints; location and minimum
  age stay hard), Advantage+ placements, Advantage+ creative enhancements, catalogue ads, Advantage
  campaign budget (formerly CBO).
- The old cap on spend for existing customers changed; steer with the account-level existing
  customer definition and customer lists `[U]`.
- Direction `[U]`: fewer campaigns, more automation, creative diversity acting as the targeting signal.
- Reported claims to treat with caution `[U]`: one ad set with many diverse creatives beating several
  small ad sets (platform test); 20+ new ads a month correlating with better accounts (correlation);
  independent incrementality tests finding automated campaigns look better on attributed than on
  incremental return.
- Decide `[H]`: ecommerce with catalogue, at least about 50 purchases a week and healthy Pixel plus
  server events: Advantage+ sales plus one manual or broad creative-test campaign. Low volume, lead gen,
  B2B, local: manual Sales or Leads ad sets with broad targeting and Advantage+ audience on.
  Regulated or brand-sensitive clients (health, finance, alcohol): tighter controls, creative
  enhancements off, placement exclusions. Always keep an existing-customer definition for acquisition.
- Mistakes: judging automation by blended return when half the purchases are existing customers;
  changing audiences or creative daily; too few creatives.

## Structure

- Hierarchy: business portfolio, ad account, campaign (objective, budget level, special category),
  ad set (budget, audience, placements, optimisation goal, bid, event), ad (creative, copy, call to
  action, URL, tracking).
- Consolidate `[H]`: fewer campaigns and ad sets, broader audiences, more ads per ad set; overlapping
  ad sets compete in the same auction (higher CPM).
- Lean structure for an Italian SME ecommerce (EUR 3-15k a month) `[H]`: prospecting (one automated
  sales campaign or 1-2 broad ad sets); creative testing (one campaign with isolated ad sets per
  concept); retargeting and existing customers (one small campaign, watch frequency).
- Naming `[H]`: `[Client]_[Objective]_[Funnel]_[Audience/Geo]_[Offer]_[YYYYMM]`; ad names carry
  concept, format, hook. UTM: source facebook or instagram, medium paid_social, campaign and content
  from the dynamic name macros, so analytics can join.
- Ownership: the client owns business portfolio, page, Pixel, catalogue and domain verification; the
  agency has partner access with minimal rights; two admins and two-factor authentication.
- Red flags `[H]`: 10+ ad sets under EUR 10 a day; duplicate ad sets with identical audiences; many
  campaigns with the same objective and audience; over 50 ads in an ad set with no testing intent
  or under 3 with no variety; several Pixels on a domain; domain not verified; zombie campaigns
  (active, no spend for 14 days).
- Account fields worth reading when the data capability exists: effective status, budgets,
  remaining budget, learning stage info (status, events since last significant edit, last
  significant edit time), optimisation goal, bid strategy, destination type, issues, review feedback.

## Learning

- Learning starts at launch or after a significant edit and ends after about 50 optimisation events
  within 7 days of the last significant edit `[H]` (supported by a Meta help page whose text could not be
  read in full; see `sources.md`). Statuses: learning, active (learning complete), learning limited.
  Learning Limited ad sets may deliver but with unstable, usually higher CPA.
- Significant edits `[H]`: targeting change; creative edits that change the ad; optimisation goal;
  bid strategy or amount; budget change above about 20% in one edit; pause over 7 days; manual
  placements switch; adding ads in some accounts. Small budget moves and brief pauses are not.
  Duplicating an ad set resets learning on the copy only.
- Reasoning: persistent Learning Limited: consolidate ad sets, broaden audience, raise budget,
  optimise a higher-funnel event, or lengthen the attribution window to count more events.
- Log every change (date, who, why); stagger changes.
- Mistakes: scaling +50% overnight; swapping creatives in a learning ad set; repeated pause and
  unpause; budgets so split that no set reaches 50 events; reading "learning" as a problem when it is normal.

## Budgets

- Advantage campaign budget (CBO) distributes across ad sets: good for similar-intent ad sets; use
  spend limits sparingly. Ad set budget (ABO): control for tests, retargeting caps, geo splits, low volume.
- Daily budget can overspend on a day and averages over the week `[U]` (about 25% over, weekly
  cap 7 times daily): not an error. Lifetime budgets suit fixed promotions.
- Minimum budgets depend on bid and currency (about EUR 1 a day for link-click goals, higher for conversion goals) `[U]`.
- Spend caps on account or campaign protect client monthly budgets `[U]`.
- Tests: 3-5 times target CPA per concept over 3-7 days. Scale: +20% every 2-3 days or duplicate a winner `[H]`.
- Pacing: spend to date over budget times elapsed fraction; red under 70% by mid-month or over 110% `[H]`.
- Mistakes: CBO with very different audience sizes and no minimum (tests starve); ABO with tiny
  budgets; lifetime budget without end-date logic; comparing ad-set return as if budgets were equal.

## Bidding

| Strategy | Use | Risk |
|---|---|---|
| Highest volume (lowest cost) | default; new accounts, learning, scaling | CPA drifts up as budget grows |
| Cost per result goal (cost cap) | stable account with known CPA | cap too low means under-delivery; start 1.2-1.5 times observed CPA |
| Bid cap | experienced, controlled auctions | under-delivery common |
| ROAS goal (minimum ROAS) | ecommerce with clean values, about 50+ purchases a week | target too high throttles spend |
| Highest value | maximise revenue, mixed order values | chases high-value buyers |

All `[U]`/`[H]` (enum names such as lowest cost with or without cap, cost cap, minimum ROAS are
reported, not re-verified). Sequence `[H]`: highest volume first; test a cost cap once CPA is stable
2-4 weeks; ROAS goal when the value signal is clean. A bid strategy change is a significant edit.
If the account does not scale, the cause is usually creative, signal or offer, not bidding.

## Sources

See `sources.md`.
