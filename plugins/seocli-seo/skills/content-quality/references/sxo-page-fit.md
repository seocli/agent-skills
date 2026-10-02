last_verified: 2026-10-02
volatile: false

# Page-type fit against the SERP (SXO)

All `[H]`: a practitioner method built on observable SERP signals. A technically perfect page of the
wrong type rarely ranks. Needs `seocli:check_serp` (E3.1) for the top 10 and `seocli:check_page_html`
(E6.7) for the target page; without them, ask the user for both.

## Eight page types

Classify with this priority order when signals conflict: tool, local, comparison, product, landing,
service, hybrid, blog (default).

| Type | Typical signals |
|---|---|
| tool | calculator, generator, interactive widget, app behind the page |
| local | map pack, address, opening hours, "near me" or town in the query |
| comparison | tables, "vs", "best", "alternatives", several products side by side |
| product | price, add to cart, variants, shopping units |
| landing | single offer, one call to action, little navigation |
| service | what we do, process, quote request, case studies |
| hybrid | two strong types on one page (guide plus category) |
| blog | article, guide, how-to, news, dated byline |

## Procedure

1. Classify the target page.
2. Classify each top-10 result by the same table; ignore results that are not real competitors
   (encyclopaedias, giant marketplaces, forums) when counting, but record them as evidence.
3. Dominant type share: above 60% strong consensus; 40-60% mixed (name the two types, recommend the one
   that matches the business goal); below 40% fragmented (no mismatch claim, differentiation chance).
4. With a strong consensus and a different target type, record a mismatch with the severity below.
5. Only after type fit, look at depth, UX, structured data, media, authority and freshness gaps.

## Mismatch severity

| Target vs SERP | Severity |
|---|---|
| blog vs product pages | Critical |
| blog vs comparison | High |
| product vs informational | High |
| landing vs tool | High |
| service vs local | Medium |

Severity follows revenue impact (`methodology`), so lower it for a page with no commercial goal.

## Evidence for what users want

Use only signals visible in the SERP: People Also Ask clusters (definitional = awareness, evaluative =
consideration, comparative = decision), ad copy themes (commitment, trust, cost, time barriers), related
searches ("alternatives" = dissatisfaction, "vs" = active comparison, "reviews" = trust seeking),
snippet format (paragraph, list, table, video), sources cited in an AI answer. Do not invent personas;
each user need must trace to a signal.

## Optional gap score (0-100), reported separately from any technical score

Page type 15, content depth 15, UX 15, structured data 15, media 15, authority 15, freshness 10.
Score only measured dimensions; with a missing input write `n/d` (see `methodology`).

## Sources

Practitioner method; no Google primary source states these thresholds. The one verified Google fact
used, that AI features need no special page type, is AIFEAT
https://developers.google.com/search/docs/appearance/ai-features (accessed 2026-10-02).
