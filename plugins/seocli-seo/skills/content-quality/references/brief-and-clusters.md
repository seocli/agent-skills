last_verified: 2026-10-02
volatile: false

# Content briefs and topic clusters

All `[H]` defaults. Data: `seocli:check_serp` (E3.1), `seocli:research_keywords` (E5.1),
`seocli:select_keywords` (E5.2); without them, ask the user for SERP exports.

## Brief template

1. **Mode.** Improve an existing URL or create a new page for a keyword.
2. **Intent and format.** Intent label with evidence, and the page type the SERP rewards
   (`sxo-page-fit.md`). Prefer the format the SERP already shows.
3. **SERP analysis.** Top 5 real competitors after removing excluded domains (encyclopaedias, social
   networks, marketplaces, forums, news, tool sites, AI platforms, government and university sites, and
   URL patterns such as tag, author, feed, login, cart, terms). Table: format, depth, structure, media.
4. **Gaps.** Topic gaps (missing subtopics), depth gaps, quality gaps. Priority = impact x our advantage / effort.
5. **Information gain (mandatory).** Name the exact new value versus the current top results: own data,
   case study with outcomes, expert quote, first-hand experience, original framework.
6. **Outline.** H2s with the question each answers; subtopics covered by about 60% of the top 10 are table stakes.
7. **On-page.** Primary keyword in the title (front), H1, URL slug, meta description, early in the
   text and in one image alt text; secondary terms where natural. No fixed frequency.
8. **Credibility.** Author, reviewer, sources, update date, disclosures.
9. **Internal links.** 3-5 suggestions from real existing pages only.
10. **Measure.** Falsifier and leading indicator in the `methodology` format.

Two guard rules: every heading, keyword or question must be something the client can truthfully
deliver; a hub page lists only child pages that exist. Never name research tools in client text.

## Cluster architecture

- Expand a seed to 30-50 variants (modifiers such as best, vs, price, how to, for beginners, template,
  mistakes, checklist; question words); if fewer than 30, run a second pass using People Also Ask as seeds.
- SERP overlap between two keywords (shared top-10 URLs): 7-10 same page; 4-6 same cluster, separate
  pages allowed; 2-3 adjacent, interlink; 0-1 separate. Ties at 3-4: shared domains, same intent, volume
  ratio of 10 or more (own page); default to cohesion. Different SERP features (map pack vs snippet)
  mean different content types. Filter the most common domains first.
- Never cluster by text similarity or stemming. Cost: one SERP check per keyword, comparisons local.
- Shape: one pillar, 2-5 clusters, 2-4 spokes each, 5-21 pages. Fewer than about 5 distinct intents
  usually need one page, not a hub.
- Links: spoke and pillar both ways, 2-3 sibling links, 0-1 cross-cluster, at least 3 incoming links per
  page, reachable from the pillar in 2 clicks, no anchor above 40% of links to a page.
- Cannibalisation rule: no two pages share a primary keyword.
- Scorecard: coverage of planned pages, orphans 0, pillar connectivity 100%, links per page at least 3.
- Report at topic level: clicks, impressions, weighted position, ranking queries.
- Where the SERP rewards a tool, directory or template, plan that asset instead of an article.

## Sources

Practitioner methods; none verified against a Google primary source. The note that no fixed keyword
frequency exists rests on STARTER https://developers.google.com/search/docs/fundamentals/seo-starter-guide
(accessed 2026-10-02).
