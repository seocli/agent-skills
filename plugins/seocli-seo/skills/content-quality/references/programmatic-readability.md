last_verified: 2026-10-02
volatile: false

# Templated page sets and Italian readability

Google's rule (SPAM, accessed 2026-10-02 at https://developers.google.com/search/docs/essentials/spam-policies):
many pages made mainly to rank, without added value, are scaled content abuse, whatever tool made them.
Numbers below are `[H]`. Crawl text via `seocli:crawl_site` (E6.1) when it exists.

## When a set is acceptable

- Each page has data or content no sibling has (own inventory, real local information, measured values).
- The page is useful if no search engine existed (standalone-value test).
- A person reviewed a sample. If a page cannot be made useful, keep it out of the index.

## Gates (one table)

| Check | Warn | Stop |
|---|---|---|
| Programmatic set size without per-page review | 100+ pages | 500+ without written justification |
| Location pages | 30+ | 50+ without justification (60% or more unique) |
| Unique share of words across the set (nav and footer excluded) | under 40% = thin | under 30% |
| Words per page | under 300: review | n/a |
| Records sharing 80% or more of fields | near-duplicate | n/a |

Unique share = unique words / total words x 100, measured across the set; it needs the crawled text
(`n/d` without it). Page-count gates are prompts for questions, not automatic halts in a chat.

## Rollout

Batches of 50-100 pages, 2-4 weeks of monitoring (indexed vs intended, impressions, errors), human
review of 5-10%, canonical and sitemap `lastmod` set to the real data update time, monthly audit.

## Doorway test (towns, comuni, services)

Swap the place name: if the page still reads the same, it is a doorway. Real local content: team,
premises, landmarks, local projects, reviews, services actually offered there. Synonym swaps do not count.

## Gulpease (Italian readability) `[H]`

G = 89 + (300 x sentences - 10 x letters) / words. Higher is easier. Rough reading: below 40 hard even
for graduates, 40-60 hard for middle-school level, above 60 comfortable for a general public, above 80
easy. Aim for 60 or more on general web copy, lower is acceptable for specialist audiences. It is a
check on clarity, not a ranking factor. Also: sentences of 15-20 words, paragraphs of 2-4 sentences.

## Sources

SPAM https://developers.google.com/search/docs/essentials/spam-policies (accessed 2026-10-02). Gates and
Gulpease bands are practitioner or published-formula defaults, not re-fetched; the formula is from Lucisano
and Piemontese (1988), cited from memory, hence `[H]`.
