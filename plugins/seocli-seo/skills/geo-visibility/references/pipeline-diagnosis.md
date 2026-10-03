last_verified: 2026-10-02
volatile: false

# Pipeline stages and diagnosis

Frame from the 2026 survey (S9): GEO is a stochastic, partially observable pipeline. "Not cited" can
fail at any stage, so locate the stage before proposing a tactic.

| Stage | Question | Check |
|---|---|---|
| 0 Activation | Did the engine search the web for this prompt? | Citations or sub-queries present in the answer; answers from model memory alone carry mentions, not citations |
| 1 Crawl access | Is the engine's search bot allowed and reachable? | robots.txt per bot, WAF 403/429, server logs, published IP lists (`bot-access-matrix.md`) |
| 2 Index | Is the page in the underlying index (Google, Bing, engine's own)? | Search Console, Bing Webmaster, `seocli:inspect_indexing` (E7.4) |
| 3 Retrieval | Does the page rank for the engine's fan-out sub-queries? | Capture exposed sub-queries and test them as ordinary searches (`seocli:check_serp`) |
| 4 Selection | Is it chosen among candidates? | Citation present over n samples |
| 5 Absorption | Does the answer reuse the page's facts or wording? | Compare answer text with the page |
| 6 Prominence | Is the brand named, positioned, described accurately? | Mention extraction, accuracy review |
| 7 Behaviour | Do users click or convert? | Referral analytics for AI domains (undercounted: some assistants strip referrers), Search Console |

## Citation, mention, absorption

- **Citation**: a URL or domain attached to the answer as a source.
- **Mention**: the brand or entity named in the text, linked or not; can come from model memory.
- **Absorption**: the cited page contributes language, evidence, structure or facts (S12).

| Case | Reading | Action |
|---|---|---|
| Cited and mentioned | Strong | Maintain; check accuracy |
| Cited, not mentioned | Used as a source, not credited in text | Strengthen brand association in content and coverage |
| Mentioned, not cited | Memory or third-party coverage | Make owned pages retrievable; earn the link |
| Neither | Not in the candidate set or not chosen | Diagnose stages 1-4, only with n >= 10 per engine |

## Workflow when the sample says "rarely cited" (n >= 10 per engine)

1. Access: search bot allowed, no WAF block, logs show hits within 7 days.
2. Index status in the engine's underlying index.
3. Fan-out sub-queries: does the page rank for them organically?
4. Compare with cited pages: type, length, evidence density, freshness, language, whether competitors win through earned media (then the lever is off-page).
5. Change one thing, log the date, re-measure with a control (`measurement-protocol.md`).

Optimisation never creates retrieval: if the page is not in the candidate set, rewriting it does not
help (S9). Manipulated or fabricated evidence is covered in `tactics-evidence.md`.

## Sources

- S8, S9, S10, S12 in `sources.md`. Stage table adapted from S9 by the plugin authors.
