last_verified: 2026-10-02
volatile: true

# Google Ads and Merchant Center: sources and verification ledger

All fetches on 2026-10-02. "Confirmed" means the quoted statement was read on the page and is
phrased as `[G]` in the skill. Pages that returned 404 or an empty body are not cited as evidence.

## Sources

| Id | URL | Accessed | Supports |
|---|---|---|---|
| gads-smartbid | https://support.google.com/google-ads/answer/7065882 | 2026-10-02 | evaluate Smart Bidding over 30+ conversions (50 for Target ROAS), month or longer; can be enabled without prior data |
| gads-budget | https://support.google.com/google-ads/answer/6385083 | 2026-10-02 | daily spend up to 2x and monthly 30.4x the average daily budget "for most campaigns" |
| gads-optscore | https://support.google.com/google-ads/answer/9061546 | 2026-10-02 | optimization score is an estimate of how well the account is set to perform |
| gads-recs | https://developers.google.com/google-ads/api/docs/recommendations | 2026-10-02 | score at customer and campaign level, weight, apply/dismiss, auto-apply subscriptions |
| gads-change | https://developers.google.com/google-ads/api/docs/change-event | 2026-10-02 | 30-day window, date filter and LIMIT of at most 10,000 required |
| gads-rsa | https://support.google.com/google-ads/answer/7684791 | 2026-10-02 | RSA limits, pinning caution |
| gads-consent | https://support.google.com/analytics/answer/9976101 | 2026-10-02 | four consent signals, basic vs advanced, modeling, CMP; no numeric modeling threshold |
| gads-political | https://support.google.com/adspolicy/answer/6014595 | 2026-10-02 | EU political advertising restricted under Regulation (EU) 2024/900; no start date stated on the page |
| gads-mcprice | https://support.google.com/merchants/answer/9626903 | 2026-10-02 | internal-use restriction, GTIN requirement |
| gads-mcapi | https://developers.google.com/merchant/api/guides/compatibility/overview | 2026-10-02 | Content API for Shopping sunset 2026-08-18, progressive errors from 2026-09-01 |

## Verification ledger (DESIGN 9.3)

- Smart Bidding 30/50: confirmed as an evaluation recommendation, not an activation gate. Promoted to `[G]` with that wording.
- Daily 2x and monthly 30.4x caps: confirmed "for most campaigns" (previously [VERIFY]). Promoted to `[G]`; pacing still uses tolerance bands as heuristics.
- Merchant benchmark internal use: confirmed. Rule (D2).
- Content API sunset 2026-08-18: confirmed, now past. Promoted to `[G]`.
- change_event 30 days and 10,000 rows: confirmed.
- RSA limits: confirmed. "Ad strength is not an auction input": NOT stated on the page, kept `[U]`; the skill claims neither way.
- Consent Mode signals and modes: confirmed. The "700 clicks over 7 days" modeling threshold: not on the page, demoted to `[U]`.
- EU political ads: Google restriction under Regulation 2024/900 confirmed; date of effect not on the page. Meta's stop in the EU is `[U]`.
- Not re-verified, kept `[U]`: Quality Score and match-type help ids, audience minimum list sizes, offline conversion 90-day window, attribution defaults, shared negative list limits, enum names (primary status reasons, recommendation types, bidding strategy types), Merchant Center report field names, benchmark refresh cadence, `change_status` 90-day horizon, Auction Insights availability, developer token quotas, current API version.
- Two research URLs (Merchant 14994803, Ads 10548326) returned 404 and are not cited.

Re-verify every 60 days (volatile).
