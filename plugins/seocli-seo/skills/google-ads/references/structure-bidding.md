last_verified: 2026-10-02
volatile: true

# Google Ads: structure, campaign types, keywords, bidding, budgets, pacing

Tags: `[G]` Google primary source re-verified (ids in `sources.md`), `[H]` heuristic default,
`[U]` reported or unverified, never a rule. Field names are GAQL names; confirm against the current
API version before any tool is built.

## Hierarchy

- Manager account with client accounts; customer, then campaign, then ad group, then ads and
  criteria (keywords, audiences). Performance Max uses asset groups instead of ad groups; Shopping
  uses product groups or feed-only. Shared sets (negative lists), shared budgets, bidding portfolios
  and the asset library sit beside the tree.
- Conversion setup lives at customer level (conversion actions, goals); see `measurement-consent.md`.

## Structure principles

- One campaign, one goal, one budget, one bidding strategy, one geo and language intent `[H]`.
- Split brand, non-brand, competitor, generic and automated-coverage campaigns: brand CPA is often
  several times lower and a blended target damages both `[H]`.
- Consolidate for data. Fragmenting 200 conversions over 20 campaigns starves all of them `[H]`.
- Ad groups tightly themed, roughly 5-20 related keywords; single-keyword ad groups are legacy `[H]`.
- Names encode channel, goal, brand flag, geo, theme; unparseable names are hygiene, not performance `[H]`.
- Geo setting should be "presence" for local and EU businesses; "presence or interest" is a classic
  waste source unless intentional `[H]`.
- Search campaigns default to Search Partners and Display expansion on; check the segment before
  keeping them `[H]`. Language targeting must match the ad language.

## Campaign types: use when / avoid when

| Type | Use when | Avoid when |
|---|---|---|
| Search | existing intent, lead gen, brand and non-brand | monthly budget under about 10 times target CPA `[H]` |
| Performance Max | goal-led, conversion volume, feed and assets exist | no conversion tracking, strict query control needed, tiny budgets |
| Shopping (standard) | product-group control, priority, negatives, brand isolation | large catalogues already covered by feed-only Performance Max at low volume |
| Demand Gen | mid and upper funnel on video and discovery surfaces, visual demand creation | direct response without conversion data; judging it on search-like CPA |
| Video | reach, consideration, view-based remarketing | pure last-click return evaluation |
| Display | remarketing, managed placements with exclusions | main prospecting engine on small budgets |
| AI Max for Search (feature) | extend reach on existing Search via an experiment | regulated wording, brand or exact-query control `[H]` |

Names and enum values change (Demand Gen replaced Discovery and Video action campaigns; Smart
campaigns are legacy) `[U]`: read them from the live account.

## Decision shortcuts

1. At least 30 conversions a month and reliable value? If not, start Search with a capped
   manual or Maximise Clicks strategy or Maximise Conversions and fix tracking first `[H]`.
2. Retail with a feed: brand and non-brand Shopping or feed-only Performance Max, plus Search for brand.
3. Lead gen with CRM stages: Search plus offline conversion import, then Performance Max.
4. Demand creation: Demand Gen or Video, judged on reach, brand search lift, assisted conversions.

## Match types and negatives

- Exact: same meaning including close variants. Phrase: the query contains the keyword's meaning;
  absorbed the old broad match modifier. Broad: related searches using context; Google pairs it with
  Smart Bidding. Control stance: exact and phrase for brand and proven terms; broad plus Smart
  Bidding, strong negatives and clean conversions for scale `[H]`.
- Negatives do not use close variants: add plurals and misspellings; negative broad matches all words
  in any order, phrase in order, exact only the exact query.
- Levels: ad group, campaign, shared list, account-level (check availability in the account), and
  Performance Max negatives plus brand exclusions `[U]`.
- Always-on negative themes: jobs, free, DIY, login, support (unless relevant), wrong locations or
  product lines `[H]`.
- Before negating, check converting queries. Falsifier: impression volume of the intended cluster
  stays flat 14 days later.
- Search-term cuts: spend with no conversion above 2-3 times target CPA is a negative candidate;
  converting terms missing from the keyword list become exact or phrase; brand leakage into non-brand
  campaigns; share of spend on terms outside the intent. Negate on significance only: clicks at
  least twice the inverse of historical conversion rate, or cost above 2 times target CPA with zero
  conversions `[H]`.
- Privacy thresholds hide low-volume terms. If more than 30-40% of Search spend is hidden, switch to
  word-level aggregation and category insights `[H]`.
- Performance Max search reporting is category-level and not equivalent to the Search terms report.

## Bidding

- Strategies: manual CPC, Maximise Clicks (with a CPC cap, for bootstrapping), Maximise Conversions
  and Maximise Conversion Value with optional targets (CPA, ROAS), target impression share (brand
  defence), and CPM or CPV variants for reach formats. Portfolio strategies pool small campaigns of
  the same intent. Enum names for target CPA and ROAS vs optional targets differ by API version `[U]`.
- Evidence: Google recommends measuring Smart Bidding over a period with at least 30 conversions
  (a month or longer) and 50 for Target ROAS; one can turn it on without prior data `[G]` gads-smartbid.
- Learning: about 1-2 weeks or 30-50 conversions after a significant change (strategy, target
  move above 20%, budget move above 20-30%, goal change, big asset change) `[H]`. The "learning"
  status shows in the campaign primary status reasons.
- Step rules: tCPA at most 20% below recent CPA per step; tROAS at most 15-20% above; start near the
  actual 30-day figure then tighten `[H]`. An unrealistic target throttles delivery.
- Constraint reasoning: data, budget or target? Data: consolidate. Budget: reallocate to winners.
  Target: loosen by at most 20% and watch impression share recover. None: look at conversion quality
  and the landing page.
- Mistakes: weekly strategy changes; tROAS under about 15-30 conversions a month or with constant
  value; counting soft or duplicate conversions; mixing margin tiers in one tROAS campaign; raising
  budget where rank, not budget, is the limit.
- Simulators (budget, target CPA, target ROAS) give directional hints only.

## Budgets and pacing

- Semantics `[G]` gads-budget: for most campaigns daily spend can reach 2 times the average daily
  budget; the monthly charge stays within 30.4 times the average daily budget. The limits say
  "for most campaigns": confirm for the campaign type before quoting them.
- Pacing is judged against the client's agreed monthly budget: expected = monthly budget times day of
  month over days in month; ratio = actual over expected; bands 0.9-1.1 healthy, under 0.8 under,
  above 1.15 over `[H]`. Do not flag a single day above the daily budget.
- Forecast: month-to-date plus average of the last 7 days times remaining days, adjusted by weekday `[H]`.
- Reallocation: cut budget where CPA is above 1.3 times target or ROAS below 0.7 times for 14+ days
  with enough data; raise where lost impression share to budget exceeds 10% and the campaign is
  profitable `[H]`. Shared budgets hide which campaign consumes it. Mid-month budget slashes can
  restart learning.
- Budget recommendations from Google push spend up: accept only if the marginal CPA or ROAS is acceptable.
- Weekday patterns (B2B drops on weekends) and conversion delay must be accounted for.

## Sources

See `sources.md` (ids gads-smartbid, gads-budget).
