last_verified: 2026-10-02
volatile: false

# Local audit model and evidence labels

Everything here is a plugin convention `[H]` or a reported statement `[U]`; no number below is a Google
rule. Rules with a Google source are in the skill and the two other references.

## Method

1. Classify the business: shop, service-area business, hybrid. Identify the vertical (restaurant, health,
   legal, home services, real estate, car trade) from the site; verticals change the profile fields that
   matter.
2. Collect facts: profile fields, website name-address-phone, markup, location pages, directory listings,
   review counts and dates, the local pack for the main queries (`seocli:check_serp` (E3.1)).
3. Score only what was measured. A dimension with no data is `n/d`, and the total is withheld if fewer
   than four of the six dimensions were measured.
4. Report with the four fields from `methodology`.

## Six dimensions (default weights, override per client)

| Dimension | Weight | Examples of checks |
|---|---|---|
| Profile | 25 | primary category, extra categories, hours, description, photos, services, verified |
| Reviews | 20 | count, rating, rhythm, owner replies, gating or incentive risk |
| Local on-page | 20 | name-address-phone in text, location pages, local proof, map, internal links |
| Name-address-phone and citations | 15 | exact match across directories, duplicates, old addresses |
| Markup | 10 | LocalBusiness validity, match with page and profile |
| Local links and mentions | 10 | local press, associations, suppliers, chamber of commerce |

## Profile checklist (use when profile data is available)

- Critical: primary category, name, address or hidden service area, phone, website, hours, verification.
- Important: description, services or products, photos (recent, real), attributes, service areas,
  booking or menu link.
- Supplementary: posts, logo and cover, videos, reply rate. Do not promise ranking from posts or from
  geotagged photos `[U]`.
- Interpretation of the points is a convention: report counts of items present, not a headline score.

## Evidence labels

- `[H]` Plugin default. `[U]` Reported by studies or practitioners, not confirmed by Google: for example
  that proximity explains more than half of local ranking variance, that a primary category change is the
  strongest single lever, that a few steady reviews matter more than a burst, that dedicated service pages
  are the strongest organic local factor. Use them to choose what to check, never to promise a result.
- `[U]` Citation tiers (profile, Apple and Bing business listings, social, major directories, data
  aggregators) and the claim that AI assistants use Bing and review sites for local answers are reported.

## Geo-grid rank tracking

No seocli tool exists for it; do not simulate it. If a client asks, say it needs a dedicated tool and
offer the local pack check for named queries instead `[H]`.

## Limits to state in every local deliverable

Seocli cannot see the true position per map point, verification status, profile insights or private
messages. Say so under NOT ASSESSED, and ask the client for a screenshot or export if the point matters.

## Sources

- No fetched Google page backs this file; it is plugin convention. Google-backed rules: `business-profile.md` and `location-pages-schema.md`.
