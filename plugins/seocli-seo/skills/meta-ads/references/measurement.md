last_verified: 2026-10-02
volatile: true

# Meta: Pixel, server events, match quality, deduplication, attribution, lift, consent

Tags: `[G]` re-verified (ids in `sources.md`), `[H]` heuristic, `[U]` reported or unverified.

## Browser plus server events

- Meta recommends a redundant setup: browser Pixel and server events together. Browsers lose events
  to tracking prevention, blockers and consent denial; server events carry first-party data `[G]` meta-emq.
- Standard events: PageView, ViewContent, Search, AddToCart, AddToWishlist, InitiateCheckout,
  AddPaymentInfo, Purchase, Lead, CompleteRegistration, Contact, Schedule, SubmitApplication,
  StartTrial, Subscribe. Purchase needs value and currency; catalogue needs content ids and type;
  order id helps deduplication `[U]`.
- Server event basics `[U]`: event name, event time (recent), action source, user data; website
  events also carry the source URL and the client user agent.
- Routes `[H]`: partner or native integration for SMEs; server-side tag container for custom stacks;
  a gateway or direct code for developers.

## Event Match Quality

- Scale 0-10; 8-10 is high, below 8 leaves room to improve `[G]` meta-emq. Targets `[H]`: 8+ for
  Purchase and Lead, 6-8 acceptable, under 6 poor. Per-event, in the events manager.
- Improve by sending hashed email, hashed phone with country code (+39), names, location fields,
  external id, the click id `fbc` and browser id `fbp`, client IP and user agent `[G]` meta-capi-params.
- Hash with SHA-256 after normalising (lowercase, trimmed, phone digits only); never hash `fbc`, `fbp`, IP or user agent `[G]`.
- `fbc` comes from the `fbclid` click id; preserve it from landing page to checkout `[H]`.

## Deduplication

- Browser and server events must share event name and event id; web deduplication window 48 hours,
  offline 7 days `[G]` meta-dedup, meta-emq.
- Check in the events manager that events show as received from both browser and server and that
  a healthy share is deduplicated; typically 30-60%+ `[H]`. Server coverage of at least about 75% of browser events `[H]`.
- Duplicate Purchase counts inflate return and are the most common audit finding `[H]`. Do not
  split one order into several events; tags firing again on a thank-you page refresh create duplicates.

## Attribution

- Conversion campaigns use attribution windows chosen at ad set level: 1-day click, 7-day click
  (common default for web sales), 28-day click in some setups, 1-day view, 1-day engaged view `[U]`.
- Reported change: the insights API removed the 7-day and 28-day view windows and many click+view
  combinations on 2026-01-12, and limited retention for unique-metric and hourly breakdowns to 13
  months and frequency breakdowns to 6 months, with 37 months for aggregates `[U]`. The Meta
  changelog pages that should confirm this could not be read on 2026-10-02: verify in the live
  account and API changelog, and degrade gracefully when a requested window is unavailable.
- Always state the attribution setting, report time basis, time zone and currency next to any
  number. Default windows differ between the ads UI and the API, a classic mismatch `[U]`.
- Incremental attribution (opt-in) optimises toward people predicted to convert because of the ad.
  Vendor-cited gains are not independent: validate with a lift test `[U]`.

## Platform vs reality

- Reported conversions include modelled conversions after iOS tracking limits and consent changes;
  differences of 10-40% vs analytics are common `[U]`. Compare with back-end revenue; a persistent
  gap above 30% means audit the tracking `[H]`.
- Lift tests (randomised holdout) give incremental conversions; they need budget, often tens of
  thousands of euro over 2-4 weeks `[U]`. For smaller accounts: geo holdouts with matched Italian
  regions over 4-6 weeks `[H]`. Marketing-mix modelling only for large spend (above about EUR 100k a month) `[H]`.
- Never judge on under 3-7 days, on single-day swings, or on last-click analytics alone (undervalues
  prospecting) or platform view-through alone (overvalues retargeting).

## Consent

- Pixel and server events must respect consent (GDPR, ePrivacy). With consent signals the Pixel stays
  quiet until consent; personal data for non-consented users should not be sent server-side without a
  lawful-basis analysis `[H]` (legal question for the client).
- Maintain a CMP integrated with Google Consent Mode v2 and Meta's signals. The US limited-data-use
  mechanism is not the EU mechanism `[U]`.
- Document the controller and processor roles (data processing agreement) with the client; Meta's
  business tools terms require the advertiser to hold the lawful basis `[U]`.

## Diagnostics a data capability should expose

Pixel or dataset last fired time and availability; events by detection method (browser vs server);
match quality per event; deduplication rate; domain verification; iOS measurement config; errors;
test events. Common mistakes: Pixel only; server events without deduplication; missing or wrong-currency
value; stale event times; wrong action source; several Pixels firing; ignoring match quality under 6;
a conversion event too rare so learning never ends.

## Sources

See `sources.md` (ids meta-emq, meta-dedup, meta-capi-params).
