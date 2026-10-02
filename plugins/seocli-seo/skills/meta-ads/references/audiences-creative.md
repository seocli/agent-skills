last_verified: 2026-10-02
volatile: true

# Meta: audiences and creative

Tags as in `objectives-structure.md`. Almost everything here is `[H]` or `[U]`: Meta publishes few
hard thresholds, so keep them configurable per client.

## Audiences

- Broad with Advantage+ audience on often wins when the signal is strong, because delivery matches
  ads to people by creative `[U]`. Interests and lists are hints only.
- Custom audiences: customer lists (hashed; match rate often 40-70% `[U]`), website events via Pixel
  and server events, app activity, engagement (video, profile, page, form, shopping), offline events,
  catalogue. Retention up to 180 days for most on-platform and website sources `[U]`. Minimum about
  100 matched users to run, 1,000+ for stability `[U]`.
- Lookalikes: seed at least 100 people in one country, practically 1,000-5,000; source by value
  (top purchasers by lifetime value), not all visitors or page likes. With Advantage+ audience on,
  delivery may expand beyond the lookalike `[U]`.
- Exclusions `[H]`: recent purchasers and customers from acquisition, current leads, employees,
  short-window visitors when separate retargeting runs. Automated campaigns handle exclusions
  differently (some are suggestions) `[U]`.
- Geography in Italy: regions, cities with radius, postal codes; "living in" plus 10-30 km for local.
- Overlap: above 25-30% overlap between active prospecting ad sets: consolidate or exclude `[H]`.
  Overlap is a UI tool; approximate it with reach breakdowns if only API data exists `[U]`.
- Privacy: customer lists need a lawful basis and consent for advertising (GDPR), a data
  processing agreement, hashed upload and respect for opt-outs; custom audience terms accepted per account.
- Mistakes: lookalikes from page likes; seed under 1,000; lists never refreshed; retargeting pools
  so narrow that frequency exceeds 8 in 7 days; converters not excluded from retargeting; stacked interests.
- Retargeting windows `[H]`: cart and checkout 7-14 days, viewers 30 days, buyers 30-180 days for upsell.

## Creative

- Formats `[U]` (confirm in Meta's ads guide): 1:1 and 4:5 for feed, 9:16 for Reels and Stories with
  text kept clear of top and bottom overlay zones; video 15-30 seconds typical, captions on (most
  watch muted); carousel 2-10 cards; collection; catalogue; partnership ads; click-to-message.
- Primary text is truncated after roughly 125 characters; headline about 40 `[U]`.
- Concept thinking `[U]`: retrieval systems treat near-duplicate ads as one candidate, so diversity
  of angle, format, persona and hook matters more than cosmetic variants. Build a matrix: angle
  (problem, proof, offer, education, user content, comparison) x format x hook x persona.
- Mix `[H]`: 60-70% video or Reels-native, the rest static or carousel; user-style creative often
  beats polished studio ads in direct-to-consumer categories (practitioner view).
- Hook structure `[H]`: first 1-3 seconds pattern interrupt or visible outcome; then problem or
  desire, proof or demo, offer, call to action; one message per ad. Hook rate 25-35%+, hold rate
  (ThruPlay over 3-second plays) 20-40% are practitioner figures, not Meta's.
- Italian audiences: native Italian, local proof, prices with IVA, clear delivery times, calls to
  action consistent with the landing page; have copy reviewed.

## Testing frameworks

1. Concept test: 3-6 concepts with a hero asset plus 1-2 variants; budget at least 3-5 times target
   CPA per concept over 3-7 days; stop at 2-3 times target CPA with no result; promote winners.
2. Iteration test: vary one variable of a winner (hook, first frame, headline, offer).
3. Meta's A/B test tool gives exclusive splits and a confidence readout; use it for audience or
   strategy questions.
4. Dynamic or flexible creative explores combinations at low volume but explains less.
5. Cadence: see `SKILL.md` Heuristics. Accounts above EUR 30k a month often need 10+ new assets a month `[U]`.
- Mistakes: ten variants of one visual; killing ads after one day; giving the single winner all
  budget as its frequency climbs; text-heavy images; hiding a price or offer compliance requires.

## Fatigue and frequency

- Frequency = impressions over reach. Prospecting above 2.5-3 in 7 days is a caution; retargeting 5-8 `[H]`.
- Signals: link CTR down 20-30% from peak; CPM and CPA rising for the same audience; relevance
  diagnostics (quality, engagement rate, conversion rate rankings) below average on 2 of 3; negative
  feedback rising `[H]`.
- Action: new concepts, wider audience, frequency control for awareness buying, rotation.
- Derived metrics a data capability should compute: hook rate, hold rate, outbound CTR, CPM trend over 7 days,
  frequency against CTR decay, share of spend on ads older than 30 days.

## Sources

See `sources.md`.
