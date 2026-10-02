---
last_verified: 2026-10-02
volatile: false
---
# KB file formats

## Header of every KB file

```
---
sector: <sector-slug>
file: glossary.md
updated: YYYY-MM-DD
---
```

## index.md

```
| id | title | type | file | source URL | accessed | last_verified | volatile | confidence |
|---|---|---|---|---|---|---|---|---|
| K-001 | Closed season for X | regulation | regulations.md | https://... | 2026-10-02 | 2026-10-02 | true | high |
```

Ids are unique and never reused. A researcher working in parallel uses its own range (the lead assigns
K-100..K-199 to researcher 2, and so on); the lead renumbers nothing, only merges rows.

## Entry shapes (id in the heading, one source line)

- glossary: `### K-010 <term IT> / <term EN>` then synonyms, jargon users search, one-sentence meaning.
- entities: `### K-020 <name>` then kind (category, brand, organisation, place), what it is, relation.
- calendar: `### K-030 <event or period>` then dates or rule, territory, volatile true.
- regulations: `### K-040 <rule>` then jurisdiction, scope, summary, "verify with the client's legal advisor".
- audience-questions: `### <intent>` then bullets `K-050 <question in the users' words>`.
- sources: ranked table: rank, name, body type, URL, what it is good for, accessed.
- topic-map: `### <cluster>` then seed topics, supporting ids, intent mix.
- notes/<topic>.md: summary in own words, key facts with ids, open gaps; short attributed quotes only.

Every entry ends with `Source: <URL> (accessed YYYY-MM-DD, confidence)`.

## Build checklist

1. Check the sector path exists or create it; read `index.md` if present (never duplicate an entry).
2. Search, fetch public pages only, one at a time; skip paywalls and logins.
3. Write entries in own words; one `index.md` row per entry.
4. Finish with: files written, entries added, low-confidence entries, gaps.

## Update checklist

1. List index rows older than 60 days (volatile) or 180 days (stable).
2. Re-fetch each source; change `last_verified` only after confirming; fix or mark `low` if it moved.
3. A dead source: find an official replacement or lower confidence; never delete silently.
