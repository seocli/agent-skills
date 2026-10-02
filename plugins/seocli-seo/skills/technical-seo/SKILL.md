---
name: technical-seo
description: Technical SEO knowledge - crawling, robots.txt, status codes, canonicals, JavaScript rendering, mobile-first indexing, Core Web Vitals, structured data and its deprecations, hreflang, sitemaps, pagination, facets, migrations and drift signals, each tied to a Google primary source. Knowledge for developers and the lead; not a command.
user-invocable: false
---

# technical-seo

## When to use

Load it when a finding or fix concerns how a site is fetched, rendered, indexed or marked up, or when
interpreting audit output and drift signals. Use `methodology` for finding and recommendation form,
`seocli-tools` for what the server can fetch and what it costs, `search-analytics` for Search Console
and traffic questions, `content-quality` for content and spam policy, and `local-seo` for Business
Profile and location pages. Tags: `[G]` Google primary source (id = `references/*.md` Sources, accessed
2026-10-02); `[H]` default in Heuristics.

## Rules

1. **Googlebot reads the first 2 MB** of a supported file (uncompressed), 64 MB for PDFs; each CSS or JS
   file has its own cap, and the fetch stops at the cutoff. The general crawler page says 15 MB by default
   and that a crawler may set a smaller cap such as 2 MB: for Googlebot in Search the 2 MB figure applies.
   Keep the title, canonical, main copy, links and JSON-LD early in the HTML. [G:googlebot][G:crawlers]
2. **robots.txt** is parsed up to 500 KiB. Most specific (longest) path wins; on a conflict the least
   restrictive rule wins; `*` and `$` work. A 4xx other than 429 means "no robots.txt, no restrictions". A
   5xx stops crawling for 12 hours, then the last good copy is used for up to 30 days. More than five
   redirect hops count as a 404. [G:robots-spec]
3. **robots.txt controls crawling, not indexing.** A disallowed URL cannot show its `noindex` or canonical,
   so those are ignored; to keep a page out of results serve `noindex` on a crawlable URL. When robots
   rules conflict the more restrictive one applies (`nosnippet` beats `max-snippet`). [G:robots-meta]
4. **Status codes.** Googlebot follows up to 10 redirect hops; 301 and 308 are strong signals, 302 and 307
   weak. 4xx content is not used and indexed URLs drop out over time; 429 and 5xx slow the crawl. Never use
   401 or 403 to throttle crawling. [G:http-errors]
5. **Crawl budget** matters only for sites with 1M+ pages changing weekly, 10k+ pages changing daily, or
   many "Discovered - currently not indexed" URLs. `noindex` does not save it (the URL is still fetched).
   Levers: 404 or 410 for removed pages, no soft 404, accurate `lastmod`, short redirect chains, 304
   support. [G:crawl-budget]
6. **Canonical.** Signals by strength: redirect (strong), `rel="canonical"` (strong), sitemap (weak).
   Use absolute URLs, a self-referencing canonical, a `Link` header for non-HTML files. Do not use
   robots.txt, the removal tool, `noindex` or fragments to choose a canonical, and do not send conflicting
   canonicals. Google may pick a different canonical than declared. [G:canonical]
7. **JavaScript.** Links must be `<a href>`; use the History API, not `#` routes. A non-200 page may skip
   rendering. A `noindex` in the raw HTML may stop JavaScript from running, so JS cannot reliably remove it.
   A canonical set by JS must equal the raw one; one canonical only. JSON-LD injected by JS is read. In a
   single-page app a missing page needs a server 404, a redirect to one, or an injected `noindex`. [G:js-seo]
8. **Mobile-first.** The mobile page is the indexed page: same content, title, description, structured data
   and robots meta on both. Content that needs a swipe, click or typing to load is not loaded. Responsive
   design is the recommended setup. [G:mobile-first]
9. **Core Web Vitals** at the 75th percentile of page loads, mobile and desktop apart, all three must pass:
   LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1. INP replaced the older first-input metric in 2024. Good scores do not guarantee
   rankings; relevance still wins and page experience separates similar pages. [G:vitals][G:page-exp]
10. **LCP**: make the resource discoverable in the initial HTML, `fetchpriority="high"`, never lazy-load
    it. **INP**: start from field data, break long tasks, shrink the DOM. **CLS**: give media dimensions,
    reserve space for late content. Detail in `references/rendering-performance.md`. [G:lcp][G:inp][G:cls]
11. **Structured data.** JSON-LD is preferred; the page must be crawlable and indexable; markup must match
    visible content with required properties; display is never guaranteed; a violation can trigger a
    manual action on rich results only. A site reviewing itself gets no star results. [G:sd-policies][G:review-snippet]
12. **Retired features.** The FAQ search feature is gone (May 2026; docs removed 15 June 2026) and the
    search gallery lists neither FAQ nor HowTo. Check `references/structured-data-hreflang.md` before
    promising any visual feature. [G:faq][G:gallery]
13. **hreflang.** Each version lists itself and all others; return links are mandatory (one-way tags are
    ignored); language is ISO 639-1, region optional ISO 3166-1 alpha-2, region alone is invalid;
    `x-default` for unmatched users; absolute URLs; HTML, header and sitemap methods are equivalent, pick
    one. [G:hreflang]
14. **Sitemaps.** 50,000 URLs or 50 MB uncompressed per file, UTF-8, absolute URLs, index file for more;
    `priority` and `changefreq` are ignored; `lastmod` is used only if consistently accurate. Submitting is a
    hint. [G:sitemap]
15. **Pagination and facets.** Unique URL per page, own canonical per page (not page 1), `<a href>` links
    between pages, no fragments; the prev/next link hints are unused. Block filter and sort duplicates with
    robots.txt or `noindex`; indexable facets need `&`, fixed order, 404 when empty. [G:pagination][G:facets]
16. **Migrations.** Map old to new 1:1, use server-side 301 or 308, keep redirects at least one year, update
    sitemaps, expect weeks before new URLs show; leftover `noindex` or robots blocks and redirect chains
    above five hops are the classic errors. [G:site-move]
17. **Verify Googlebot** by reverse DNS or by matching Google's published IP ranges: the user agent is often
    spoofed. [G:googlebot]

## Heuristics

- Fix order `[H]`: indexability blockers (robots, `noindex`, 5xx, WAF), then content availability in raw
  HTML, duplicate-URL control, discovery (sitemaps, links), Core Web Vitals (LCP, INP, CLS), structured
  data the business can win, hreflang. Order by dependency, not by count.
- One redirect hop target, audit tolerance 3 hops; important pages within 3 clicks of home `[H]`.
- Flag a rendered/raw word-count ratio above 1.3 as "content depends on JavaScript" `[H]`.
- After a canonical fix wait about two weeks before calling it failed `[H]`.
- At most two facets indexable at once, only combinations with search demand `[H]`.
- Data requests (lead executes): `seocli:crawl_site` (E6.1), `seocli:get_audit_issues` (E6.3),
  `seocli:check_page_html` (E6.7), `seocli:check_performance` (E6.5), `seocli:recheck_urls` (E6.6),
  `seocli:inspect_indexing` (E7.4). Map each audit issue to category, severity, fix and the check that
  proves it; never promise a ranking effect, name the leading indicator instead.

## Do not recommend

- Dynamic rendering for a new build; SSR, SSG or hydration instead.
- `noindex` or robots.txt as a crawl-budget tool for small sites; crawl budget as the cause on SME sites.
- Hash routes, `onclick` navigation, IP-based language redirects, 200 for "not found", mass redirects to the home page.
- HowTo rich results or FAQ rich results as a goal; FID as a metric; the prev/next link hints as a signal.
- The Indexing API for ordinary pages; `meta keywords`; a keyword density target.

## Italian market notes

- `it-IT` for Italy, `it-CH` for Ticino; `de-IT` (South Tyrol) and `fr-IT` (Aosta Valley) exist for the
  bilingual provinces. A site aimed only at Italy needs no hreflang.
- Never redirect by IP or browser language: serve the locale by URL (Googlebot mostly crawls from the US) `[H]`.
- Most client sites run WordPress or Shopify; duplicate canonical and JSON-LD output from theme plus plugins
  is the first thing to check `[H]`.
- Staging copies left with `noindex` or `Disallow: /` after launch are the most frequent migration error `[H]`.

## References

- `references/crawling-indexing.md`: robots.txt, status codes, crawl budget, canonical, sitemaps, pagination, facets, with sources; read on demand.
- `references/rendering-performance.md`: JavaScript rendering, mobile-first, Core Web Vitals engineering; read on demand.
- `references/structured-data-hreflang.md`: structured data rules, deprecation ledger, hreflang; read on demand.
- `references/migrations-drift.md`: migration checklist, platform notes, drift signal rules, reported items; read on demand.
