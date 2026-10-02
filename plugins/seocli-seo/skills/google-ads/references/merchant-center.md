last_verified: 2026-10-02
volatile: true

# Merchant Center: feed quality, disapprovals, price benchmarks

Account data tools are not available yet. Method and thresholds only.

## Feed essentials

- Core attributes: id, title (up to 150 characters, front-load brand, type, key attributes),
  description, link, image link, availability, price, condition, brand, GTIN where a manufacturer
  GTIN exists, MPN, identifier-exists, category, shipping and tax (country dependent; EU prices include
  tax), item group id for variants, apparel attributes, product type, custom labels 0-4 for margin,
  season, bestseller `[U]` (specification page not re-verified; limits can change).
- Sources: primary feed, supplemental feeds, API-based management, automated crawl of structured
  data (needs Product markup consistent with the page). The Content API for Shopping was sunset on
  2026-08-18; integrations use the Merchant API `[G]` gads-mcapi.
- Quality principles `[H]`: titles as people search; feed price and availability identical to the
  page (mismatch causes disapproval); images without watermarks or promo text; unique descriptions;
  correct categories; no all-caps; sale price with its effective dates.
- Free listings and paid surfaces are separate destinations; control with excluded destinations.

## Disapprovals and account issues

- Item-level: missing value, invalid value, mismatched value (price, availability, image vs page),
  landing page not working, policy (prohibited, restricted, misrepresentation, unsupported claims,
  promotional overlays).
- Account-level: misrepresentation, missing return policy or contact, business info, website
  verification and claim, shipping policy; suspensions block all products.
- Metrics: products by status (approved, pending, disapproved, demoted), top issues by affected
  products, percent approved. Red `[H]`: disapproval above 5% of the catalogue; any critical account
  issue; out-of-stock products still advertised; approved products with zero impressions for long.
- Wasted spend on products `[H]`: high cost with no conversion, price above benchmark, few reviews,
  weak image, high clicks while out of stock.
- Mistakes: sale price not synced; stale availability; GTIN missing on branded items (no benchmark,
  weaker matching); one feed for all countries without feed labels; one product group for everything;
  no supplemental rules for titles and custom labels; Performance Max fed with low-margin or out-of-season items.

## Price benchmarks: licence and use (decision D2)

Facts `[G]` gads-mcprice: pricing reports are only for the internal use of the retailer or those
acting on its behalf; data may not be resold, publicly displayed, advertised or aggregated across
businesses; benchmark price needs valid GTINs, while sale-price suggestions do not strictly need them.

Plugin rules (product decision D2):
1. Only from the client's own connected Merchant Center account.
2. Never in the cross-account cache or any cross-client summary or benchmark.
3. Never in pre-sales: a prospect's account is not connected.
4. Shareable artifacts: only the client's own report, with the note "uso interno".
5. Until a data capability exists, do not display benchmark figures; describe the method only.

## Decision logic with a benchmark (own account, own report)

Insight concepts: benchmark price (typical price that wins auctions for the matched product),
price gap (your price vs benchmark), distribution (higher, similar, lower), predicted uplift from
suggested prices (needs conversion reporting). Report field names differ between API versions: not
relied on here `[U]`.

| Price gap | Reading | Action `[H]` |
|---|---|---|
| over +10% above, clicks but low conversion | price likely culprit | test price or promotion; lower bids or remove from growth campaigns |
| over 10% below | margin leak | test a higher price; push into the hero campaign |
| within +-5% | competitive | invest in title, image, reviews |

- Also compare total price: shipping cost and speed, promotions, ratings, return policy.
- Judge trends, not single points; benchmark refresh cadence is not confirmed `[U]`.
- Join price gap with product-level cost, clicks, conversions and return: spend share on products priced
  above benchmark, potential savings, best "benchmark minus price" with strong return to scale.

## Sources

See `sources.md` (ids gads-mcprice, gads-mcapi).
