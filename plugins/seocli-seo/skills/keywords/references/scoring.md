last_verified: 2026-11-25
volatile: false

# Shortlist score

The only place of the score formula and its tables. Every flow of `/seocli-seo:keywords` that shows
"extra clicks" uses it. Tables are `[H]` defaults: they change only when the user asks. General method
of an uplift forecast: `search-analytics/references/forecasting-prioritisation.md`, "Uplift forecast";
this file is the fixed instance of it.

## Formula

```text
extra clicks per month = round( (demand x CTR(target band) - today's clicks) x P(current band) )
```

P multiplies the difference. Same data, same result: by hand or with code execution, always the same
steps below. Difficulty is not in the formula (see the end).

## One Search Console read

Position, impressions, clicks and the CTR curve all come from ONE `seocli:get_search_console_data`
read of the last 90 final days: `from` and `to` explicit, `dimension` `query`, `sort` `impressions`,
`limit` 1,000. The 28-day Search Console columns of `seocli:research_keywords` (`site`) never enter the
score, the band or the name of a group: one number, one period. They are only shown, for a variant
group the read does not have, when `omitted` is above 0 (the read returns at most 1,000 rows; `omitted`
counts the rows not returned, so a query with no row is not proved absent): see "Current band". With
`omitted` 0, no row is absent. The report says how many queries the read covers (`rows` plus `omitted`).

## Inputs

- **Variant group:** a row is a group of variants. Its Search Console numbers are the sum of impressions
  and of clicks of the variants that have a row, and the position averaged weighted by impressions. Its
  name is the variant with the most impressions in the read. On a tie, or with no row in the read: the
  user's seed if it is among them, otherwise the first of them in alphabetical order.
- **Demand per month:** by current band. Bands `1` to `7-10` (page one): impressions in the read
  divided by 3. Bands `11-20`, `21-100`, `absent`, `unknown`: the monthly volume, the low end of the range
  when two volumes differ. Those bands with no volume: the impressions in the read divided by 3, labelled
  "minimum". Neither: demand unknown, the row goes last as "unknown", never 0 clicks.
- **Current band** (average position rounded, half up): `1`, `2-3`, `4-6`, `7-10`, `11-20`, `21-100`,
  `absent` (not in the first 100: no Search Console row, or in a `missing` list), `unknown` (no data).
  A group with no row in the read, `omitted` above 0:
  1. some variant has `site` in `seocli:research_keywords`: `absent` for the calculation (target, P,
     demand). The table shows the `site` position (mean weighted by impressions) and impressions with the
     source "Search Console 28 days, occasional": a query missing from the read has fewer impressions than
     its smallest row, and such positions come from a few searches (Shopping listings, single queries),
     not from the regular results. Today's clicks are not there: unknown, the calculation uses 0 and the
     result is labelled "maximum, today's clicks unknown".
  2. no variant has `site`, `search_console.status` is `ok` and `search_console.truncated` is not true:
     `absent`, today's clicks 0 measured.
  3. otherwise (status not `ok`, or `truncated` true): `unknown`.
- **Today's clicks per month:** clicks of the query in the read divided by 3. Search Console associated
  and no row for the query (`omitted` 0, or case 2 above): 0 (a measured zero). No Search Console:
  unknown; the number is then a ceiling, labelled "maximum, today's clicks unknown".
- **Brand query:** contains the domain label without separators (`etruriafishing`), its words in
  sequence (`etruria fishing`) or a brand term the user gave. Fixed rule, so the curve is the same every
  round. Brand queries stay out of the curve.

## Target and P

| Current band | Target position | P |
|---|---|---|
| 1 | - | out of the shortlist, "already first" |
| 2-3 | 1 | 0.3 |
| 4-6 | 3 | 0.5 |
| 7-10 | 3 | 0.5 |
| 11-20 | 5 | 0.4 |
| 21-100 | 10 | 0.2 |
| absent or unknown | 10 | 0.15 |

P comes from the priors of "Uplift forecast" (0.3-0.5 near page one, 0.1-0.25 for a new page on a
contested term).

## CTR of a position

CTR(target) is the CTR of the band whose position is the target: target 1 uses band `1`, target 3 band
`2-3`, target 5 band `4-6`, target 10 band `7-10`.

1. **Client curve** (with Search Console). Recipe in
   `search-analytics/references/gsc-analyses.md`, "CTR curves": non-brand queries of the read, per band
   `1`, `2-3`, `4-6`, `7-10`, `11-20`, CTR = sum of clicks / sum of impressions, desktop and mobile
   together. Every non-brand query of the read counts, off-topic ones too: the curve measures the site.
   A band needs 1,000 or more impressions in the read; a band under it shows "-" in the curve.
2. **Fixed table** otherwise (no Search Console, or a band under 1,000 impressions). Every number that
   uses it is labelled "estimated" (modelled, never measured):

   | Band | `1` | `2-3` | `4-6` | `7-10` | `11-20` |
   |---|---|---|---|---|---|
   | CTR | 0.25 | 0.12 | 0.06 | 0.03 | 0.01 |

3. **Inversion check** on the curve of step 1 once the table fills its empty bands: CTR(1) >= CTR(2-3) >=
   CTR(4-6) >= CTR(7-10) >= CTR(11-20). If a band has a lower CTR than a worse band, the whole curve is the
   fixed table, every CTR labelled "estimated", and the report says "client curve inverted" with the
   measured CTR per band.

Say per row which source the CTR has: "client curve" or "estimated".

## Order and rounding

1. Compute with all decimals, round to a whole number once at the end, half up.
2. A negative result, or one that rounds to 0, shows 0 with the reason "no estimated gain" (a computed 0,
   not a missing one).
3. Sort by extra clicks descending, then demand descending, then keyword alphabetical. Unknown rows last,
   in the same order.
4. Each row shows its factors: demand and its source, current band, target, CTR and its source, P,
   today's clicks.

## Worked example

Demand 1,000 per month, fixed table, today's clicks = demand x CTR of the current band (0 for `21-100`
and `absent`):

| Band to target | Calculation | Extra clicks |
|---|---|---|
| 2-3 to 1 | (1,000 x 0.25 - 120) x 0.3 | 39 |
| 4-6 to 3 | (1,000 x 0.12 - 60) x 0.5 | 30 |
| 7-10 to 3 | (1,000 x 0.12 - 30) x 0.5 | 45 |
| 11-20 to 5 | (1,000 x 0.06 - 10) x 0.4 | 20 |
| 21-100 to 10 | (1,000 x 0.03 - 0) x 0.2 | 6 |
| absent to 10 | (1,000 x 0.03 - 0) x 0.15 | 5 (4.5 rounded up) |

Use it as the check of a by-hand calculation: the six results are 39, 30, 45, 20, 6, 5.

Demand check: band 11-20, 150 impressions in the read, volume 2,400: demand 2,400 (volume), not 50.

Occasional check: no row in the read, `site` position 2 with 1 impression, volume 2,400: band `absent`,
(2,400 x 0.03 - 0) x 0.15 = 11, labelled "maximum"; the table shows position 2 as occasional.

## Difficulty and later sources

- `seocli:check_keyword_difficulty` is a context column, never a factor. In Italy 0 or `null` is
  frequent and does not mean "easy".
- Described, not used: a trend factor on demand (E12.5), Ads volumes as the second figure of the volume
  range (E12.6), a difficulty estimated from the SERP (E12.2, always labelled an estimate).
