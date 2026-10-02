last_verified: 2026-10-02
volatile: false

# Keywords, intent, SERP overlap, SERP features, competitor gap

All thresholds are `[H]` defaults: validate on the client's data before quoting them. Data tools are
planned: `seocli:research_keywords` (E5.1), `seocli:select_keywords` (E5.2), `seocli:check_serp` (E3.1),
`seocli:find_competitors` (E5.3).

## Keyword method

1. Seeds, most reliable first: the client's Search Console queries (real, free), product and service
   taxonomy, internal search and sales questions, competitor keywords, autocomplete and related
   searches, paid search terms.
2. Expand with modifiers (best, vs, price, how to, near me, for an audience) and locale variants. Keep a
   `source` per keyword.
3. Group only when top-10 SERPs overlap, never by string similarity or stemming.
4. Enrich: volume (a provider estimate; use ranges), difficulty proxy, cost per click as a value proxy,
   current rank and URL, SERP features, trend.
5. Classify intent, map keyword to page (existing, new, or "no page": the SERP wants another format).
6. Size and prioritise (see `forecasting-prioritisation.md`).
7. Write the plan: owner, target URL, intent, volume range, current and target position, review date.

Volume is not traffic. Always attach location and language. Do not drop a low-volume commercial
keyword on volume alone. Branded demand is not an SEO growth target.

## Intent

Classic base (Broder 2002, navigational / informational / transactional; paper not re-fetched): keep
these three as the root. The usual four labels in tools add commercial investigation. Local, news and
video intents come from the SERP.

- Step A, query words: brand or "login" = navigational; buy, price, quote, hire, "near me" = transactional;
  best, vs, review, alternatives = commercial investigation; what, why, how, guide = informational.
- Step B, SERP evidence overrides step A: product and category pages or shopping units = transactional;
  listicles and comparisons = commercial; guides, forums, videos, People Also Ask = informational;
  the brand's own sitelinks first = navigational; a map pack = local; no clear majority = "mixed".
- Store label, confidence 0-1, evidence (features and type counts), date. Query-only classifiers
  top out around three quarters correct in published work, so the SERP breaks ties. Re-label quarterly.

## SERP overlap

overlap(A, B) = shared URLs in the two top-10 sets. About 3-4 or more shared = one page can serve both;
1 or fewer = separate pages; in between, adjacent topics to interlink. Use URL overlap for page
decisions, domain overlap only to find competitor sets. Cost: one SERP check per keyword, then pairwise
comparison locally. Filter ubiquitous domains (encyclopaedias, big forums) before scoring.

## SERP features

- Inventory per keyword and per device: AI answer and its cited sources, featured snippet, People Also
  Ask, video and image packs, map pack, shopping units, news, sitelinks, forums, ads count.
- A rank-1 result under many features earns less attention; record how much sits above it.
- Take 3 or more snapshots over 2-4 weeks (features vary); mobile is feature-heavier.
- Estimate a feature's CTR penalty on the client's own rows: same position, with vs without the feature.
- Win-ability priors: 0.5 if already top 3 with a matching format, 0.2 for rank 4-10, 0.05 otherwise.
- Do not treat an AI answer citation as a click. AI features appear inside the Web search type (Rule 7).

## Competitor and gap

- Business competitors differ from SERP competitors; build both. Discover SERP competitors by counting
  top-10 appearances over 100-500 keywords weighted by position (weight 11 - rank).
- Keyword gap types: missing (they rank top 20, we have no impressions), weak (we 11-50, they top 10),
  shared strong, unique. Filter by intent fit, volume floor, feasibility; group into topics.
- Content gap is a hypothesis: subtopics covered by about 60% or more of the top 10 and absent on our page are table stakes.
- Feasibility from own data: ranking 8-20 for five or more sibling queries means high; no impressions means long horizon.
- Competitor traffic figures are modelled; never present them as facts.

## Sources

Method sections are practitioner defaults. Primary references: GSC-METRICS
https://support.google.com/webmasters/answer/7042828 and AI-FEATURES
https://developers.google.com/search/docs/appearance/ai-features (both accessed 2026-10-02). The Broder
taxonomy paper (SIGIR Forum, 2002) was not re-fetched on 2026-10-02: its mirror URL returned 404.
