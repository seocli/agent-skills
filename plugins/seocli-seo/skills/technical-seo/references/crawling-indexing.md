last_verified: 2026-10-02
volatile: true

# Crawling and indexing: detail and verification notes

Tags: `[G]` re-fetched from the Google page named by its id in Sources (accessed 2026-10-02), `[H]` default
to override per client, `[U]` reported, not confirmed.

## Contradiction settled: Googlebot file size

- `[G:googlebot]` The Googlebot page (updated 2026-02-03) says Googlebot crawls the first 2 MB of a
  supported file type and the first 64 MB of a PDF; limits apply to uncompressed data; each referenced
  resource is bound by the same limit; at the cutoff the fetch stops and only the downloaded part is sent on.
- `[G:crawlers]` The overview of crawlers (updated 2026-06-12) gives 15 MB as the default for Google's
  crawlers and fetchers in general, and says an individual crawler may use a smaller limit, for example 2 MB.
- Resolution: both pages are current and consistent. 15 MB is the generic default, 2 MB is what Googlebot
  uses for Search. Use 2 MB. Older blogs quoting 15 MB for Googlebot are out of date.
- Practical reading `[H]`: very large inline CSS, inline JS or base64 images can push content, links and
  JSON-LD past the cutoff; move them to external files and keep the head and main copy early.

## robots.txt

- `[G:robots-spec]` Size limit 500 KiB; content after it is ignored.
- `[G:robots-spec]` 2xx: parsed. 3xx: at least five hops followed, then treated as a 404. 4xx except 429: as
  if no file existed, no crawl restrictions.
- `[G:robots-spec]` 5xx: for the first 12 hours Google stops crawling the site but keeps trying; for the
  next 30 days it uses the last good version while still retrying; after that the behaviour depends on
  the site's overall availability.
- Contradiction settled: the earlier claim "5xx and 429 mean a temporary full disallow, with a roughly 30 day
  fallback" was imprecise. The documented sequence is 12 hours of no crawling, then up to 30 days of the last
  good copy. The page says 429 is handled differently from other 4xx but does not give the rule in the text
  retrieved: treat a 429 on robots.txt like a temporary server problem `[H]` and check live.
- `[G:robots-spec]` Matching uses the most specific rule by path length; with conflicts, including wildcards,
  the least restrictive rule wins. `*` and `$` are supported.
- `[G:robots-meta]` robots.txt does not remove pages from results; `noindex` needs crawl access.
- `[H]` Keep robots.txt in version control with a test that fetches key URLs; never ship a staging
  `Disallow: /`.
- `[H]` A firewall or bot-protection layer returning 403, 429 or 503 to verified Googlebot looks like
  silent deindexing; allow by verified IP range, not by user agent alone.

## Status codes

- `[G:http-errors]` Up to 10 redirect hops by default; 301 and 308 strong, 302 and 307 weak signal of the
  target; 4xx (not 429) content unused, indexed URLs removed over time; 429 and 5xx slow the crawl, then the
  rate grows again after 2xx; 401 and 403 must not be used to limit crawl rate.
- `[G:js-seo]` Soft 404 in single-page apps: redirect to a URL that returns a 404, or inject `noindex`.
- `[H]` 410 and 404 are treated alike by practice; use 410 when removal is deliberate `[U]`.
- `[H]` 503 with `Retry-After` for planned downtime.

## Crawl budget

- `[G:crawl-budget]` Audience: 1M+ unique pages changing weekly; 10k+ changing daily; many "Discovered -
  currently not indexed". Capacity (server health) and demand (size, freshness, quality) are the two parts.
- `[G:crawl-budget]` `noindex` wastes crawl (URL still requested). Use 404 or 410, accurate `lastmod`, short
  chains, 304.
- `[H]` For a small business site, "Crawled - currently not indexed" is a quality or duplication question,
  not a crawl-budget one.

## Canonical and duplicates

- `[G:canonical]` Strength: redirect, `rel="canonical"`, then sitemap. Absolute URLs. Self-reference.
  `Link` header for non-HTML. Google prefers HTTPS as canonical unless the certificate is invalid.
- `[G:canonical]` Do not: robots.txt, removal tool, conflicting methods, fragments, `noindex` for this.
- `[H]` Typical duplicates: http/https, www/apex, trailing slash, case, tracking parameters, sort
  parameters, print pages, session IDs, faceted URLs. Fix at one normalisation layer (redirects), then
  align canonical, sitemap and internal links to the same URL.
- `[H]` Compare declared and Google-selected canonical through `seocli:inspect_indexing` (E7.4).

## Sitemaps

- `[G:sitemap]` 50 MB or 50,000 URLs per file (uncompressed); XML, RSS/Atom or text; UTF-8; absolute URLs;
  `priority` and `changefreq` ignored; `lastmod` only if consistently verifiable; submit in Search Console,
  its API or a `Sitemap:` line in robots.txt; submission is a hint.
- `[H]` List only canonical, indexable, 200 URLs. Split by template (products, categories, posts) so the
  index report can be filtered per sitemap. A sitemap of 200 URLs that mostly redirect is a defect.
- `[H]` Severity of sitemap findings: over the limit Critical; non-200 or `noindex` URLs High; redirected
  URLs Medium; uniform `lastmod` Low; `priority` or `changefreq` present Info.
- `[U]` Image and news extension limits (1,000 images per URL, news last 2 days) are reported, not
  re-fetched in this pass.

## Pagination and facets

- `[G:pagination]` (updated 2025-12-10) Unique URL per page, each page its own canonical, `<a href>` links,
  no fragments; prev/next link hints unused by Google; infinite scroll needs crawlable links or sitemaps;
  noindex or robots.txt for sort and filter duplicates.
- `[G:facets]` (updated 2025-12-18) Prefer blocking crawl of facet parameters in robots.txt, or use
  fragments; if indexing is needed use `&`, consistent order, 404 for empty combinations, canonical, and
  `nofollow` on filter links as a weaker measure.
- `[H]` Decide which combinations have demand ("scarpe running rosse"): give them clean URLs, unique title,
  h1 and copy and sitemap entries; everything else stays blocked.

## Sources

- googlebot: https://developers.google.com/search/docs/crawling-indexing/googlebot, accessed 2026-10-02 (page updated 2026-02-03)
- crawlers: https://developers.google.com/crawling/docs/crawlers-fetchers/overview-google-crawlers, accessed 2026-10-02 (updated 2026-06-12)
- robots-spec: https://developers.google.com/crawling/docs/robots-txt/robots-txt-spec, accessed 2026-10-02
- robots-meta: https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag, accessed 2026-10-02 (updated 2026-03-24)
- http-errors: https://developers.google.com/search/docs/crawling-indexing/http-network-errors, accessed 2026-10-02
- crawl-budget: https://developers.google.com/crawling/docs/crawl-budget, accessed 2026-10-02 (updated 2026-07-22)
- canonical: https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls, accessed 2026-10-02 (updated 2026-07-10)
- sitemap: https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap, accessed 2026-10-02 (updated 2026-07-08)
- pagination: https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading, accessed 2026-10-02
- facets: https://developers.google.com/search/docs/crawling-indexing/crawling-managing-faceted-navigation, accessed 2026-10-02
- js-seo: https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics, accessed 2026-10-02
