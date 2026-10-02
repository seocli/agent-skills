last_verified: 2026-10-02
volatile: true

# Meta Ads: sources and verification ledger

All fetches on 2026-10-02. Meta business help pages are partly script-rendered: several fetches
returned thin summaries. Only statements read on a page are phrased as `[G]` in the skill.

## Sources

| Id | URL | Accessed | Supports |
|---|---|---|---|
| meta-dedup | https://developers.facebook.com/docs/marketing-api/conversions-api/deduplicate-pixel-and-server-events | 2026-10-02 | 48-hour window; match on event name plus event id |
| meta-emq | https://developers.facebook.com/docs/marketing-api/best-practices/omni-optimal-setup-guide | 2026-10-02 | match quality 8-10 high; dedup windows 48 hours web and 7 days offline; send deduplication parameters |
| meta-capi-params | https://developers.facebook.com/docs/marketing-api/conversions-api/parameters/customer-information-parameters | 2026-10-02 | SHA-256 fields; fbc, fbp, IP, user agent not hashed; IP plus user agent may improve matching |
| meta-sac | https://developers.facebook.com/documentation/ads-commerce/marketing-api/audiences/special-ad-category | 2026-10-02 | special_ad_categories values |
| meta-learning-help | https://www.facebook.com/business/help/412951382532338 | 2026-10-02 | summary mentions "50 events in 7 days" and restart after large budget changes; page body not readable in full |

## Verification ledger (DESIGN 9.3)

- Dedup 48 hours web / 7 days offline: confirmed twice. `[G]`.
- Match quality scale: 8-10 high confirmed; "target 8+ for Purchase/Lead, 6-8 acceptable" is the
  research's practitioner wording, kept `[H]`. A "75% server coverage" target was NOT stated by the
  guide (it speaks of real-time sending); kept `[H]`.
- Hashing and unhashed fields: confirmed. `[G]`.
- Special ad categories: five values confirmed; the gambling-and-gaming value in the research is NOT on the page. Corrected.
- Learning "about 50 events in 7 days": the help-page summary mentions it, but the page body could
  not be read; kept `[H]`, not promoted. Budget tolerance "about 20%", the list of significant
  edits and "daily budget up to 25% over": not confirmed, `[H]`/`[U]`.
- Insights API: removal of 7d_view / 28d_view on 2026-01-12 and retention limits (13 months, 6
  months, 37 months): the changelog and breakdowns pages fetched did not contain them; the claim
  rests on secondary press, so it stays `[U]`. Re-check against the Marketing API changelog.
- Match quality help page, budget help page: not retrievable (404 or empty body); not cited as evidence.
- EU political-ads stop (October 2025) and "less personalised ads" (January 2026): secondary press
  only, `[U]`. Google's own restriction under Regulation 2024/900 is confirmed in the Google skill.
- Not re-verified, `[U]`: objective names, Advantage+ components, bid strategy enums, attribution
  window defaults by objective, audience retention and size limits, creative specs, Italian financial-services proofs.

Re-verify every 60 days (volatile).
