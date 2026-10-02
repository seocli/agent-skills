last_verified: 2026-10-02
volatile: false

# Migrations, platforms, drift signals, reported items

Tags as in `crawling-indexing.md`.

## Migrations

- `[G:site-move]` (updated 2026-08-20) Steps: prepare the new site, map old to new URLs, plan server-side
  301 or 308 redirects, implement and test, monitor in Search Console and analytics. Keep redirects "as long
  as possible, generally at least 1 year". Map 1:1; funnelling many pages to one target invites soft 404.
  Medium sites need weeks or more for new URLs to show. Avoid chains above five hops, ideally under three.
  Mistakes: leftover `noindex` or restrictive robots.txt, wrong targets, sitemaps not updated, too little
  server capacity.
- `[H]` Before: crawl the old site (status, canonical, indexability, titles, h1, inlinks, JSON-LD, hreflang),
  export the top pages and queries from Search Console, keep a baseline of Core Web Vitals and rankings.
  Staging behind authentication, not only `noindex`.
- `[H]` Launch: list-driven redirect test (status 301 or 308, one hop, final 200, final canonical equals
  itself); update canonicals, hreflang, sitemaps, internal links, structured data URLs, feeds, analytics.
- `[H]` After: watch index status, 404 and 5xx in logs, old URLs still crawled (expected until recrawl);
  compare with the baseline weekly for 8-12 weeks; keep the old domain registered.
- `[H]` Change one thing at a time where possible; do not combine a redesign, a replatform and a domain
  change if the business can avoid it.

## Platform notes `[H]`

Written as defaults, not re-fetched. Verify against the platform's current documentation.
- Headless CMS: model `seoTitle`, `metaDescription`, canonical override, `noindex`, `ogImage`, slug and
  locale group as required fields; fallback chain title then description from excerpt; stable slugs or
  automatic redirect on change; draft environments behind authentication and absent from sitemaps; webhooks
  for revalidation.
- WordPress: one SEO plugin only; duplicate canonical, JSON-LD or sitemap from theme plus plugin is the top
  defect; "discourage search engines" must be off in production; noindex thin tag, date and author archives.
- Shopify: products are reachable under collection paths and under `/products/`; internal links should use
  the canonical form; redirects live in the admin and can be imported; app scripts are the main cause of
  slow INP and LCP.
- Other shops: one canonical per product (or per variant with real demand); keep a returning out-of-stock
  product at 200 with the correct availability, 410 or a redirect to a successor when discontinued; cart,
  checkout and account pages blocked and `noindex`; feed prices equal page prices.

## Drift signals `[H]`

Plugin convention for comparing a baseline with a new capture (`seocli:create_baseline` (E7.7),
`seocli:manage_monitors` (E4.1)). Signal codes belong to the server; severities below are defaults.
- Critical: structured data removed entirely, canonical changed or removed, `noindex` added, h1 removed,
  title removed, status moved from 2xx to 4xx or 5xx.
- Warning: title or description changed, a Core Web Vitals metric more than 20% worse, social tags removed,
  structured data content changed.
- Info: structured data added, h2 structure changed, content hash changed.
- Normalise before comparing: lowercase host, sorted query parameters, tracking parameters dropped, nonces
  and dynamic fragments stripped; a raw content hash is noisy.
- Retired structured-data types should not count as a lost rich result.
- Recommend next steps by dependency: restore indexability first, then canonical, then on-page, then schema.
- Use `seocli:check_page_html` (E6.7) for a before and after check around a deployment.

## Reported, not confirmed `[U]`

- IndexNow is a protocol used by some other search engines; it does not notify Google.
- A spam policy against hijacking the browser back button is reported as enforced from 2026-06-15.
- Cache and viewer maintenance for AMP pages reported unnecessary from 2026-07-01.
- The crawl-rate setting in Search Console reported removed in January 2024.
- A canonical change can take up to two weeks to be reflected (also kept as a default in the skill).
- Search Console API quotas (URL Inspection, Search Analytics rows) change; read the current quota page.

## Sources

- site-move: https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes, accessed 2026-10-02
- Items marked `[H]` and `[U]` are not tied to a fetched page; `[H]` platform and drift defaults are plugin conventions.
