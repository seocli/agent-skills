last_verified: 2026-10-02
volatile: true

# Structured data, deprecation ledger, hreflang

Tags as in `crawling-indexing.md`.

## Structured data rules

- `[G:sd-policies]` (updated 2026-07-10) JSON-LD preferred; Microdata and RDFa accepted. Pages blocked by
  robots.txt or `noindex` are not eligible. Content must be visible, representative, complete in required
  properties, original. No guarantee of display. Violations can lead to a manual action that blocks rich
  results without affecting normal web ranking. Check with the Rich Results Test and URL Inspection; they
  may miss some errors.
- `[G:review-snippet]` (updated 2026-09-08) If the reviewed entity controls the reviews about itself, the
  page is ineligible for star results. Reviews must be visible; no fake or undisclosed incentivised
  reviews; include an aggregate rating with several reviews; do not aggregate from other sites.
- `[G:local-business]` Required `name` and `address`; `geo` with at least 5 decimals; `priceRange` under
  100 characters; `url` a working link to the location; most specific subtype. Detail: skill `local-seo`.
- `[H]` Generate JSON-LD on the server from the same object that renders the visible page; use `@graph` with
  `@id` to link Organization, WebSite and WebPage; escape `<` in JSON; one source of markup (theme plus
  plugin duplicates are common). Prices as numbers with a currency, ISO 8601 dates, absolute URLs.
- `[H]` Common errors: invisible content marked up, stale price in JSON-LD, invalid JSON from template
  concatenation, markup only in a client-rendered widget, `availability` as free text.

## Deprecation ledger

- `[G:faq]` The FAQ search feature is no longer shown in Google Search; the page cites a May 2026 changelog
  ("will no longer appear starting May 7, 2026" in the entry dated 8 May) and a 15 June 2026 documentation
  update that removed the feature page. Since September 2023 it had been limited to well-known government
  and health sites. The page does not tell owners to remove markup.
- Contradiction settled: earlier notes gave inconsistent dates (May versus June 2026) and conflicting
  advice on removal. Reading: the feature stopped appearing in May 2026, the documentation was withdrawn on
  15 June 2026, markup is harmless but yields nothing in Google Search. Position: do not sell FAQ markup,
  do not spend effort removing it unless a template change makes it cheap.
- `[G:gallery]` The current search gallery (30 features) lists Article, Breadcrumb, Carousel, Event, Job
  posting, Product, Recipe, Video, Review snippet, Local business, Organization, Software app, Course list,
  Dataset, Discussion forum, Education Q&A, Employer aggregate rating, Image metadata, Math solver, Movie,
  Profile page, Q&A, Speakable, Vacation rental, Subscription and paywalled content. FAQ and HowTo are not
  listed. Re-read the gallery before promising any visual feature.
- `[U]` Reported, from search snippets and press, not confirmed here: HowTo reduced in 2023; seven types
  phased out on 12 June 2025 (Book Actions, Course Info, ClaimReview, Estimated Salary, Learning Video,
  Special Announcement, Vehicle Listing) with Search Console support ending from January 2026; sitelinks
  search box removed October 2024. Do not state these as facts to a client without opening the changelog.
- `[H]` Value ranking for a small business: Organization, Breadcrumb, Product, Local business, Article,
  Video. Everything else only with a demonstrated use.

## hreflang

- `[G:hreflang]` Return links are mandatory (otherwise tags are ignored); each version lists itself and all
  others; ISO 639-1 language with optional ISO 3166-1 alpha-2 region, region alone not allowed; `x-default`
  for unmatched users, typically a selector page; URLs fully qualified; HTML, HTTP header and sitemap
  methods are equivalent, choose one.
- `[H]` Pointing to the same set from HTML and from the sitemap invites drift: one method per site section.
  Alternates must be 200, indexable and canonical to themselves; a canonical pointing at another language
  version cancels the tag.
- `[H]` Repair order: canonical alignment, self-reference, return links, codes (`en-GB`, not `en-UK`),
  `x-default`, protocol and trailing slash match, then the method.
- `[H]` Severity: missing self-reference or return links Critical; invalid code or non-canonical target
  High; missing `x-default` Medium.
- Italian cases `[H]`: `it-IT`, `it-CH`, `de-IT`, `fr-IT`; an Italy-only site needs none; serve language by
  URL, not by IP.

## Sources

- sd-policies: https://developers.google.com/search/docs/appearance/structured-data/sd-policies, accessed 2026-10-02
- review-snippet: https://developers.google.com/search/docs/appearance/structured-data/review-snippet, accessed 2026-10-02
- local-business: https://developers.google.com/search/docs/appearance/structured-data/local-business, accessed 2026-10-02
- faq: https://developers.google.com/search/docs/appearance/structured-data/faqpage, accessed 2026-10-02
- gallery: https://developers.google.com/search/docs/appearance/structured-data/search-gallery, accessed 2026-10-02
- hreflang: https://developers.google.com/search/docs/specialty/international/localized-versions, accessed 2026-10-02
