last_verified: 2026-10-02
volatile: false

# Unit economics, KPI trees, channel mix

## Unit economics: the first spreadsheet

- Break-even ROAS = 1 / gross margin share. 40% margin -> 2.5x on the first purchase, ignoring repeat.
- Break-even CPA = margin per sale. Target CPA = margin per sale times the share the client accepts
  to spend on marketing (agree it; with repeat purchase 30-50% of first-order margin is a common
  starting point `[H]`).
- LTV/CAC above 3 and payback under 12 months is a subscription rule of thumb `[H]`; check by sector.
- Capacity: can the client serve double the leads? If not, do not scale lead volume.
- Cash: B2B payment terms of 30-90 days; account for working capital before ramping spend.
- Marginal returns decline with spend: average ROAS overstates the return of the next euro.

## KPI trees

E-commerce:
```
Gross profit (north star) = orders x AOV x gross margin% - marketing cost
  orders = sessions x CVR ; sessions = organic + paid + direct/brand + email + social
  CVR = f(offer, price, speed, trust, checkout, device) ; AOV = f(bundles, free-shipping threshold)
  repeat rate = f(email, product, service)
```
Lead generation and local services:
```
Closed gross profit = leads x qualified% x close% x average margin per job
  leads = calls + forms + WhatsApp + walk-ins (map every source)
  qualified% = f(targeting, query intent, form friction) ; close% = f(response time, price, process, reviews)
```
Rule: one north star the client owns (gross profit, qualified bookings), 3-5 input metrics, and
leading indicators. Vanity metrics only as leading indicators with an explicit link.

Leading (weeks): index and query coverage, impressions on target queries, top-3 share of voice,
impression share, CTR, landing-page CVR, review velocity, speed to lead.
Lagging (months): non-brand organic clicks, qualified leads, revenue, payback, LTV.
Map each lagging KPI to its leading indicators and expected lag. SEO content is commonly 3-6 months
to rankings and 6-12 to revenue, varying with competition `[H]`.

## Funnel map

| Stage | SEO | GEO | Google Ads | Paid social | Other |
|---|---|---|---|---|---|
| Trigger | Informational content, local pack for urgent need | Cited in "how to / which" answers | Video, demand-gen | Reach, video, lead magnets | PR, partners, events |
| Exploration | Guides, comparisons, categories | Entity clarity, comparisons | Generic non-brand search | Prospecting, social proof | Reviews, directories |
| Evaluation | Service pages, pricing, cases | Source authority, reviews | Brand plus comparison terms, shopping | Retargeting with proof | Sales calls, WhatsApp |
| Purchase | Fast trusted pages, local data | n/a | Brand protection, high intent | Retargeting | Conversion work, payments |
| After | Support content | n/a | Customer lists | Lookalikes, retention | Email, reviews, referral |

Find the stage that leaks most (impressions -> clicks -> leads -> qualified -> closed) and the
cheapest channel to fix it.

## Budget allocation procedure `[H]`

1. Envelope: media plus production plus fee. Small-business marketing is often 5-10% of revenue.
2. Split: foundation (tracking, site, business profile, reviews), capture, creation, retention,
   test reserve 10-20%.
3. Starting priors by archetype, updated by data:

| Archetype | Foundation | Capture | Creation | Retention | Test |
|---|---|---|---|---|---|
| Local service (plumber, dentist, lawyer) | 20% | 55% | 10% | 5% | 10% |
| E-commerce, established brand | 10% | 40% | 30% | 10% | 10% |
| E-commerce, new brand, little demand | 15% | 20% | 50% | 5% | 10% |
| B2B services, long cycle | 15% | 30% | 30% | 10% | 15% |
| Hospitality and tourism | 15% | 40% | 30% | 10% | 5% |

4. Capture ceiling = volume x CTR x CVR x margin. If ceiling is below spend, shift to creation or
   new queries.
5. Minimum viable spend per channel: below the volume needed to exit learning and collect signal a
   channel is noise. Platform guidance on conversions needed per campaign changes; check the
   `google-ads` and `meta-ads` skills. For tiny budgets: fewer campaigns, consolidation, optimise to
   a higher-funnel event.
6. Under about 3,000 euro/month media: at most 2 active paid channels.
7. Reallocate monthly by marginal return, 10-20% a step.

Decision rules:
- No tracking: fix first (1-2 weeks), do not scale.
- CPA at target and impression share under 60%: scale that channel by at most 20% a week.
- CPA at target and impression share over 80%: capture saturated, add creation or new queries.
- CPA worse than target for 4 weeks after learning: diagnose offer, page, audience; reduce spend to
  the minimum rather than adding budget.
- High branded ROAS: hold budget flat, test incrementality.
- Poor lead quality: add qualifying questions, optimise to a deeper event, or use higher-intent forms.
- Topics with high paid CPC and informational intent: prioritise SEO content.
- GEO: require basic SEO health first, log a baseline.

## Forecasting

1. Driver model per channel: impressions x CTR x CVR x qualified % x close % x margin.
2. Three cases with stated assumptions: conservative, base, optimistic. Never one number.
3. Assumption sources in order: the client's history, then published benchmarks (vendor benchmarks
   are weak), then analogues.
4. SEO: volume x position CTR x rank probability, discounted for a 3-6 month ramp; published CTR
   curves disagree, fit on the client's own data. Paid: spend / CPC = clicks, times CVR; discount the
   first 4-6 weeks for learning.
5. Show the assumption that moves the result most (usually CVR, order value or close rate).
6. Update monthly. Below the conservative band for 2 months triggers a diagnosis, not more budget.
7. Base case payback over 12 months for a cash-constrained client: redesign or cut scope.

## Sources

- Arithmetic and framing from the repo research notes (marketing/strategy §3.5-3.6, §4, §5, §8); no
  primary source applies. Percent splits and thresholds are `[H]` practitioner defaults, re-fetched
  nowhere on 2026-10-02.
