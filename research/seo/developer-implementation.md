# Developer's technical SEO + GEO implementation playbook (2026)

Research date: 2026-10-02. Audience: the seocli Claude Code plugin (skills + MCP server). Language: English.

## 0. How to read this file

Source confidence tags used on every rule:

- [V] = verified by fetching the official page on 2026-10-02 (URL given).
- [R] = recalled from prior knowledge of official docs/specs; fetch was not done in this pass. Re-verify before shipping it as a hard rule in a skill.
- [N] = news/secondary source (blog, trade press); treat as "reported", not canonical.

Each section ends with: Verify (how to prove the fix), MCP data (what the seocli MCP server must expose).

Core stance (supported by Google's own AI guide [V], https://developers.google.com/search/docs/fundamentals/ai-optimization-guide, accessed 2026-10-02): there is no separate "AI SEO" layer for Google. Pages must be crawlable, indexed, snippet-eligible; follow JavaScript SEO; keep good page experience; reduce duplicates. The GEO work below for non-Google assistants (ChatGPT, Claude, Perplexity) is mostly "make the same content fetchable, parseable and attributable by bots that do not execute JavaScript".

---

## 1. Rendering strategies and JavaScript SEO

### 1.1 Facts about Googlebot

- Pipeline is crawl -> render -> index. Pages that return HTTP 200 are queued for rendering in headless Chromium; JS is executed before indexing. [V] https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics (2026-10-02)
- SSR remains recommended for speed and for bots that do not run JS. [V same URL]
- Canonical: put it in the raw HTML; if injected by JS, it must equal the HTML value; never emit multiple conflicting canonicals. [V]
- Status codes: return real 404/401/410 from the server. In an SPA that cannot, either redirect to a URL that returns a server 404, or inject `<meta name="robots" content="noindex">` with JS (noindex avoids waiting on rendering). [V]
- Routing: History API paths, never `#fragment` routes (Googlebot does not parse fragments as separate URLs). [V]
- Links must be `<a href="...">`; `onclick` handlers, `<div role=link>`, `href="#"` + JS routers are not crawlable. [R]
- Lazy loading must follow Google's guidance so media is discoverable (IntersectionObserver or native `loading="lazy"`, not scroll-event-only). [V partial]
- Shadow DOM is flattened by Google; confirm in Rich Results Test / URL Inspection rendered HTML. [V]
- Fingerprint JS/CSS filenames (`main.2bb85551.js`) so Google's cache does not serve stale assets. [V]
- Dynamic rendering (serving a pre-rendered snapshot to bots by UA) is documented by Google as a workaround, not a recommendation; prefer SSR/SSG/hydration. Treat as legacy debt. [R] (Google deprecated the page in its docs; verify current wording.)
- Render-blocking resource fetch: Googlebot respects robots.txt for JS/CSS/API calls. Blocking `/api/` or `/_next/static` in robots.txt can break rendered content. [R]
- Googlebot fetches up to 15 MB per file by default (HTML uncompressed); larger HTML is truncated. [V] https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers. Note: some docs state a 2 MB limit for Googlebot HTML in search; confirm in the page before citing. [R]
- Rendering does not click, scroll to trigger fetches, or accept cookies/permissions; content behind interaction is not seen. [R]

### 1.2 Strategy table

| Strategy | SEO verdict | Use for | Pitfall |
|---|---|---|---|
| SSG (build-time HTML) | Best: complete HTML, CDN-cached, TTFB minimal | docs, blogs, marketing, catalog pages that change rarely | rebuild time at scale; stale prices |
| ISR / stale-while-revalidate | Excellent | large catalogs, news archives | stale window; cache-poisoning with per-user data; on-demand revalidation must be wired to CMS webhooks |
| SSR (per request) | Good if TTFB is controlled | personalised/high-churn pages | slow TTFB wrecks LCP; streaming SSR must not drop SEO tags into body late |
| CSR only (SPA) | Risky | authenticated apps | empty HTML to non-JS bots (all AI crawlers, [R] most do not execute JS); soft 404; render queue delay |
| Hybrid hydration / islands (Astro, Qwik, React Server Components) | Excellent | content + sparse interactivity | hydration mismatches; JS cost -> INP |
| Edge SSR | Good | geo/variant pages | `Vary`, cache key discipline |

Rule of thumb: everything that should rank or be cited by an LLM must be present in the first HTML response (title, meta, canonical, h1, main copy, internal links, JSON-LD). Test by `curl` with no JS. [R]

### 1.3 Framework patterns (short, own-written)

Next.js (App Router) [R: https://nextjs.org/docs/app/api-reference/functions/generate-metadata, not fetched]:

```ts
// app/products/[slug]/page.tsx  (server component = HTML in first response)
export async function generateMetadata({ params }: { params: { slug: string } }) {
  const p = await getProduct(params.slug);
  if (!p) return { robots: { index: false } };
  return {
    title: `${p.name} | Brand`,
    description: p.summary,
    alternates: { canonical: `https://example.com/products/${p.slug}` },
    openGraph: { images: [p.image] },
  };
}
export const revalidate = 3600; // ISR
export default async function Page({ params }) {
  const p = await getProduct(params.slug);
  if (!p) notFound();            // real 404 status
  return <ProductView p={p} />;
}
```

Also: `app/sitemap.ts`, `app/robots.ts`, `redirects()` in `next.config` (permanent: true = 308), `permanentRedirect()` in server code. Avoid client-only `useEffect` data fetching for primary content.

Nuxt 3 [R]: `useSeoMeta({ title, description, ogImage })`, `useHead({ link:[{rel:'canonical',href}] })`, `routeRules: { '/blog/**': { isr: 3600 }, '/old': { redirect: { to:'/new', statusCode:301 } } }`, module `@nuxtjs/seo` bundles sitemap/robots/schema.org/OG image.

Astro [R]: static by default, zero JS unless `client:*` directive; `@astrojs/sitemap` integration; set `site` in config; `Astro.redirect(url, 301)`; content collections for typed frontmatter (title/description required via zod schema = lint for SEO).

SvelteKit [R]: `+page.server.ts` `load` returns data; `export const prerender = true`; throw `error(404)` / `redirect(301, ...)`; `<svelte:head>` for title/meta; trailing slash policy via `trailingSlash` option.

React SPA (Vite/CRA): migrate to a framework with SSR/SSG, or at minimum prerender (e.g. vite-ssg, react-snap style). Interim: `react-helmet-async` only helps Googlebot, not non-JS bots.

### 1.4 Pitfalls checklist

1. Title/meta/canonical/robots set only client-side.
2. Different content for Googlebot vs users (cloaking) when "optimising" with UA sniffing. Dynamic rendering that is byte-for-byte equivalent is tolerated but unsupported. [R]
3. Hydration errors that blank the page on JS failure.
4. Soft 404 (200 + "not found" copy).
5. `noindex` in initial HTML then removed by JS: Google may honour the initial noindex and skip render. [R]
6. Infinite scroll with no paginated URL fallback (see 4.4).
7. Cookie/consent wall or interstitial covering content in first response.
8. Locale detection by IP/Accept-Language with redirects: Googlebot crawls mostly from US IPs; serve locale via URL. [R]
9. Service-worker cached shells that return 200 for any path.
10. Content in `<template>`, in `display:none` tabs OK for Google if in DOM, but invisible to many LLM fetchers if loaded on click.

### Verify
- `curl -sA "Mozilla/5.0 (compatible; Googlebot/2.1)" URL | grep -Ei '<title|canonical|<h1|application/ld\+json'` (raw HTML check).
- Compare raw vs rendered DOM: headless Chromium (`playwright`, `page.content()`) diff on: title, meta description, canonical, h1, link count, JSON-LD count, word count. Flag if rendered/raw word-count ratio > 1.3 (heuristic, mine).
- Search Console URL Inspection -> "View crawled page" / Rich Results Test for rendered HTML. [R]
- Playwright test: `expect(response.status()).toBe(404)` for bogus slug.

### MCP data
`fetch_raw_html(url, ua)`, `fetch_rendered_html(url, wait)`, response headers + status + redirect chain, diff summary raw vs rendered (counts of links/words/ld+json/canonicals), JS-error console log, blocked-resource list (robots.txt disallowed sub-requests).

---

## 2. Core Web Vitals engineering

### 2.1 Thresholds (75th percentile of page loads, mobile and desktop segmented) [V]
Source: https://web.dev/articles/vitals (2026-10-02)

| Metric | Good | Needs improvement | Poor |
|---|---|---|---|
| LCP | <= 2.5 s | 2.5-4.0 s | > 4.0 s |
| INP | <= 200 ms | 200-500 ms | > 500 ms |
| CLS | <= 0.1 | 0.1-0.25 | > 0.25 |

(Needs-improvement/poor upper bounds 4 s, 500 ms, 0.25 are [R] from web.dev; the "good" values are [V].) INP replaced FID (stable CWV since 2024) [V]. A URL/origin passes if all three meet "good" at p75. Stable metrics change at most once a year [V].

Page experience is a signal among many; Google says great content matters more, but CWV breaks ties and drives UX/conversion. [R]

### 2.2 Field vs lab
- Field (RUM/CrUX): real users, 28-day rolling window, p75, what Google uses. CrUX data comes only for URLs/origins with enough traffic; low-traffic URLs fall back to origin-level. [R]
- Lab (Lighthouse, WebPageTest, DevTools): reproducible, debug-friendly, no INP (Lighthouse uses TBT as proxy; use Timespan/User flows for INP). [R]
- Rules: decide by field; debug with lab; confirm with field after >= 28 days (CrUX) or via own RUM (`web-vitals` library, attribution build) within days.
- CrUX History API gives weekly p75 series for trend; CrUX API `records:queryRecord` by URL or origin, by `formFactor`. PageSpeed Insights API v5 returns both `loadingExperience` (field) and `lighthouseResult` (lab). [R]

RUM snippet (own):

```js
import {onLCP, onINP, onCLS} from 'web-vitals/attribution';
const send = m => navigator.sendBeacon('/rum', JSON.stringify({
  name: m.name, value: m.value, id: m.id, nav: m.navigationType,
  attr: m.attribution // LCP element/subparts, INP target + phases, CLS shift sources
}));
onLCP(send); onINP(send); onCLS(send);
```

### 2.3 LCP engineering [V https://web.dev/articles/optimize-lcp]
Four sub-parts, ideal split: TTFB ~40%, resource load delay < 10%, resource load duration ~40%, element render delay < 10%. Any time in the two "delay" parts is pure waste.

- Discoverability: LCP image must be in initial HTML as `<img src>` (not CSS background, not JS-inserted, not lazy). Preload CSS-referenced hero.
- Priority: `fetchpriority="high"` on the LCP `<img>`; never `loading="lazy"` on it.
- Render delay: shrink/inline critical CSS, defer non-critical JS, avoid client-only rendering of hero text, avoid web-font blocking for text LCP.
- Load duration: right-size, AVIF/WebP, `srcset`+`sizes`, CDN, HTTP caching.
- TTFB: CDN/edge caching of HTML, ISR/SSG, DB query tuning, avoid redirects chains before document, `103 Early Hints` for preload (CDN-dependent) [R], HTTP/2 or 3.

```html
<link rel="preload" as="image" href="/hero.avif" imagesrcset="/hero-800.avif 800w, /hero-1600.avif 1600w"
      imagesizes="100vw" fetchpriority="high">
<img src="/hero-1600.avif" srcset="/hero-800.avif 800w, /hero-1600.avif 1600w" sizes="100vw"
     width="1600" height="900" fetchpriority="high" decoding="async" alt="...">
```

### 2.4 INP engineering [V https://web.dev/articles/optimize-inp]
INP = worst (approx. p98) interaction latency over the page lifetime: input delay + processing duration + presentation delay.
- Input delay: reduce main-thread long tasks at load (script eval/parse); code-split; defer third parties.
- Processing: keep handlers tiny; do only what updates next frame; yield (`scheduler.yield()` where available, fallback `setTimeout`) between chunks; move heavy work to Web Workers.
- Presentation delay: small DOM (Lighthouse warns > ~800 nodes, errors > ~1400 [R]), `content-visibility: auto`, avoid layout thrash, avoid huge client-side HTML injection.
- Frameworks: avoid hydrating whole page (islands/partial hydration, RSC), memoise expensive renders, virtualise long lists, debounce input handlers.

```js
async function onClick() {
  updateUiNow();                       // paint-critical
  await (self.scheduler?.yield?.() ?? new Promise(r => setTimeout(r)));
  doHeavyWorkInChunks();               // not blocking next paint
}
```

### 2.5 CLS
- Always set `width`/`height` (or `aspect-ratio`) on images, video, iframes, ad slots; reserve space for embeds, banners, consent bars.
- Fonts: `font-display: swap|optional` plus metric-matched fallback (`size-adjust`, `ascent-override`) or `next/font`-style self-hosted preload.
- Do not insert content above existing content after load; animate with `transform`/`opacity`, not `top/height`.
- Back/forward cache eligibility helps repeat navigations (avoid `unload` handlers, `Cache-Control: no-store` on documents). [R]

### 2.6 Assets, caching, CDN
- Images: modern formats, responsive, lazy below the fold only, `decoding="async"`.
- Fonts: preload at most 1-2 critical WOFF2 with `crossorigin`; subset; self-host.
- Scripts: `defer` by default; `async` for independent; no render-blocking `<script>` in head; remove unused JS (coverage); third-party via facade/`requestIdleCallback`/Partytown-like worker approaches.
- Caching: static assets `Cache-Control: public, max-age=31536000, immutable` with hashed names; HTML `s-maxage` + `stale-while-revalidate` at CDN; support `ETag`/`Last-Modified` and 304 (also helps crawl budget [V crawl-budget doc]).
- Compression: Brotli for text; HTTP/2+; TLS 1.3.
- `Vary: Accept-Encoding` only (avoid `Vary: Cookie` on cacheable HTML).
- Speculation Rules API for prerender of likely next page (Chromium) [R]: `<script type="speculationrules">{"prerender":[{"where":{"href_matches":"/products/*"},"eagerness":"moderate"}]}</script>`; ensure analytics fires on `prerenderingchange`.

### Verify
- CLI: `npx lighthouse URL --only-categories=performance --output=json --chrome-flags="--headless"`; thresholds in `lighthouserc.json` via Lighthouse CI (`assertions: {"largest-contentful-paint":["error",{"maxNumericValue":2500}], "cumulative-layout-shift":["error",{"maxNumericValue":0.1}], "total-blocking-time":["warn",{"maxNumericValue":200}]}`). [R]
- `curl "https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=URL&strategy=mobile&category=performance&key=KEY"` -> `loadingExperience.metrics.*.percentile`.
- CrUX API: POST `https://chromeuxreport.googleapis.com/v1/records:queryRecord?key=KEY` body `{"url":"...","formFactor":"PHONE"}`.
- Gate in CI on lab (regression guard), gate in release on field (RUM p75 trend).

### MCP data
CrUX p75 + histogram per metric/formFactor/URL-or-origin and history series; PSI lab result + LCP element, LCP sub-part timings, long-task list, CLS shift sources, INP attribution (from RUM beacons if site has them); resource waterfall with priorities; cache headers per asset; image dimensions vs rendered size.

---

## 3. Head tags, canonical, robots, hreflang

### 3.1 Minimum head contract (every indexable URL)
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Primary topic - Brand</title>
  <meta name="description" content="One sentence value prop, ~120-160 chars.">
  <link rel="canonical" href="https://example.com/page">
  <meta name="robots" content="max-snippet:-1, max-image-preview:large">
  ...
```
Rules:
- One `<title>`, one description, one canonical per URL; in the first HTML response. [V for canonical via JS doc]
- Title/description length are display heuristics, not limits: titles truncated by pixel width (~600px, ~50-60 chars), Google may rewrite titles/snippets. [R]
- `<html lang>` accurate; helps accessibility and language handling.
- Open Graph/Twitter cards for sharing (not ranking): `og:title`, `og:description`, `og:image` (1200x630), `og:url`, `og:type`.
- Google ignores `meta keywords`.

### 3.2 Canonical
- Self-referencing absolute canonical on every indexable page; one canonical per URL; point to a 200, indexable, same-language URL. [R/V partial]
- Canonical is a hint, not a directive; Google chooses from redirects, sitemap inclusion, internal links, hreflang and canonical. Keep all signals aligned (sitemap lists only canonicals [V sitemaps doc]).
- Normalise: protocol https, one host (www vs apex), trailing-slash policy, lowercase, strip tracking params (`utm_*`, `gclid`), sort/session params canonical to the unsorted version.
- Never canonical to a noindex or redirecting URL; never canonical all paginated pages to page 1 [V pagination doc].
- Cross-domain canonical for syndication allowed.
- Alternatives: `Link: <https://...>; rel="canonical"` HTTP header for PDFs.

### 3.3 Robots: three different tools
| Tool | Controls | Effect | Notes |
|---|---|---|---|
| robots.txt | crawling | URL not fetched; may still be indexed w/o content if linked | Does not remove from index; blocks noindex discovery [V] |
| `<meta name="robots">` / `X-Robots-Tag` | indexing / serving | noindex, nofollow, nosnippet, max-snippet, max-image-preview, max-video-preview, unavailable_after, indexifembedded [V partial robots-meta-tag] | Most restrictive wins on conflicts [V]; ignored if robots.txt blocks the URL [V] |
| `data-nosnippet` attr | text-level snippet exclusion | span/div/section; boolean attribute [V] | |

https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag (2026-10-02).

Patterns:
- Remove from index: serve `noindex` (meta or header) on a crawlable URL; or 404/410; not robots.txt disallow.
- Crawl-budget trimming of junk URL spaces: robots.txt Disallow patterns (not noindex) [V crawl-budget].
- Faceted/filter duplicates: Google's pagination doc offers noindex or robots.txt restrictions [V].
- PDFs/images: `X-Robots-Tag: noindex` via server config.
- Snippet control also governs AI Overviews usage: `nosnippet`, `max-snippet`, `data-nosnippet` restrict what Google can show/use; Google-Extended token does NOT affect Search or AI Overviews [R].

robots.txt syntax (own):
```
User-agent: *
Disallow: /cart/
Disallow: /search?
Disallow: /*?sort=
Allow: /search?q=brand      # more specific rule wins
Sitemap: https://example.com/sitemap_index.xml
```
Rules: UTF-8, at host root, per-host/per-protocol/port; longest-match precedence; `*` and `$` wildcards; 4xx = treated as no robots file (allow all), 5xx = Google treats as full disallow temporarily (then may use last cached copy) [R]; max 500 KiB parsed [R].

### 3.4 hreflang [V] https://developers.google.com/search/docs/specialty/international/localized-versions (2026-10-02)
- Reciprocal (return) links mandatory; each version lists itself and all siblings.
- `x-default` for unmatched users (language selector/global page).
- Codes: ISO 639-1 language, optional ISO 3166-1 alpha-2 region (`en-GB`, `fr`, `zh-Hans`); region alone invalid; `UK`, `EU` invalid.
- Fully-qualified URLs. Three equivalent methods: HTML `<link>`, HTTP `Link` header (non-HTML), XML sitemap `xhtml:link`.
- Pick one method per site section to avoid drift; for large sites sitemap is easiest to generate and test.

```html
<link rel="alternate" hreflang="en" href="https://example.com/en/page">
<link rel="alternate" hreflang="it" href="https://example.com/it/pagina">
<link rel="alternate" hreflang="x-default" href="https://example.com/">
```
```xml
<url><loc>https://example.com/en/page</loc>
  <xhtml:link rel="alternate" hreflang="en" href="https://example.com/en/page"/>
  <xhtml:link rel="alternate" hreflang="it" href="https://example.com/it/pagina"/>
</url>
```
Pitfalls: canonical pointing to a different language version (kills hreflang); noindexed alternates; redirecting alternates; auto-translated thin pages; mixing relative URLs.

### Verify
- Crawler assertion set: exactly 1 canonical, canonical 200+indexable, self-canonical rate, canonical chains = 0, hreflang reciprocity graph has no one-way edges, all hreflang targets 200 and canonical to themselves, valid code regex `^[a-z]{2,3}(-[A-Za-z]{4})?(-[A-Z]{2})?$|^x-default$`.
- Search Console Page indexing: "Alternate page with proper canonical tag", "Duplicate, Google chose different canonical than user".
- Unit test: render page -> parse head -> snapshot with required tags.

### MCP data
Per URL: raw+rendered head tags, canonical (HTML/header/JS), robots meta/X-Robots-Tag, robots.txt allow/deny verdict for Googlebot and each AI bot, hreflang set & reciprocity graph, GSC "user-declared vs Google-selected canonical" (URL Inspection API), indexing state.

---

## 4. Sitemaps, pagination, faceted navigation, internal linking

### 4.1 Sitemaps [V] https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap (2026-10-02)
- Limits per file: 50,000 URLs or 50 MB uncompressed; use a sitemap index for more. UTF-8; absolute URLs; canonical, indexable (200) URLs only.
- Google ignores `<priority>` and `<changefreq>`; uses `<lastmod>` only if consistently accurate (real content changes, not footer-year tweaks).
- Submit in Search Console / API or `Sitemap:` line in robots.txt.
- Formats: XML (extensions image, video, news, hreflang), RSS/Atom, text.
- Extensions: `image:image` (loc; legacy extra tags deprecated [R]), `video:video` (thumbnail_loc, title, description, content_loc or player_loc, duration, publication_date), `news:news` (publication name+language, publication_date, title; URLs from last 2 days only; max 1000 per file [R]).
- Segment sitemaps by type (products, categories, posts) so GSC "Page indexing" can be filtered per sitemap -> best diagnostic tool.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <sitemap><loc>https://example.com/sitemaps/products-1.xml</loc><lastmod>2026-09-30</lastmod></sitemap>
</sitemapindex>
```
Generate from the same source of truth as the canonical logic; assert in CI that every sitemap URL returns 200, is self-canonical, not noindex, not robots-blocked.

### 4.2 Pagination [V] https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading
- `rel=prev/next` no longer used by Google (harmless).
- Each page unique URL (`?page=n` or `/page/n`), self-canonical, linked with `<a href>`; no fragments; not canonical to page 1.
- "Load more"/infinite scroll: back them with paginated URLs and real links so Googlebot (which does not scroll/click) can reach items; use History API to update URL.
- Page 2+ title tweak optional ("Page 2"); keep h1 stable.

### 4.3 Faceted navigation / filters [V same doc; R for specifics]
- Decide which facet combos have search demand (e.g. "red running shoes"): make those clean indexable URLs with unique title/h1/copy and sitemap inclusion.
- All other combinations: robots.txt disallow of param patterns (saves crawl, but blocks noindex reading) or `noindex` (still crawled). Do not do both on the same URL.
- Order parameters canonically; cap combinations (max 2 facets indexable); `rel="nofollow"` on facet links is a weak hint; prefer not rendering links for non-indexable combos or load via POST/JS.
- Return 404 for facet combos with zero results (not 200 empty).

### 4.4 Internal linking architecture [R unless noted]
- Every indexable page reachable in <= 3-4 clicks from home; orphan pages (in sitemap, no inbound links) flagged.
- Links are `<a href>` with descriptive anchor text; avoid "click here"; image links need alt.
- Hub-and-spoke: pillar page links to cluster pages and each cluster links back and laterally.
- Breadcrumbs (HTML + BreadcrumbList JSON-LD) mirror hierarchy.
- Keep main nav/footers stable; avoid >~150-300 links/page by dilution heuristic (not a Google rule).
- Related-content modules rendered server-side, not fetched on scroll.
- Avoid linking to redirecting or non-canonical URLs; update internal links after migrations (do not rely on 301s).
- Link equity metrics: inlinks count, depth, internal PageRank computed from crawl graph.

### Verify
Crawl + graph analysis: depth histogram, orphans, broken links, links to redirects/non-canonicals, facet URL explosion (count of parameter variants per path), sitemap vs crawl diff (URLs in one not the other).

### MCP data
Full crawl graph (edges with anchor, rel, source/target status), depth, inlinks, sitemap URL set and lastmod, URL parameter inventory, GSC coverage by sitemap, pagination chains.

---

## 5. Structured data

### 5.1 Rules [V] https://developers.google.com/search/docs/appearance/structured-data/sd-policies (2026-10-02)
- JSON-LD preferred (Microdata/RDFa supported).
- Mark up only content visible to users; no misleading/fake reviews; include all required properties of the targeted rich result.
- Page must not be blocked (robots.txt/noindex/auth).
- No guarantee of rich result display.
- For AI features structured data is not required [V ai-optimization-guide] but remains useful for entity clarity and rich results.
- Feature set changes: Google has retired/limited several rich results (e.g. FAQ and HowTo reduced/removed in 2023; more simplifications since). Always check the current search gallery before promising a visual feature. [R] https://developers.google.com/search/docs/appearance/structured-data/search-gallery

### 5.2 Implementation pattern
- Generate JSON-LD server-side from the same data object that renders the visible page (single source of truth) -> no drift.
- Use `@graph` with `@id` URIs to connect entities (Organization, WebSite, WebPage, Article, Person).
- Escape `</script>` and `<` in JSON (`JSON.stringify(obj).replace(/</g,'\\u003c')`).
- One block per page or one `@graph`; avoid duplicate conflicting blocks from theme + plugin (common in WordPress).

```tsx
function JsonLd({data}:{data:object}) {
  return <script type="application/ld+json"
    dangerouslySetInnerHTML={{__html: JSON.stringify(data).replace(/</g,'\\u003c')}} />;
}
```

### 5.3 Per-type minimal patterns (own-written; check required/recommended props in gallery)

Organization + WebSite (home):
```json
{"@context":"https://schema.org","@graph":[
 {"@type":"Organization","@id":"https://example.com/#org","name":"Brand","url":"https://example.com/",
  "logo":"https://example.com/logo.png","sameAs":["https://www.linkedin.com/company/brand"]},
 {"@type":"WebSite","@id":"https://example.com/#site","url":"https://example.com/","name":"Brand",
  "publisher":{"@id":"https://example.com/#org"}}]}
```
Article / BlogPosting: `headline`, `image` (multiple ratios), `datePublished`, `dateModified` (ISO 8601 with tz), `author` as Person/Organization with `url`, `publisher`.
Product (+Offer, AggregateRating, Review): `name`, `image`, `description`, `sku`/`gtin`, `brand`, `offers{price, priceCurrency, availability (schema.org/InStock), url, priceValidUntil, shippingDetails, hasMerchantReturnPolicy}`; price must match visible price; `aggregateRating` only with real on-page reviews. Merchant Center feed is the preferred ecommerce channel for Google [V ai guide].
BreadcrumbList: `itemListElement[]` with `position`, `name`, `item` (URL; last item may omit).
LocalBusiness: `@type` specific subtype, `address` PostalAddress, `geo`, `openingHoursSpecification`, `telephone`, `priceRange`; must match Google Business Profile.
Event, Recipe, VideoObject (`name, description, thumbnailUrl, uploadDate, contentUrl|embedUrl, duration`), JobPosting, SoftwareApplication, Course, Dataset: follow gallery.
FAQPage / HowTo: rich results largely retired for most sites; markup harmless but no promise. [R]
Person/ProfilePage for authorship, `sameAs` to authoritative profiles (entity disambiguation, useful for LLM grounding). [R]
Speakable, Q&A: limited availability. [R]

### 5.4 Common errors
Missing required property; wrong types (price as "$10" instead of number "10.00" + currency); relative URLs; dates not ISO 8601; markup for content not on page; duplicate Product blocks with different prices; `Review` of the site itself on Organization (self-serving reviews not eligible); `availability` as plain text; `@type` typos/case; invalid JSON (trailing comma) from template concatenation; markup in a client-rendered widget that never reaches raw HTML.

### Verify
- Rich Results Test (URL or code) and Schema Markup Validator (schema.org, generic syntax) [R]. Search Console Enhancements reports.
- CI: parse each template's JSON-LD with a JSON parser; validate with `ajv` against a trimmed schema or with a headless call to a validator; snapshot tests per type; assert visible value == JSON-LD value (price, title, rating count).
- Monitor GSC enhancement error counts after deploy.

### MCP data
All JSON-LD/microdata blocks per URL (raw and rendered), parsed types, missing required/recommended properties per Google feature, visible-vs-markup value mismatches, GSC enhancements/rich-result status API data, Merchant Center feed diagnostics if connected.

---

## 6. HTTP semantics, redirects, migrations, logs, crawl budget

### 6.1 Status code rules [V partial; R otherwise]
| Situation | Code | Notes |
|---|---|---|
| OK | 200 | indexable |
| Permanent move | 301 / 308 | passes signals; keep >= 1 year |
| Temporary | 302 / 307 | canonical stays old URL |
| Gone for good | 410 (or 404) | Google treats similarly; [V crawl-budget] "404/410 for permanently removed" |
| Not found | 404 | real status, not soft 404 [V js doc] |
| Needs login | 401 / 403 | [V js doc: 401] |
| Server error | 500 / 503 | 503 + `Retry-After` for planned downtime; prolonged 5xx reduces crawl rate and can drop URLs [R] |
| Rate limit | 429 | Google backs off; hostload [V crawl-budget] |
| Not modified | 304 | implement `ETag`/`If-None-Match`, `Last-Modified` [V] |

Do not use meta refresh / JS redirects for permanent moves unless unavoidable (Google follows but slower and weaker). [R]

Redirect hygiene: 1 hop max, no chains/loops [V "avoid long redirect chains"], http->https and host normalisation in a single hop, preserve path+query where relevant, case and trailing-slash normalised.

```nginx
# one-hop canonical host+scheme
server { listen 80; server_name example.com www.example.com; return 301 https://example.com$request_uri; }
server { listen 443 ssl; server_name www.example.com; return 301 https://example.com$request_uri; }
```

### 6.2 Migration checklist (domain, URL structure, replatform, HTTPS)
Before:
1. Crawl old site: full URL inventory with status, canonical, indexability, titles, h1, inlinks, JSON-LD, hreflang; export GSC top pages/queries (16 months) and backlinks.
2. Map every old URL -> new URL 1:1 (301); prioritise by traffic+links; no mass redirect to home (becomes soft 404).
3. Staging crawlable only to your IPs (auth), not just `noindex` robots; ensure staging noindex/robots do not ship to prod (classic bug). 
4. Keep content, titles, headings, internal links, structured data parity; change one thing at a time when possible.
5. Prepare new sitemaps; lower TTL of DNS; capture baselines (CrUX, rankings, logs).
Launch:
6. Deploy redirects; verify with a list-driven redirect tester (status, hops, final 200).
7. Update canonicals, hreflang, sitemaps, internal links, robots.txt, OG URLs, structured data URLs, feeds, analytics.
8. Domain move: add new property in GSC, use Change of Address tool (domain-level moves) [R]; keep old property verified.
After:
9. Monitor GSC Page indexing, Crawl stats, server logs for 404/5xx spikes, Googlebot hitting old URLs (expected until recrawl).
10. Keep redirects >= 12 months; keep old domain registered.
11. Compare traffic/rankings vs baseline weekly for 8-12 weeks; expect temporary fluctuation.

### 6.3 Crawl budget [V] https://developers.google.com/crawling/docs/crawl-budget (2026-10-02)
- Matters for: 1M+ pages with weekly change; 10k+ with daily change; many "Discovered - currently not indexed". Otherwise not a primary concern.
- Levers: consolidate duplicates; block unimportant URLs via robots.txt; 404/410 removed pages; fix soft 404s; accurate `lastmod`; avoid redirect chains; faster server; support 304; watch "Hostload exceeded" in Crawl Stats; host load = connection-time Google holds, auto-adjusted to site health.

### 6.4 Log file analysis
Fields: timestamp, IP, UA, method, URL (+query), status, bytes, response time, referrer. 
- Verify Googlebot: reverse DNS of IP -> `googlebot.com`/`google.com`, forward-confirm; or match Google's published IP JSON ranges [R]. Spoofed UAs are common (30%+ of "Googlebot" hits on some sites, heuristic).
- Same for AI crawlers where operators publish IP lists (OpenAI, Perplexity, Anthropic publish ranges [R]).
- Reports: hits by bot x status; share of hits to non-indexable/param URLs; hits to 3xx/4xx/5xx; crawl frequency per template/directory; days since last crawl of key URLs; response-time p95 for bot hits; orphan URLs only seen in logs; sitemap URLs never hit.
- Tools: `awk`/`jq`, GoAccess, BigQuery/DuckDB over parquet-ed logs; CDN logs (Cloudflare Logpush, Fastly, CloudFront) are the most complete (HTML cached at edge never hits origin).

```sql
-- DuckDB: googlebot hits by status class and directory
SELECT split_part(path,'/',2) AS dir, status/100 AS cls, count(*) hits
FROM logs WHERE bot='googlebot' GROUP BY 1,2 ORDER BY hits DESC;
```

### Verify
Redirect map test (CSV -> assert `status in (301,308)`, hops == 1, final 200, final canonical == self). Post-launch: 404 rate in logs for Googlebot < 1-2% target (heuristic); `5xx` < 0.1%.

### MCP data
Log ingestion (CDN/origin) with verified-bot flag; Crawl Stats from GSC (API partly; manual export [R]); redirect-chain tester; pre/post crawl diff; GSC Page indexing reasons.

---

## 7. Security, HTTPS, accessibility overlap

- HTTPS site-wide, valid cert chain, auto-renew; HSTS (`Strict-Transport-Security: max-age=31536000; includeSubDomains; preload` once stable); no mixed content; redirect http->https one hop; canonical/sitemap/hreflang all https. HTTPS is a (light) ranking signal and prerequisite for modern APIs. [R]
- Security headers (CSP, `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`) do not rank but avoid compromise; hacked-content and malware flags in GSC "Security issues" cause de-ranking/warnings.
- WAF/bot-protection must allow verified Googlebot (and AI search bots if desired); Cloudflare-style "Block AI bots" toggles and JS challenges return 403/429/503 to crawlers -> silent deindexing. Allow-list by verified IP/ASN, not UA only. [R]
- Rate limiting: never 429/503 permanently to search crawlers.
- Accessibility overlap (same DOM serves SEO, screen readers, and agents):
  - Semantic landmarks (`header/nav/main/article/footer`), single `h1`, logical heading order.
  - `alt` text for informative images, empty `alt=""` for decorative.
  - Descriptive link text; buttons are `<button>`; form inputs have `<label>`.
  - Sufficient contrast, focus visible, keyboard operable; skip links.
  - Captions/transcripts for video (also feed VideoObject and LLM transcripts).
  - Lighthouse's new Agentic Browsing category scores agent-centric accessibility (names/labels, tree integrity, visibility) [V] https://developer.chrome.com/docs/lighthouse/agentic-browsing/ (2026-10-02).

Verify: `npx lighthouse --only-categories=accessibility,best-practices`, `axe-core` (`@axe-core/playwright`) in CI, `ssllabs`/`testssl.sh`, `curl -I` header check, securityheaders-style assertions.

MCP data: TLS/cert expiry, mixed content list, security headers per host, bot-access test (fetch as Googlebot UA from allowed IP vs. generic), axe violations per template, GSC security issues.

---

## 8. Machine-readable content for AI agents and AI crawlers (GEO engineering)

### 8.1 What is documented
- Google: AI Overviews/AI Mode draw from the Search index (RAG + query fan-out); no special optimisation layer; llms.txt not used; chunking not required; structured data not required; measure with the Search Console generative AI performance report. [V] https://developers.google.com/search/docs/fundamentals/ai-optimization-guide (2026-10-02); published 2026-05-15 per [N] https://www.techwyse.com/news/ai-search/google-ai-search-optimization-guide-llms-txt-lighthouse-audit
- Chrome/Lighthouse 13.3 (reported 2026-05-07 [N]) put an "Agentic Browsing" category in the default config: WebMCP tool-registration checks via CDP, agent-centric accessibility, CLS and an `llms.txt` presence audit (404 = not applicable; server errors flagged). It reports "passes N of M checks", not a 0-100 score. [V] https://developer.chrome.com/docs/lighthouse/agentic-browsing/
- WebMCP: proposed W3C-track standard (Google + Microsoft engineers) exposing site tools to in-browser agents through `navigator.modelContext` (e.g. `registerTool()`), plus a declarative HTML form-based variant; announced as early preview Feb 2026 in Chrome 146 Canary behind a flag; a DevTrial whose API will change. [N] https://www.eweek.com/news/google-webmcp-chrome-ai-web-standard-preview/ (accessed 2026-10-02 via search snippets; not canonical). Treat as experimental; do not recommend to production beyond optional pilots.

Conclusion for skills: llms.txt = optional, low-cost, no proven effect on Google; may help coding assistants reading developer docs (Mueller quote reported [N]). WebMCP = watch/pilot. Neither should displace crawlability work.

### 8.2 AI bots: robots.txt control (names from vendor docs; [R], verify against each vendor's page before publishing)
Three families per vendor: training crawler, search/index crawler, user-initiated fetcher.

| Vendor | Training | Search index | User-triggered |
|---|---|---|---|
| OpenAI | GPTBot | OAI-SearchBot | ChatGPT-User |
| Anthropic | ClaudeBot | Claude-SearchBot | Claude-User |
| Perplexity | (PerplexityBot is index) | PerplexityBot | Perplexity-User |
| Google | Google-Extended (token only; controls Gemini/Vertex training & grounding use, not Search ranking/AI Overviews) | Googlebot | Google-Agent / Google user-triggered fetchers (not listed in the overview page fetched) |
| Apple | Applebot-Extended (token) | Applebot | - |
| Common Crawl | CCBot | - | - |
| ByteDance | Bytespider | - | - |
| Meta | meta-externalagent | - | meta-externalfetcher |

Notes [R]: user-triggered fetchers often ignore robots.txt because a human requested the page; Google's overview documents "user-triggered fetchers" as a separate class [V https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers]. Common crawlers always respect robots.txt [V same page]. The Google doc fetched did not list Google-Extended on that page (it is on the "common crawlers" sub-page [R]).

Policy patterns (own):
```
# Visible in AI search/citations, but not used for training
User-agent: OAI-SearchBot
Allow: /
User-agent: Claude-SearchBot
Allow: /
User-agent: PerplexityBot
Allow: /
User-agent: GPTBot
Disallow: /
User-agent: ClaudeBot
Disallow: /
User-agent: CCBot
Disallow: /
User-agent: Google-Extended
Disallow: /
```
Trade-off: blocking training bots does not reduce Google AI Overviews visibility; blocking search/user bots removes you from those assistants' citations. Business decision; expose it as a switch in the plugin.

Enforcement beyond robots.txt: CDN bot management (Cloudflare "AI Crawl Control"/pay-per-crawl-style products [N/R]); verify IP ranges; server logs prove who actually fetches.

### 8.3 Clean HTML for LLM retrieval
Most AI fetchers do not execute JS [R] and chunk by HTML structure; therefore:
- Full content in initial HTML (see section 1).
- Semantic elements and heading hierarchy; one idea per section with a descriptive `h2/h3`; answer-first paragraphs (definition/number in first sentences); tables as real `<table>`; lists as `<ul>/<ol>`; code in `<pre><code>`.
- Stable anchors (`id` on headings) so citations can deep-link; `Last updated` visible + `dateModified` in JSON-LD.
- Avoid content in images/canvas/PDF-only; provide text equivalents.
- Low boilerplate-to-content ratio; keep nav/cookie text out of `<main>`.
- Authorship + organisation entity (Person/Organization with `sameAs`) and primary-source citations on the page (trust signals) [R].
- Markdown alternates: serve `Accept: text/markdown` or `page.md` variants (docs sites; Vercel/Cloudflare "Markdown for agents" style features reported [N/R]); canonicalise them to HTML with `Link: <html-url>; rel="canonical"` and `X-Robots-Tag: noindex` to avoid duplicates.
- Paywall: use `isAccessibleForFree` + `hasPart` structured data for subscription content (Google flexible sampling) [R].
- Feeds: sitemap + Atom/RSS keep `lastmod` honest; IndexNow (Bing, Yandex, others; not Google) pings for fast re-crawl, which also feeds Bing-based assistants [R].

llms.txt (if chosen), spec by llmstxt.org [R]:
```
# Brand
> One-paragraph summary of what the site is.

## Docs
- [Getting started](https://example.com/docs/start.md): install and first call
## Policies
- [Pricing](https://example.com/pricing): plans and limits
```
Rules: UTF-8 Markdown at `/llms.txt`, HTTP 200 `text/plain` or `text/markdown`; no 5xx (Lighthouse flags server errors [V]); absolute links to canonical pages; keep <~50 links; optional `/llms-full.txt`. Do not expect Google ranking effects [V].

### 8.4 Measuring GEO
- Server logs: hits by AI user agents; share of fetches 200 vs blocked.
- Referral analytics: `chatgpt.com`, `perplexity.ai`, `claude.ai`, `gemini.google.com`, `copilot.microsoft.com` referrers (utm often appended `utm_source=chatgpt.com`) [R].
- Prompt-set sampling: fixed set of 30-100 queries run weekly against assistants with web search; log whether brand/URL cited, position, sentiment. Beware nondeterminism: sample N>=5 per prompt. Do not claim "Google AI Overviews rank" from third-party tools; GSC has the generative AI report [V ai-guide, "avoid third-party tools claiming internal Google metrics"].
- GSC Search Appearance / AI performance report (new) for Google surfaces.

### Verify
- `curl -A "OAI-SearchBot" URL` and similar for each UA: status 200 and content parity with browser fetch; robots.txt parser test per UA (use `robotparser`-like lib or Google's open-source robots.txt parser).
- No-JS content check (section 1). Lighthouse agentic category (`npx lighthouse` >= 13.3) for llms.txt/WebMCP/a11y signals.
- Playwright test fetching with `javaScriptEnabled:false` and asserting key facts exist.

### MCP data
robots.txt matrix verdict per bot UA x URL; WAF/CDN challenge detection (does a bot UA get 403/429/JS challenge?); text-only extraction of page (what an LLM sees: Markdown conversion, word count, headings outline, tables); llms.txt presence/validity; AI-bot hit counts from logs; referrals by AI domains from analytics; GSC generative AI performance data when available; citation sampling store.

---

## 9. Headless CMS and e-commerce platform specifics

### 9.1 Headless CMS (Contentful, Sanity, Strapi, Storyblok, Payload, WordPress headless)
- Model SEO fields as first-class content type: `seoTitle`, `metaDescription`, `canonicalOverride`, `noindex` boolean, `ogImage`, `slug`, `hreflang group`, `publishedAt`, `updatedAt`. Make title/description required via validation (editor-time lint).
- Fallback chain: seoTitle -> title; description -> excerpt trimmed.
- Slug policy: lowercase-hyphen, immutable once published, or auto-create 301 on change (keep redirect table in CMS or edge config).
- Preview/draft: draft environments `noindex` + auth; never leak preview URLs in sitemaps.
- Webhooks -> on-demand revalidation / rebuild of affected pages + sitemap; invalidate CDN tags (`Surrogate-Key`/cache-tags) [R].
- Images via CMS image CDN with width/format params; enforce alt text field required.
- Rich text -> semantic HTML (headings not skipped, no `<h1>` in body fields), links resolve to final URLs.
- Single JSON-LD generator in frontend keyed by content type.
- Multi-locale: locale in URL, hreflang group relation in CMS, fallback pages must not be silently duplicated.

### 9.2 WordPress [R]
- Use one SEO plugin (Yoast, Rank Math, SEOPress, AIOSEO); duplicate output of canonical/JSON-LD/sitemap from theme+plugins is the top defect. WP core ships `wp-sitemap.xml`; disable if plugin provides its own.
- Permalinks: post name; avoid changing without redirects (Redirection plugin or server-level).
- Settings > Reading "Discourage search engines" must be off on prod (writes noindex).
- Performance: page cache (server-level/CDN), object cache (Redis), image sizes/WebP/AVIF (core generates), lazy-load (core adds `loading=lazy`, ensure LCP image is exempt: `fetchpriority` support in core since 6.3), remove unused plugins/scripts, `defer`.
- Archives: noindex thin tag/date/author archives; paginate categories; keep `/feed` for discovery.
- Headless WP: use WPGraphQL/REST and make frontend SSR; mirror Yoast data via its REST head fields.

### 9.3 Shopify [R]
- Liquid theme: `{{ canonical_url }}`, `{% render 'structured data' %}`; Dawn theme includes basic Product JSON-LD (check duplicates from apps).
- Fixed URL prefixes (`/products/`, `/collections/`, `/pages/`, `/blogs/`); product also reachable under `/collections/x/products/y` -> Shopify canonicalises to `/products/y`; ensure internal links use canonical form.
- Variants via `?variant=` — canonical stays product URL.
- Filter URLs `?filter.*` / `?sort_by=`: robots.txt.liquid customisation allowed to disallow; Shopify default robots already blocks some.
- Redirects: Online Store > Navigation > URL Redirects (301) and bulk CSV import; changing handle offers auto-redirect checkbox.
- Performance: app bloat is main cause of INP/LCP regressions; audit each app's script; use `loading="lazy"` except hero; Hydrogen/Oxygen (React Router/Remix-based) for headless needs SSR + `getSeoMeta`.
- Sitemap `/sitemap.xml` auto; no direct edit; submit in GSC.
- Markets/hreflang: Shopify Markets generates hreflang for domains/subfolders.

### 9.4 Other e-commerce (Magento/Adobe Commerce, WooCommerce, BigCommerce, Commerce stacks)
- Product variants: one canonical URL per product (or per significant variant with distinct demand); `ProductGroup`/`hasVariant` markup for variants [R].
- Out-of-stock: keep page 200 with `OutOfStock` availability if returning; 404/410 or 301 to successor if discontinued permanently.
- Merchant Center feed + free listings; feed price/availability must match page/JSON-LD.
- Reviews: JSON-LD only if reviews visible and genuine; third-party review widgets rendered via JS may not be in raw HTML.
- Faceted nav per section 4.3; internal search pages `noindex` + robots disallow.
- Checkout/cart/account: disallow in robots.txt and noindex.

### Verify
Platform-neutral crawl test-suite from sections 1-5; plus platform checks: duplicate JSON-LD count == 1 per type; canonical on variant URLs; robots.txt contains cart/search/filter disallows; staging not indexable; sitemap includes only canonical products.

### MCP data
Platform detection (generator meta, headers, `Shopify.theme`, `wp-json`), plugin/app script inventory with transfer size and long-task attribution, CMS field completeness (if CMS API connected), Merchant Center diagnostics.

---

## 10. Verification toolkit (CI and ops)

| Layer | Tool / command | Gate |
|---|---|---|
| Unit | template snapshot: head tags & JSON-LD per page type | required tags present, valid JSON |
| E2E | Playwright: JS disabled and enabled; status codes; redirects; canonical | parity of key content, 404 real |
| Lab perf | Lighthouse CI (`lhci autorun`) on key templates, mobile emulation, 3-5 runs median | LCP<=2.5s, CLS<=0.1, TBT<=200ms (warn), perf score budget |
| Field perf | CrUX API / PSI API / own `web-vitals` RUM | p75 good, no regression > 10% week over week (heuristic) |
| Crawl | headless crawler on staging+prod (Screaming Frog CLI, own crawler, `linkinator`) | no 4xx/5xx internal links, no chains, no orphans, canonical OK |
| Sitemap | script checking each URL | 200, self-canonical, indexable |
| Structured data | JSON parse + schema checks + Rich Results Test (manual/API) | no errors on key templates |
| Robots | parse robots.txt per UA, test important URLs | key URLs allowed; junk blocked; staging fully blocked |
| Security | `curl -I`, ssl test, header assertions | HTTPS+HSTS, no mixed content |
| A11y | axe via Playwright | 0 serious/critical |
| Logs | scheduled query | Googlebot 5xx < 0.1%, 404 trend |
| GSC | URL Inspection API, Search Analytics API, sitemaps API | indexed state of key URLs, coverage reasons |

Pre-deploy SEO regression checklist (ponytail version: 10 assertions that catch ~80% of breakages):
1. `robots.txt` has no `Disallow: /` on prod.
2. No `noindex` on indexable templates.
3. Canonical self-referencing absolute https.
4. Exactly one `<h1>`, non-empty `<title>`.
5. Sitemap URLs 200.
6. Redirect map hop count == 1.
7. JSON-LD parses; required props present.
8. LCP image not lazy, has fetchpriority.
9. hreflang reciprocal.
10. Key content present with JS disabled.

Example Playwright assertions:
```ts
test('product page SEO contract', async ({ page, request }) => {
  const r = await request.get('/products/foo');           // raw, no JS
  const html = await r.text();
  expect(r.status()).toBe(200);
  expect(html).toMatch(/<link rel="canonical" href="https:\/\/example\.com\/products\/foo"/);
  expect(html.match(/<h1/g)?.length).toBe(1);
  const ld = [...html.matchAll(/<script type="application\/ld\+json">([\s\S]*?)<\/script>/g)]
              .map(m => JSON.parse(m[1]));
  expect(ld.some(x => JSON.stringify(x).includes('"Product"'))).toBeTruthy();
  expect((await request.get('/products/does-not-exist')).status()).toBe(404);
});
```

---

## 11. Consolidated MCP data requirements (for seocli-api tool design)

Capability names are generic (constitution V: no vendor leak in tool names) except Google-account services.

| Capability | Output essentials | Used by sections |
|---|---|---|
| Page fetch (raw) | status, headers, redirect chain, TTFB, size, body, per-UA | 1,3,6,7,8 |
| Page fetch (rendered) | rendered DOM, console errors, blocked subrequests, screenshots | 1,5 |
| Crawl (site) | URL graph, depth, inlinks, canonical/robots/hreflang/JSON-LD per URL, duplicates, orphans | 3,4,5 |
| Sitemap analysis | parsed sitemaps vs crawl diff, lastmod sanity, extension types | 4 |
| robots.txt analysis | per-UA allow/deny for URL list, syntax errors, sitemap lines | 3,8 |
| Structured data extraction + validation | blocks, types, missing props, value mismatch with visible text | 5 |
| Web Vitals field | CrUX p75 (URL/origin, phone/desktop), history, histograms | 2 |
| Web Vitals lab | Lighthouse/PSI JSON, LCP subparts, long tasks, CLS sources, waterfall | 2 |
| RUM ingestion (optional) | `web-vitals` attribution beacons | 2 |
| Redirect tester | list-driven chain/loop/final-status | 6 |
| Log analysis | verified bot classification (search + AI), status/dir/template breakdown, recency | 6,8 |
| Search Console (Google account) | Search Analytics, URL Inspection (indexed? user vs Google canonical?), sitemaps, Page indexing, Crawl Stats (manual), generative AI performance report | 3,4,6,8 |
| Merchant Center (Google account) | feed diagnostics | 9 |
| Text-for-LLM extraction | clean Markdown/outline from raw HTML, word count, tables | 8 |
| Security snapshot | TLS, HSTS, mixed content, headers | 7 |
| A11y scan | axe summary per template | 7 |

Privacy note: Search Console/GA data are private customer data (constitution VII: never to non-EU models); public page fetches are fine.

---

## 12. Prioritisation heuristic for the plugin (what to fix first)

1. Indexability blockers: robots.txt/noindex/auth/5xx/WAF blocks, soft 404s, wrong canonicals.
2. Content availability: server-rendered critical content and links.
3. Duplicate-URL control: canonical + redirects + params/facets.
4. Discovery: sitemaps accurate, internal links, orphans.
5. Page experience: CWV at p75 field; LCP before INP before CLS unless data says otherwise.
6. Structured data for features the business can actually win (Product, Organization, Article, Breadcrumb, LocalBusiness, Video).
7. International: hreflang correctness.
8. GEO extras: AI-bot policy decision, clean HTML, optional llms.txt, WebMCP pilot (experimental).
Every recommendation should carry the 4 fields from constitution VIII: first-principle observation, dependency, falsifiability check (the verify step above), leading indicator (e.g. crawl hits in logs, GSC indexed count, CrUX p75 trend).

---

## 13. Source list (access date 2026-10-02 unless noted)

Fetched [V]:
- https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics
- https://web.dev/articles/vitals
- https://web.dev/articles/optimize-lcp
- https://web.dev/articles/optimize-inp
- https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers
- https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading
- https://developers.google.com/search/docs/specialty/international/localized-versions
- https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- https://developers.google.com/crawling/docs/crawl-budget
- https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag
- https://developers.google.com/search/docs/appearance/structured-data/sd-policies
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide
- https://developer.chrome.com/docs/lighthouse/agentic-browsing/

Secondary [N] (search snippets / trade press):
- https://www.techwyse.com/news/ai-search/google-ai-search-optimization-guide-llms-txt-lighthouse-audit
- https://www.eweek.com/news/google-webmcp-chrome-ai-web-standard-preview/

Recalled [R], to re-verify before encoding as hard rules: framework APIs (Next/Nuxt/Astro/SvelteKit), WordPress/Shopify specifics, AI bot user-agent names and which ignore robots.txt, Google-Extended scope, CrUX API shapes, Lighthouse CI assertion keys, news sitemap limits, robots.txt 4xx/5xx handling, DOM-size thresholds, Change of Address tool, dynamic-rendering doc wording, IndexNow, llms.txt spec.

Open questions for a follow-up pass:
1. Exact current Googlebot HTML fetch limit (15 MB generic vs smaller for Search).
2. Exact list of Lighthouse agentic audits and their IDs (docs page described categories, not IDs).
3. WebMCP status as of Oct 2026 (stable in Chrome? spec stage?).
4. Current list of surviving Google rich result types (search gallery) and 2026 deprecations.
5. Vendor docs for each AI crawler (OpenAI, Anthropic, Perplexity, Apple, Meta) incl. published IP ranges.
