last_verified: 2026-10-02
volatile: true

# Google Ads: conversion tracking, consent, attribution, audiences

Tags as in `structure-bidding.md`.

## Tag architecture and conversion actions

- Google tag or Tag Manager, conversion linker, conversion actions with type, status, category,
  value settings (default value, always use default), counting type (one per click for leads, every
  conversion for purchases), attribution model and lookback windows.
- Only primary conversion actions feed bidding and the "Conversions" column; secondary ones appear
  in "All conversions". Typical error: micro-conversions set as primary.
- Avoid double counting: a GA4 import and a native tag for the same event. Prefer one source of
  truth for bidding and compare with back-end orders (within +-15% acceptable `[H]`).
- Ecommerce: pass transaction id, dynamic value and currency. Leads: value rules or offline import.
  Target ROAS with constant value is meaningless.
- Red flags `[H]`: more than 3 primary actions of mixed intent; conversions close to clicks (page-view
  type); Search conversion rate above 30% (double counting) or below 0.5% (broken); all conversions
  far above conversions; no value on ecommerce; action untouched since a site migration; call
  conversions without a duration threshold; GA4 import and native tag together.

## Enhanced conversions

- Web: hashed first-party data sent with the tag to improve matching and recover lost conversions.
  Leads: hash email or phone at submission and upload with the click id or later by email.
- Account-level settings include acceptance of customer data terms and the leads feature flag.
- Tag health is shown mainly in the UI diagnostics; an API proxy is zero conversions in 14 days for a
  previously active action `[H]`.

## Consent Mode v2 (EEA and UK)

- Signals and modes: see Rules 8 in `SKILL.md` `[G]` gads-consent. The documentation names no numeric
  threshold for when conversion modeling starts. A figure of about 700 ad clicks per domain and
  country over 7 days is reported by practitioners `[U]`; do not quote it as a rule.
- Symptoms of a broken setup `[H]`: sudden conversion drop from the date of a banner or tag change;
  remarketing lists not growing; consent warnings in the tag diagnostics; banner loading after tags.
- With API data alone the modeled share is not directly visible `[U]`. Audit item: ask the user to
  confirm the CMP, that it is certified by Google, the mode (basic or advanced) and the signals.

## Offline conversion import

- For B2B and lead gen: upload click-id matched conversions with value via the upload service, or
  hashed email/phone with enhanced conversions for leads. Window up to 90 days from click `[U]`
  (research figure, confirm in the conversions documentation); aim to import within 24-48 hours.
- Weight value by stage probability so value bidding optimises toward pipeline; at least 30 uploads a
  month is a working minimum `[H]`.
- Typical errors: click id not captured in the CRM, timezone format, duplicate order ids, upload
  before click time, event too recent.

## Attribution

- Data-driven attribution is the default for new actions; older rule-based models are mostly retired
  `[U]`: read the model from the account. Lookback defaults reported: click-through 30 days (1-90),
  view-through 1 day (1-30) `[U]`.
- Ads and GA4 differ by design (cross-channel vs Google Ads touchpoints). Use blended efficiency
  (revenue over total marketing spend) at business level.
- Conversion lag: the last 3-7 days under-report for long cycles; report by conversion date or wait.

## Audiences

- Own data: remarketing, customer match, app and YouTube; Google audiences; custom segments; lookalike-style
  expansion in Demand Gen. On Search and Shopping use observation unless restricting on purpose;
  on Display, Video and Demand Gen use targeting. Performance Max audience signals are suggestions.
- Minimum list sizes reported: 1,000 active users for Search, 100 for Display, customer match
  1,000 matched `[U]`; confirm in the account.
- In the EEA customer match and remarketing require consent and the Google EU user consent policy;
  without correct signals the lists do not populate.
- Mistakes: audiences as targeting on Search (reach collapses); only "all visitors"; converters not
  excluded; membership too long or too short; sensitive categories in personalised advertising.

## Sources

See `sources.md` (id gads-consent; others unverified and marked `[U]`).
