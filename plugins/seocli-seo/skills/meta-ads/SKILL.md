---
name: meta-ads
description: Meta (Facebook and Instagram) paid social knowledge - objectives, Advantage+, structure and learning, budgets and bidding, audiences, creative testing, Pixel and Conversions API with match quality and deduplication, attribution, EU and Italian rules, audit checklist with thresholds and KPIs. Methodology only until the account data tools exist; not a command.
user-invocable: false
---

# meta-ads

## When to use

Load it to plan, review or explain Meta paid social for a client: objective choice, structure,
learning status, creative testing, tracking quality, attribution, compliance, reporting. Use
`google-ads` for Google, `marketing-strategy` for unit economics, channel mix and incrementality
design, `search-analytics` for Search Console and GA4 joins, and `methodology` for the finding and
recommendation format.

**Data availability.** The Meta account data tools are not available yet: a post-launch server epic
will add them, read-only. Today this skill gives method, audit checklists with thresholds,
triage tables and questions. Do not request CSV or other exports as a data source; numbers the user
states in conversation are `user_supplied`, unverified, and labelled so. Never invent a tool name or
an account figure; put needed data under NEEDS as a capability ("ad set list with learning status
and last significant edit") without a tool name. An audit is therefore `partial`: say what was not assessed.

## Rules

Platform facts, each with a source id defined in `references/sources.md`.

1. **Measurement first.** Verify the conversion signal (Pixel plus server events, deduplication,
   value, match quality) before any performance finding; Meta optimises toward the signal it receives.
2. **Deduplication** needs the browser event and the server event to share the same event name and
   the same event id; for web events the maximum deduplication window is 48 hours, for offline
   events 7 days (meta-dedup, meta-emq).
3. **Event Match Quality** scores events 0-10; 8-10 is high and below 8 leaves room to improve the
   server implementation (meta-emq).
4. **Hashing.** Email, phone, names, date of birth, gender, city, state, zip and country are
   SHA-256 hashed before sending; `fbc`, `fbp`, client IP address and client user agent are sent
   unhashed; sending both IP and user agent on every server event may improve matching (meta-capi-params).
5. **Special ad categories** are declared at campaign creation; the documented values are
   `HOUSING`, `FINANCIAL_PRODUCTS_SERVICES`, `EMPLOYMENT`, `ISSUES_ELECTIONS_POLITICS`, `NONE` (meta-sac).

## Heuristics

Defaults, overridable per client, always against the account's own 28/90-day history first.
Detail: `references/audit-checklist.md`.

- Learning: Meta help pages are reported to state about 50 optimisation events in 7 days after the last
  significant edit; one fetch supported this but the page text was thin, so treat it as a default `[H]`.
  Significant edits (targeting, creative, goal, bid, large budget change, long pause) restart learning.
- Reasoning order: unit economics (break-even ROAS = 1 over gross margin), signal quality, structure,
  creative, skeptical measurement, slow change, business-terms reporting `[H]`.
- Pick the objective that matches the lowest-funnel event with at least about 50 events a week per
  ad set; if purchases are too few, step up one event level (cart, checkout, lead) `[H]`.
- Learning: do not judge before about 72 hours or 50% of target events; wait 7 days or about 50
  events unless spend reaches 2-3 times target CPA with zero results `[H]`.
- Budget edits: stay within about 20% per step, every 48-72 hours; larger jumps or pausing over a
  week are treated as likely learning resets `[H]` (not confirmed from Meta's help text here).
- Daily budgets can overspend on a day and balance over the week; a day above the daily budget is
  not an error `[U]`.
- Budget per ad set at least target CPA times 50 over 7 days per day for conversion goals; tests: 3-5
  times target CPA per concept over 3-7 days `[H]`.
- Consolidate: more than 30% of spend in Learning Limited ad sets, or more than 10 ad sets under
  EUR 10 a day, is a structure finding `[H]`.
- Frequency over 7 days: prospecting above 2.5-3, retargeting above 5-8 is a caution `[H]`.
- Creative fatigue: link CTR down 20-30% from peak, CPM up, CPA up on the same audience `[H]`.
  Refresh with new concepts, not recolours; 3-5 new concepts every 2-4 weeks per EUR 5-10k monthly `[H]`.
- Hook rate (3-second plays over impressions) below 20% weak; 25-35%+ good `[H]` (practitioner figures).
- Server event coverage of about 75% of browser events and Purchase EMQ of 8+ are targets `[H]`.
- Platform purchases vs back-end orders within 15-30% is explainable; above 100% suggests duplicates,
  below 40% a tracking gap `[H]`.
- Platform ROAS more than 3 times analytics ROAS with no explanation is a tracking audit trigger `[H]`.
- Pacing ratio 0.9-1.1 of expected; delivery under 80% of budget means a cap or audience is too tight `[H]`.
- Cost cap initially at about 1.2-1.5 times observed CPA, revisited weekly `[H]`.

## Do not recommend

- Judging an ad set before it has had time and events to learn, or after one day.
- Budget jumps of +50% overnight, or constant audience and creative swaps in a learning ad set.
- Splitting budget over many tiny ad sets so none leaves learning.
- Optimising for clicks or engagement when the goal is sales or leads.
- Reporting platform ROAS as incremental truth, or Advantage+ blended ROAS without separating existing customers.
- Pixel without server events, or server events without deduplication.
- Layering many interests that shrink prospecting audiences below the volume learning needs.
- Lookalikes built from page likes or tiny seeds.
- Cosmetic creative variants presented as testing.
- Boosted posts from the app on agency-managed accounts.
- Copy that asserts or implies personal attributes (health, finances, religion) or before/after health claims.
- Promising political or social-issue delivery in the EU.

## Italian market notes

- Consent before the Pixel fires: Italy's data-protection authority 2021 cookie guidance (equally
  prominent reject, no cookie walls, renewed consent after at most six months) applies; run a CMP
  with Consent Mode v2 for Google and consent signals for Meta. What may be sent server-side for
  non-consented users is a legal question for the client's adviser.
- Gambling advertising is banned by the Decreto Dignita (2018) with narrow exceptions; financial
  products need prior authorisation in Meta's verification flow (accepted registers, for example
  Bank of Italy, Consob, IVASS, to be checked `[U]`).
- Prices to consumers include IVA; "was" prices must follow the Omnibus rule (lowest price in the
  previous 30 days). Check claims with the client's legal adviser.
- Influencer and partnership content: Italian advertising self-regulation and regulator guidance
  expect clear disclosure; use the paid partnership label.
- Reported EU shifts to monitor `[U]`: "less personalised ads" choice for EU users from 2026 and the
  stop of EU political and social-issue ads from October 2025.
- Local businesses: WhatsApp and call objectives are common; lead quality depends on follow-up in
  minutes. Targeting "living in" plus 10-30 km radius or CAP.
- Seasonality: saldi, Black Friday, Ferragosto, Natale; compare year over year; CPMs rise in Q4.
- Italian copy, Italian proof (recensioni, spedizione, pagamento alla consegna, Satispay, PayPal),
  time zone Europe/Rome, currency EUR; client text uses 1.234,56 and DD/MM/YYYY.

## References

Read on demand.

- `references/objectives-structure.md`: objectives, Advantage+, structure, learning, budgets, bidding.
- `references/audiences-creative.md`: audiences, exclusions, creative strategy and testing, fatigue.
- `references/measurement.md`: Pixel, server events, match quality, deduplication, attribution, lift, consent.
- `references/eu-italy-policies.md`: EU and Italian rules, special categories, policies and rejections.
- `references/audit-checklist.md`: audit sequence, checks with thresholds, questions, KPIs, reporting, SEO link.
- `references/sources.md`: source ids, URLs, access dates, verification ledger.
