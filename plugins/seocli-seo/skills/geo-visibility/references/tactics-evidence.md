last_verified: 2026-10-02
volatile: true

# Tactics by evidence, myths, industry studies

Grades: **A** platform statement or replicated evidence; **B** single large observational or controlled
study; **C** correlational study with unclear controls; **D** anecdote or untraceable. Platform and
academic items link to `sources.md`; industry items are `[U]` (reported, with sample size) and never
become rules.

## Google's position (S1, S2) `[G]`

Generative-AI optimisation is SEO. No extra requirements, no special files, markup, chunking or
rewriting. Unique, non-commodity, people-first content, crawlable and semantically clear pages, good
images and video, and current Business Profile and Merchant Center data are what Google recommends.
These statements describe Google's own surface only; they say nothing about ChatGPT, Perplexity,
Claude or Copilot.

## Myth list

| Claim | Status |
|---|---|
| An AI text file (llms.txt) lifts citations | Google: neither helps nor harms (S2). Reported: 300,000-domain study found no relationship with citation frequency `[U]`; one 90-day vendor test saw 0.1% of AI-bot requests ask for it `[U]`. Optional hygiene for documentation sites read by coding agents |
| JSON-LD lifts citations | A matched study of 1,885 treated pages against 4,000 controls (all already cited 100+ times in AI Overviews) found no significant lift on two engines and a small decline on one `[U]`. An uncontrolled 16,851-query study showed 38.5% vs 32.0% cited `[U]`, confounded by site quality. Keep markup for rich results and entity clarity |
| Chunk content into small pieces | Google says not needed (S2); Microsoft's published advice (seen only as a summary) favours clear block structure `[U]`. Self-contained passages are cheap and harmless; artificial fragmentation is not |
| Rewrite for AI | Not needed on Google (S2); keyword stuffing negligible in the benchmark (S8) |
| Buy or farm mentions | Google: inauthentic mentions not effective (S2). Real coverage correlates with visibility, causality unproven |
| Update dates often | Freshness bias is reported mostly for ChatGPT `[U]`; fake dates are manipulation |
| A single run shows rank or presence | Refuted by sampling studies (S10, S11) |
| "Block AI bots" is one switch | False: training, search and user-triggered bots are independent (S5-S7) |

## Tiers

| Tier | Tactic | Grade | Note |
|---|---|---|---|
| 1 Eligibility | Per-bot robots.txt and WAF review; indexed and snippet-eligible; main content in server-rendered HTML; stable canonical URLs | A | Always first. JS execution by AI fetchers is not documented in the vendor pages read; test with a raw fetch |
| 2 Content | Passage that answers a likely fan-out sub-query in its first sentences under a matching heading | B | Relevance and context position are the most reproducible levers (S9) |
| 2 | Verifiable evidence: sourced statistics with date, named-expert quotes, primary citations | B | Best-supported edit in the benchmark (S8); every item must be true and sourced |
| 2 | Extractable structure: definition first, comparison tables, numbered steps, descriptive headings | B-C | Consistent with S12; reported structure studies `[U]` (+17% citation rate in an author testbed) |
| 2 | Original data, first-hand tests, local facts others lack | B- | Google calls it non-commodity (S2); effect on rerank is inference |
| 2 | Plain, fluent writing | B- | Fluency edits gained in the benchmark (S8) |
| 3 Authority | Earned third-party coverage in sources engines already retrieve | C | S13 reports earned-media bias; vendor correlation studies below. Track mention count and source quality |
| 3 | Entity consistency: name, profiles, Business Profile, Merchant data | plausible | Effect on citation unmeasured |
| 3 | Video presence where video is natural | C | Strongest vendor correlate but confounded with brand size; not a standalone tactic for small sites |
| 3 | Real freshness where the intent is time-sensitive (news, prices, versions) | B descriptive, C causal | Never churn evergreen pages |
| 4 Low or none | Schema for AI lift; AI text file; FAQ blocks only for bots | B against | Do for other reasons or skip |
| 5 Harmful | Keyword stuffing; hidden text or prompt injection; mass AI variants; fabricated stats or quotes; fake reviews, bought mentions | - | Refuse (below) |

Claims "update within 30 days gives 3.2x citations" and similar were not traceable to a method: grade
D, do not repeat.

## Industry studies (all `[U]`, correlational; vendor incentives apply)

- 75,000 brands: branded web mentions correlate about 0.66 with AI visibility, backlinks about 0.22, video-platform mentions about 0.74.
- 129,000 domains, 216,524 pages, ChatGPT citations: referring domains the strongest single predictor.
- 16.975 million cited URLs: AI assistants cite content about 25.7% newer than organic results, ChatGPT most (fetched by the research pass; the cited content is still about 2.9 years old on average).
- 2,961 runs by 600 volunteers on 12 prompts: fewer than 1 in 100 repeat runs return the same list; the same list in the same order under 0.1%. Use appearance frequency, not rank.
- US metered panel of 900 adults, March 2025: organic click in 8% of visits with an AI summary vs 15% without (US and English only).

## Refusals (manipulation)

Refuse to write: fabricated statistics or quotes, hidden text, prompt-injection strings for crawlers
or assistants, fake reviews or mention farms, parasite pages, scaled variants of one page. Research
shows rewriting attacks can promote documents and evade some filters but defences cut success from
about 50% to about 6% in lab conditions and engines keep adding them `[U]`, so the half-life is short
and the policy and legal risk is real. Offer the true, sourced alternative from Tier 2.

## Sources

- S1, S2, S8, S9, S10, S11, S12, S13 in `sources.md` (accessed 2026-10-02).
- Industry studies above: Ahrefs (brand correlation and freshness posts on ahrefs.com/blog), SE Ranking (seranking.com/blog/chatgpt-citation-factors), SparkToro (via write-ups on searchengineland.com and searchenginejournal.com), Pew Research Center (pewresearch.org). Accessed 2026-10-02 through secondary summaries unless noted; sample sizes as reported there.
