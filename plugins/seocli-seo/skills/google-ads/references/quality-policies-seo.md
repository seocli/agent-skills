last_verified: 2026-10-02
volatile: true

# Google Ads: Quality Score, ads and assets, policies, SEO-SEA, reporting

## Quality Score

- Keyword-level 1-10 diagnostic with three parts (expected CTR, ad relevance, landing-page
  experience), each below average, average or above; empty when data is insufficient `[U]` (help
  page id not re-verified). It reflects historical performance and takes weeks to move.
- Thresholds `[H]`: impression-weighted account average under 5-6 is a problem; keywords at 4 or
  below with over 5% of spend are priority; 7+ is healthy; do not chase it on low volume.
- Reading the parts: low expected CTR means copy does not match the query (add keyword headlines,
  assets); low ad relevance means the keyword is absent from the ad or the group is too broad
  (split); low landing page means speed, mobile, content mismatch, interstitials (see `technical-seo`).

## Responsive Search Ads and assets

- Limits `[G]` gads-rsa: see Rule 6 in `SKILL.md`. Ad strength (poor to excellent) is an advisory
  indicator; Google's documentation correlates better ratings with better results, but this skill
  does not claim it is an auction input either way `[U]`. Do not stuff near-duplicate headlines to
  reach "excellent" `[H]`.
- Content mix `[H]`: keyword-matched headline, benefit, proof with numbers, offer or price, call to
  action, brand, location, urgency only when true. Pin only legal lines, with at least two options
  per pinned slot. Two to three RSAs per ad group for testing.
- Check policy topics for trademarks, unproven superlatives, excessive capitalisation.
- Assets: sitelinks (4+, ideally 6+ with descriptions), callouts (4+), structured snippets, call,
  location, price, promotion, lead form, images, business name and logo `[H]`.
- Asset performance labels (best, good, low, learning, pending): replace "low" after about 30 days and a
  few thousand impressions `[H]`.
- Auto-created assets: opt out for regulated or tone-sensitive clients `[H]`.
- Experiments: 2-4 weeks, enough conversions, roughly 100 conversions per arm and about 95% confidence
  before declaring a winner `[H]`; test strategic messages, not micro-variations.
- Demand Gen and display creative: several aspect ratios (1.91:1, 1:1, 4:5, 9:16), logo, at least one video, little text in images.
- Message match between ad, headline and landing page drives post-click conversion.

## Policies

- Policy families: prohibited, restricted (financial, health, legal, gambling, alcohol),
  editorial and technical, personalised advertising (sensitive categories), political advertising,
  misrepresentation, destination requirements.
- EU political ads: restricted under Regulation (EU) 2024/900 `[G]` gads-political.
- Approval states: approved, approved (limited), disapproved, area of interest only. Disapprovals:
  fix the cause, request review, appeal; repeated violations lead to suspension.
- Common causes: trademarks in text, broken or mismatched destination, misleading claims,
  restricted healthcare wording, unreliable claims, capitalisation.
- Account-level: payment status, suspension, advertiser verification, business info.
- Red `[H]`: over 5% of ads disapproved; final URLs with errors or redirect chains; limited ads in
  regulated verticals; disapproved sitelinks shrinking assets.

## SEO and paid together

Decision matrix per query cluster `[H]`:

| Organic position | Cluster | Action |
|---|---|---|
| 1-2 with rich result, brand | brand | defend with ads only if competitors bid on the brand; else save budget |
| 1-3 non-brand | non-brand | test incrementality: pause ads on a geo or time subset and measure total clicks; do not assume |
| 4-10 or absent | any | keep or raise ads; they cover the gap while SEO builds |
| low ad conversion, high organic conversion | any | reuse the organic page and message in ads |

- Brand bidding: gains message control, protects against conquesting and takes SERP space; cost is
  possible cannibalisation of organic clicks. An older published experiment on a very well known
  brand found little incremental value `[U]`; competitor presence changes the outcome, so use a
  holdout. Keep brand in its own campaign, low budget, target impression share.
- Keyword mining: converting search terms become SEO content targets; queries with impressions but
  low CTR become ad-copy and title tests; winning headlines become title and description tests.
- Shared landing pages: speed and mobile experience help quality and conversion on both sides;
  product structured data must match the feed price and availability.
- Join key between Search Console queries and ad search terms is the normalised query; overlap data
  from the Ads UI is not an API assumption `[U]`.
- Avoid bidding on informational queries organic already owns when intent is too early, and URL
  expansion sending traffic to unrelated pages.

## Reporting

- Monthly structure `[H]`: summary (spend, conversions, value, CPA/ROAS vs target, month and year over
  year, 3 insights, 3 actions); efficiency by type with brand separate; funnel to sessions and
  revenue; share of voice (lost impression share); search-term actions; creative; Shopping and
  Performance Max product winners and losers; tests with decisions; next-month plan with leading
  indicators and falsifiers.
- Agree the primary KPI (CPA, ROAS, new customers, blended efficiency) and budget in advance and report variance to target.
- Maths: ROAS = conversion value over cost; break-even ROAS = 1 over gross margin; break-even CPA =
  order value times margin (or lifetime-value based). Marginal returns fall as spend grows: use
  experiments or simulators for incremental return.
- Avoid vanity metrics (impressions, CTR alone, the account score).

## Sources

See `sources.md` (ids gads-rsa, gads-political).
