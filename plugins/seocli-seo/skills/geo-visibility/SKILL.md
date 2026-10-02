---
name: geo-visibility
description: Generative-engine visibility (GEO, AEO, "AI visibility") - Google's position that it is SEO, the myth list, citation vs mention vs absorption, the AI-bot access matrix, tactic tiers with evidence grades, the repeated-sample measurement protocol and the refusals. Knowledge for the GEO analyst and GEO developer; not a command.
user-invocable: false
---

# geo-visibility

## When to use

Load it for questions about being cited, mentioned or recommended by AI answers (Google AI Overviews
and AI Mode, ChatGPT search, Perplexity, Claude, Copilot), for AI-bot robots.txt decisions, and for
planning or reading a GEO measurement. Use `technical-seo` for crawl, rendering and structured data,
`content-quality` for helpful-content and spam policy, `seocli-tools` for prices and `max_credits`,
`methodology` for the four fields of a recommendation. Source ids S1..S13: `references/sources.md`.

## Rules

Facts re-fetched on 2026-10-02: `[G]` platform primary, `[A]` academic primary.

1. **Google: generative-AI optimisation is SEO.** No additional requirements for AI Overviews or AI
   Mode beyond being indexed and snippet-eligible. `[G]` S1
2. **Google says you do not need** new machine-readable or AI text files, special markup or Markdown,
   tiny chunks, writing differently for generative AI; inauthentic brand mentions are not effective;
   structured data is not required for generative AI visibility. `[G]` S2
3. **Mechanism:** core ranking systems plus query fan-out (related searches across subtopics). `[G]` S1
4. **`nosnippet`, `data-nosnippet`, `max-snippet`, `noindex` apply to AI features.** `[G]` S1
5. **AI-feature traffic sits in Search Console's Web search type;** Google also points to a Generative
   AI performance report: check the property before promising it. `[G]` S1, S2
6. **Google-Extended** is a robots token for Gemini training use; it does not affect Search inclusion
   or ranking. Google common crawlers always respect robots.txt; user-triggered fetchers are separate. `[G]` S3, S4
7. **OpenAI:** OAI-SearchBot = ChatGPT search (allow; ~24 h to apply); GPTBot = training; ChatGPT-User =
   user-initiated, robots.txt may not apply. Independent settings. `[G]` S5
8. **Perplexity:** PerplexityBot = results, not training; Perplexity-User = user-triggered, generally
   ignores robots.txt; check User-Agent plus published IPs. `[G]` S6
9. **Anthropic:** ClaudeBot = training; Claude-User = retrieval for a user's question; Claude-SearchBot =
   search indexing (blocking may cut visibility). Do not block by IP: robots.txt becomes unreadable. `[G]` S7
10. **The "up to 40%" GEO gain is conditional:** benchmark of 10,000 queries where the rewritten source
    is already in context; keyword stuffing gave negligible gains. Not a promise. `[A]` S8
11. **Only already-retrieved content can be causally improved;** no technique shows a stable,
    cross-platform, longitudinal effect; relevance and context position are the most reproducible. `[A]` S9
12. **Absorption differs from citation** (the page shapes the answer text). High-influence pages were
    longer, structured, rich in definitions, numbers, comparisons, steps (602 prompts). `[A]` S12
13. **Answers vary run to run:** many domain differences sit inside the noise floor; single runs are
    misleadingly precise; the top recommendation changed across six identical runs on 4 of 6 prompts. `[A]` S10, S11
14. **Query language, not location, selected the market** (ChatGPT, 234 runs, one study). `[A]` S11

Measurement conventions (decision D1, binding; DESIGN section 11):

15. **Never one run.** Report appearance rate per engine with n and a Wilson 95% interval
    (`references/measurement-protocol.md`).
16. **No blended score, no rank.** Engines are reported separately.
17. **Below n=10 per engine say "insufficient sample";** never "not cited" or "not visible".
18. **Query language selects the market:** Italian prompts as the main set plus an English control set.
19. **Cache reuse only re-displays an existing batch** (`cache <date>`); it adds no samples. New samples
    cost credits; the estimate shows samples x engines.
20. **Keep citation, mention and absorption apart;** training-data presence is not live retrieval.

## Heuristics

- Open with the eligibility floor: indexed, snippet-eligible, search bots allowed, content in server HTML. `[H]`
- Find the failing pipeline stage first (`references/pipeline-diagnosis.md`). `[H]`
- Tiers (`references/tactics-evidence.md`): 1 eligibility (grade A); 2 passage answers, sourced stats and
  quotes, extractable structure, original data (B); 3 earned coverage, entity consistency, real freshness
  (C); 4 schema or AI text files for citation lift (no demonstrated effect); 5 harmful. `[H]`
- Tiers 2-3 are hypotheses with falsifier, control, window and leading indicator, never promises. `[H]`
- Prompt sets: 70% or more unbranded, 2-3 paraphrases of key prompts, frozen and versioned. `[H]`
- Monitor path: weekly K=5 per engine gives n=20 a month; read a rolling 4-week rate. `[H]`
- Private client data never goes to outside-EU models in prompts; public keywords may. `[H]`
- First-party counts rule absolute levels; sampling serves relative competitor comparison. `[H]`
- Bot access (training vs search vs user-triggered) is the client's decision: show consequences. `[H]`

## Do not recommend

- llms.txt as a ranking lever, or any AI text file, Markdown mirror or "AI markup" sold as a citation switch.
- Schema or FAQ blocks only for bots; FAQ rich results as a GEO goal.
- Chunking pages, or rewriting for "AI style".
- Mention farming, bought mentions, fake reviews, parasite pages.
- Fabricated statistics or quotes, hidden text, prompt-injection strings aimed at crawlers or assistants.
- Mass-produced AI page variants (scaled content abuse).
- One "AI visibility score", a rank inside answers, or a verdict from one run.
- Blanket "block all AI bots" rules that also block search bots; IP-blocking Anthropic crawlers.
- robots.txt as protection from user-triggered fetchers (use authentication).
- Promising citation lift, or "+40%" as an expected gain.

On a manipulation request, refuse that part in one sentence (spam policy, short half-life, legal and
reputation risk) and offer the true, sourced alternative.

## Italian market notes

- Query language selects the market: Italian prompts ("migliore", "consigliato", "quale scegliere",
  "prezzo", "recensioni", "vicino a me", Lei and tu) plus an English control set, reported separately.
- Entity aliases (with and without S.r.l., accents, apostrophes as in "dell'azienda") in mention matching.
- Earned media in Italy: national press, vertical publishers, Wikipedia IT, Italian forums and review
  platforms; take the cited Italian domains per vertical from samples, do not assume.
- English sources can leak into Italian answers where Italian coverage is thin; test with the control set.
- AI Overviews and AI Mode are reported live in Italian in Italy (AI Mode from about 2025-10-08); not
  verified on a Google page, say "reported" and check the live SERP. `[U]`
- Local intents lean on Business Profile and PagineGialle, Tripadvisor, TheFork: keep NAP consistent.

## References

- `references/pipeline-diagnosis.md`: stages 0-7, citation/mention/absorption table, workflow; read on demand.
- `references/bot-access-matrix.md`: training, search, user-triggered bots, patterns, checks; read on demand.
- `references/tactics-evidence.md`: tiers and grades, myth list, industry studies, refusals; read on demand.
- `references/measurement-protocol.md`: D1 sampling, Wilson interval, reporting template; read on demand.
- `references/extractability.md`: developer checklist for extractable pages; read on demand.
- `references/sources.md`: source ids S1..S13 with URL and access date; read on demand.
