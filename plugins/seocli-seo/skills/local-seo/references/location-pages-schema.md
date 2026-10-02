last_verified: 2026-10-02
volatile: true

# Location pages and LocalBusiness markup

Tags as in `business-profile.md`.

## Location pages

- `[G:spam-policies]` (updated 2026-08-28) Doorway abuse: pages or sites created to rank for specific,
  similar queries that lead users to intermediate pages less useful than the destination; examples include
  multiple domain names or pages aimed at specific regions or cities that funnel users to one page.
  Scaled content abuse also covers many pages generated without adding value.
- `[H]` One page per real location (branch, shop). For a service-area business without branches, one
  service page per service, with an area section that names the towns actually served and shows proof of
  work there; additional town pages only if each has distinct evidence.
- `[H]` Swap test: replace the place name. If the text still reads the same, the page is a doorway page.
  Real local content: street or neighbourhood, team members, photos of jobs or the shop, testimonials from
  that area, local regulations or prices, directions and parking.
- `[H]` Thresholds: warn at 30 location pages, stop at 50 without justification; mostly unique copy (above
  60%); publish in small batches and review before scaling.
- `[H]` Structure: `/sedi/<città>/` or `/dove-siamo/<città>/`, one page per location with its own `@id`,
  linked from a location index, with name, address, phone and opening hours in HTML text (not only in an
  image or a map embed), a map link, and the profile link.
- `[H]` Link the profile to the location page of that branch, not to a strongest unrelated page.

## LocalBusiness markup

- `[G:local-business]` (updated 2026-09-08) Required `name` and `address` (PostalAddress with
  `streetAddress`, `addressLocality`, `addressRegion`, `postalCode`, `addressCountry`). `geo` at least 5
  decimal places. `priceRange` shorter than 100 characters or it is not shown. `url` a working link to the
  specific location. Use the most specific subtype. No guarantee of display.
- `[G:gallery]` Local business is a documented feature (knowledge panel details such as hours, ratings,
  directions, booking or ordering).
- `[G:review-snippet]` Do not add `aggregateRating` about the business to its own markup to chase stars.
- `[H]` Match name, address, phone and hours with the visible page and the profile; one block per location;
  one source of markup (theme and plugin duplicates are common).
- `[H]` Italian fields worth adding when true: `legalName`, `vatID` (partita IVA), `taxID`, `areaServed`
  with named towns, `sameAs` to the profile, directory pages and social profiles, `openingHoursSpecification`
  with holiday exceptions, `hasMap`, `telephone` in international format (+39).
- `[H]` Useful subtypes: Plumber, Electrician, Dentist, Physician, LegalService, Restaurant, Store,
  AutoRepair, RealEstateAgent, HealthAndBeautyBusiness; if none fits use LocalBusiness.
- `[H]` Test with the Rich Results Test and Search Console enhancement reports; request page HTML through
  `seocli:check_page_html` (E6.7) to read the markup as served.

## Sources

- spam-policies: https://developers.google.com/search/docs/essentials/spam-policies, accessed 2026-10-02
- local-business: https://developers.google.com/search/docs/appearance/structured-data/local-business, accessed 2026-10-02
- gallery: https://developers.google.com/search/docs/appearance/structured-data/search-gallery, accessed 2026-10-02
- review-snippet: https://developers.google.com/search/docs/appearance/structured-data/review-snippet, accessed 2026-10-02
