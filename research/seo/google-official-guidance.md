# Google official SEO/GEO guidance: research base for the seocli-seo plugin

Compiled 2026-10-02 (all "accessed" dates = 2026-10-02 unless noted). Audience: skill authors for Italian agencies and developers; MCP tool designers.

## 0. How to read this file

Evidence grades used on every claim:

- **[F]** = fetched from the primary page during this pass (Google Search Central, web.dev, Google support, raterhub PDF). Text was read through a summarizing fetch tool, except the Rater Guidelines PDF, which was converted locally (pdftotext) and read directly.
- **[S]** = seen in a search-result snippet of the primary page or in secondary press coverage quoting Google; re-verify before shipping a skill that depends on it.
- **[B]** = background knowledge of Google documentation (stable, long-standing), not re-fetched in this pass; verify the exact number before hard-coding.
- **CHANGED 2024-26** = marks a rule, product or policy that changed in 2024-2026.

Quotes are kept short; everything else is paraphrase. URLs are the canonical source. Where this file says "Google says", it means the cited page.

Known gaps (honest list): the November 2025 "update on our efforts to simplify the search results page" post body could not be retrieved (only its title and the June 2025 sibling post were seen); the 2026 Search Generative AI performance report post body was not retrieved (details from secondary coverage, grade [S]); the Rater Guidelines have no explicit "AI Overviews" section in the Sept 2025 text I converted (a grep for "AI Overview" returned nothing), although press reports claim AI Overview examples; treat that press claim as unverified. Google Business Profile guidance was read only as a summary page. No Rich Results Test or Search Console was run.

---

## 1. Top-line synthesis (what a skill author must internalize)

1. **There is no separate "AI SEO".** Google's May 2026 guide for generative AI features says AI Overviews and AI Mode ride on the same crawl, index and ranking systems; no extra requirements, no special files or markup. It explicitly calls out as wasted effort: llms.txt, special AI markup, chunking content into tiny pieces, rewriting for AI, inauthentic brand mentions, over-focusing on schema. [F] https://developers.google.com/search/docs/fundamentals/ai-optimization-guide and https://developers.google.com/search/docs/appearance/ai-features. CHANGED 2024-26.
2. **Indexing is the real gate.** Eligibility for any feature (including AI features) requires being indexed and snippet-eligible. A crawler/indexability audit comes before any content or schema advice. [F] ai-features page; how-search-works page.
3. **Quality policy is now enforced against scale, not against AI.** Scaled content abuse is judged by value added, not by production method; Rater Guidelines say generative AI use alone does not set the rating. [F] spam-policies; QRG 4.6.5-4.6.6.
4. **Core Web Vitals = LCP 2.5 s, INP 200 ms, CLS 0.1 at the 75th percentile of field data.** FID is gone (INP replaced it in March 2024). Field data (CrUX) decides; lab data diagnoses. [F] https://web.dev/articles/vitals. CHANGED 2024.
5. **Structured data has shrunk.** FAQ rich results are gone (documentation removed, feature dropped by mid-2026), HowTo was reduced in 2023, seven further types phased out from June 2025, and Search Console/API support for those types ended from January 2026. Markup still matters for Product, Organization, LocalBusiness, Article, Breadcrumb, Video, Review, Event, etc. [F]/[S] sections 12-13. CHANGED 2024-26.
6. **Measurement changed.** Search Console now has a Search Generative AI performance report (announced 2026-06-03, on every property by 2026-08-12 per press); it shows impressions, pages, countries, devices, dates, but no clicks/CTR. AI feature traffic is otherwise counted in the normal "Web" search type. [S] section 20. CHANGED 2026.

---

## 2. Search Essentials and how Search works

### 2.1 Three stages
- Crawling: Googlebot discovers URLs from known pages, extracted links and sitemaps, and renders with Chrome (JS executed). Indexing: content analysed, duplicates clustered, a canonical chosen, signals stored. Serving: results chosen from the index by relevance and quality. [F] https://developers.google.com/search/docs/fundamentals/how-search-works (accessed 2026-10-02)
- Google states indexing is **not guaranteed** even for compliant pages, and that it accepts no payment for crawl frequency or ranking. [F] same.

### 2.2 Search Essentials (three pillars)
Technical requirements (most sites pass without noticing), spam policies, key best practices. Meeting all of them still does not guarantee crawl/index/serve. [F] https://developers.google.com/search/docs/essentials
Technical requirements in practice [B]: Googlebot not blocked; page returns HTTP 200; page has indexable content in a supported file type.

### 2.3 SEO Starter Guide (last updated 2025-12-10) [F]
Source: https://developers.google.com/search/docs/fundamentals/seo-starter-guide
Key rules:
- Content first: unique, up to date, "helpful, reliable, and people-first"; anticipate query variants.
- Descriptive URLs, topical directories, one canonical per piece of content, redirects for non-preferred versions.
- Sitemap is optional but helpful; let Google fetch CSS/JS; verify with URL Inspection.
- Unique descriptive `<title>`; meta description is 1-2 sentences summarising the page; descriptive alt text; descriptive anchors; `nofollow` (or sponsored/ugc) for untrusted links.
- Timing: effects take "a few weeks" to assess; some show within hours, others take months.
- Myths dismissed: meta keywords ignored; keyword stuffing is spam; domain-name keywords have minimal effect; no minimum or maximum word count; **E-E-A-T is not a ranking factor** (it is a framework used to evaluate content quality; see section 7).

### 2.4 Analyst vs developer
- Analyst: audit against Essentials (indexable? useful? unique?), prioritize by impact, explain limits ("not guaranteed").
- Developer: guarantee 200 status, crawlable HTML links, rendered content, canonical/redirect hygiene, titles/descriptions templated uniquely.

### 2.5 MCP data needed
Crawl (status, headers, robots, meta, canonical, links, titles), render diff (raw vs rendered DOM), GSC URL Inspection and Page indexing report, sitemap fetch.

---

## 3. Crawling: robots.txt, status codes, Googlebot, crawl budget

### 3.1 robots.txt [F]
Source: https://developers.google.com/search/docs/crawling-indexing/robots/intro
- Purpose: manage crawl load, "not a mechanism for keeping a web page out of Google." A blocked URL can still be indexed (URL plus anchor text) if linked elsewhere.
- Hiding content: use `noindex` (meta or X-Robots-Tag), password protection, or removal. Robots.txt is not a security tool.
- Size limit: 500 KiB (content beyond is ignored) [F per page reference; B for exact ignore behaviour].
- Critical interaction: if a URL is disallowed, Google cannot see its `noindex`/canonical, so the directive is ignored. [F] robots-meta-tag page.
- robots.txt HTTP handling [B]: 2xx parsed; 4xx (except 429) treated as no robots file (allow all); 5xx or 429 treated as temporary full disallow, and if persistent for about 30 days the last cached copy / allow-all logic applies. Verify before hard-coding.

### 3.2 Googlebot facts [F]
Source: https://developers.google.com/search/docs/crawling-indexing/googlebot
- Two crawler forms (smartphone, desktop) sharing one robots.txt token; most crawling is smartphone because of mobile-first indexing.
- **Size limit: first 2 MB of supported file types are crawled; PDFs up to 64 MB.** Each referenced CSS/JS resource is fetched separately. CHANGED 2024-26 (older docs and many blogs say 15 MB; the current page says 2 MB; verify live before relying on it).
- User-agent strings are spoofed often; verify by reverse DNS or Google's published IP ranges before blocking.
- Typical access no more than once every few seconds.

### 3.3 HTTP status handling [F]
Source: https://developers.google.com/search/docs/crawling-indexing/http-network-errors
- 2xx processed; not a guarantee of indexing.
- 3xx: up to **10 redirect hops** followed. 301 = strong signal the target is canonical; 302 weaker.
- 4xx (except 429): not indexed; previously indexed URLs removed; crawl frequency declines.
- 429 treated like a server error (overload signal).
- 5xx: crawl rate throttled; indexed URLs kept at first but eventually dropped if persistent.
- Soft 404 (200 with "not found" content) is a defect: return real 404/410.

### 3.4 Crawl budget [F]
Source: https://developers.google.com/search/docs/crawling-indexing/large-site-managing-crawl-budget
- Only matters for: **1M+ pages with weekly changes; 10k+ pages with daily changes; or many URLs in "Discovered - currently not indexed".** For most client sites (Italian SMEs) crawl budget is not the problem; do not sell it as one.
- Two parts: crawl capacity limit (server health) and crawl demand (popularity, staleness, inventory).
- Levers: consolidate duplicates; robots.txt for unimportant URLs; keep sitemaps and lastmod accurate; eliminate soft 404s; shorten redirect chains; speed up server; return 404/410 for gone pages; support 304.
- `noindex` does not save crawl budget (page is still fetched).

### 3.5 Common mistakes
Blocking CSS/JS; using robots.txt as noindex; leaving staging `Disallow: /` or `noindex` after launch; infinite calendar/filter URL spaces; redirect chains over a few hops; soft 404s; 5xx during traffic spikes.

### 3.6 Analyst vs developer
- Analyst: read the Page indexing report buckets; classify "Discovered/Crawled - currently not indexed" as quality vs crawl problem; tie to log samples.
- Developer: fix status codes, conditional GET, robots.txt in version control with a test, firewall allowlist of verified Googlebot only.

### 3.7 MCP data needed
robots.txt fetch with status/size; per-URL status chain (hop count); robots-allowed check per URL for the Google token; response headers (X-Robots-Tag, Link canonical); server logs if the agency can supply them; GSC Crawl stats (not in the API; manual) and URL Inspection API.

---

## 4. Indexing controls and snippets

### 4.1 Robots meta / X-Robots-Tag [F]
Source: https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag
- `noindex`, `nofollow`, `nosnippet`, `max-snippet:N` (0 = none, -1 = no limit), `max-image-preview:none|standard|large`, `max-video-preview:N`, `unavailable_after`.
- Multiple/conflicting rules: the more restrictive wins (`nosnippet` beats `max-snippet:50`).
- `data-nosnippet` attribute (span/div/section) excludes parts of a page from snippets.
- Page must be crawlable for the directive to be seen.
- These controls also govern AI Overviews/AI Mode snippets (ai-features page). [F]

### 4.2 Titles and snippets [F]
Source: https://developers.google.com/search/docs/appearance/title-link
- Every page should have a `<title>`; descriptive, concise, no boilerplate repeated across pages, no "Home".
- No character limit on `<title>`; display is truncated by device width (the oft-quoted 50-60 characters / 600 px figures are tool heuristics, not Google rules).
- Google builds the title link from `<title>`, main visible heading, `<h1>`, `og:title`, anchors; it rewrites when the title mismatches the page or is stuffed/obsolete (e.g. outdated years).
- Meta description: not a ranking factor; Google may use it or generate a snippet from page text [B].

### 4.3 MCP data needed
Raw vs rendered meta robots, X-Robots-Tag, title, h1, description; SERP snapshot showing the title Google actually displays (to detect rewrites).

---

## 5. Canonicalization and duplicates

Source: https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls [F]
- Signal strength: **redirect (strong) > rel="canonical" (strong) > sitemap inclusion (weak)**. Google may still choose a different canonical than declared.
- Use absolute URLs in `rel="canonical"`; add a self-referencing canonical on the canonical page; link internally to the canonical URL; non-HTML via `Link:` header.
- Do NOT: use robots.txt for canonicalization; use URL removal tool for it; give conflicting canonicals through different methods; use URL fragments as canonical; use `noindex` to steer canonical selection among your own duplicates.
- Common duplicates: http/https, www/non-www, trailing slash, uppercase, parameters (utm, sort), print versions, AMP leftovers, session IDs, faceted URLs, pagination misuse.
- [B] Canonical in `<body>` is ignored; multiple canonical tags conflict; JS-injected canonical works but must match the HTML value (JavaScript SEO page, section 6).

Analyst: use GSC URL Inspection "User-declared vs Google-selected canonical" to find disagreement. Developer: single normalisation layer (redirects), one canonical template, tests for parameters.
MCP data: for each URL, declared canonical (HTML and rendered), HTTP redirect chain, Google-selected canonical (URL Inspection API), sitemap membership, internal-link target consistency.

---

## 6. JavaScript SEO and rendering

Source: https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics [F]
- Three phases: crawl, render, index. Rendering uses evergreen Chromium; the render queue can delay JS-dependent content by seconds or longer.
- Links must be `<a href>`; fragment-based routes (`#/page`) are unreliable. Use the History API.
- Status codes: real 404/401 for missing/login pages; for SPAs, either redirect to a server-side 404 route or inject `<meta name="robots" content="noindex">` to avoid soft 404.
- Titles, descriptions, canonical can be set by JS but keep consistent with the HTML; JS-generated JSON-LD is supported (test it).
- Shadow DOM is flattened at render; use slots so content appears in the rendered HTML.
- Fingerprint asset filenames (Googlebot caches aggressively).
- SSR remains the most robust choice; dynamic rendering is described by Google as a workaround, not a recommendation [B].
- [B] A `noindex` present in the initial HTML may stop Google from rendering/indexing JS that would remove it; do not rely on JS to remove noindex.
- [B] Lazy-loaded content must load when visible in the viewport without user interaction; use IntersectionObserver or native `loading="lazy"`.

Analyst: compare raw vs rendered HTML; check that main content, links and meta exist in the rendered DOM. Developer: SSR/SSG for indexable routes, no hash routing, server-side status codes.
MCP data: raw HTML fetch and headless-rendered DOM (with network and console), diff of title/h1/canonical/links/text/structured data, screenshot, blocked resources list (robots).

---

## 7. Mobile-first indexing

Source: https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing [F]
- Only the mobile page's content is used for indexing; mobile-first rollout completed for the web by 2024 [B]. CHANGED 2024.
- Parity required for: primary content, titles/meta, structured data (Breadcrumb, Product, VideoObject), headings, alt text, image/video quality.
- Avoid: `noindex` on mobile only, error pages on mobile, URL fragments, blocking images/CSS/JS via robots.txt, lazy-loading primary content that needs user interaction, unstable image URLs, intrusive ads.
- Responsive design is the recommended configuration.
MCP data: fetch with smartphone UA and viewport, compare to desktop UA (text, links, structured data, meta).

---

## 8. Page experience and Core Web Vitals

### 8.1 Thresholds [F]
Source: https://web.dev/articles/vitals

| Metric | Good | Needs improvement | Poor |
|---|---|---|---|
| LCP | <= 2.5 s | 2.5-4 s | > 4 s |
| INP | <= 200 ms | 200-500 ms | > 500 ms |
| CLS | <= 0.1 | 0.1-0.25 | > 0.25 |

- Evaluate at the **75th percentile** of page loads, segmented mobile/desktop; a page/origin passes only if all three are Good.
- INP became a stable Core Web Vital in 2024 and replaced FID (March 2024). CHANGED 2024.
- TTFB (not a CWV): good <= 0.8 s, poor > 1.8 s. [F] https://web.dev/articles/ttfb
- CrUX: 28-day rolling window of real Chrome users, URL and origin levels, data only when traffic is sufficient [B].

### 8.2 Role in ranking [F]
Source: https://developers.google.com/search/docs/appearance/page-experience
- Core Web Vitals are used by ranking systems, but good scores do not guarantee top rankings; relevance wins. Page experience is assessed per page, with some site-level assessment. Checklist: good CWV, HTTPS, mobile friendliness, limited intrusive ads/interstitials, clear main content.
- Search Console's CWV report and PageSpeed Insights are the standard measures; Lighthouse is lab only.

### 8.3 Optimization guides
**LCP** [F] https://web.dev/articles/optimize-lcp
- Four subparts and ideal budget: TTFB ~40%, resource load delay <10%, resource load duration ~40%, element render delay <10%.
- Priority: (1) eliminate load delay: make the LCP resource discoverable in initial HTML (not behind JS/CSS), use `fetchpriority="high"`; (2) eliminate render delay: cut render-blocking CSS, defer JS, consider SSR; (3) shrink load duration: modern formats (WebP/AVIF), CDN, caching; (4) cut TTFB: fewer redirects, edge caching.
- Common mistake: **never lazy-load the LCP image.**
**INP** [F] https://web.dev/articles/optimize-inp
- Target 200 ms at p75. Interaction = input delay + processing + presentation delay.
- Fixes: reduce main-thread blocking and long tasks at load; break up event-callback work (yield via setTimeout/scheduler); reduce DOM size; CSS `content-visibility`; avoid layout thrashing; careful with client-side HTML rendering.
- Diagnose from field data (RUM/CrUX) first, then reproduce in DevTools.
**CLS** [F] https://web.dev/articles/optimize-cls
- Image `width`/`height` (or CSS `aspect-ratio`); reserve space for ads/embeds (`min-height`); fonts: `font-display: optional`, fallback metrics, preload; animate with `transform` not `top/left`; enable bfcache.

### 8.4 Common mistakes
Optimizing Lighthouse score while CrUX p75 fails; treating lab LCP as truth; hero image lazy-loaded; cookie-banner/consent causing CLS; third-party tags blowing INP; fixing the homepage only (CrUX groups by URL/origin).

### 8.5 Analyst vs developer
- Analyst: pull CrUX per template/URL group, identify which metric and which segment (mobile) fails, quantify share of pages affected, prioritise templates by traffic from GSC.
- Developer: LCP element identification, subpart timing breakdown, long-task attribution (LoAF), image pipeline, font strategy.

### 8.6 MCP data needed
CrUX API (URL and origin, phone/desktop, p75, histogram, 28-day), CrUX History API for trends, PageSpeed Insights API (field+lab), Lighthouse lab runs with LCP subparts and long-animation-frame attribution, GSC CWV report groups (not in API: use CrUX).

---

## 9. Helpful, people-first content and E-E-A-T

Source: https://developers.google.com/search/docs/fundamentals/creating-helpful-content [F]
- Primary principle: content made to help people, not to manipulate rankings.
- E-E-A-T (Experience, Expertise, Authoritativeness, Trust): Google says **trust is the most important**; stronger alignment expected for YMYL topics. It is a framework for evaluating quality, not a single ranking factor (Starter Guide).
- Self-assessment: original research/analysis, substantial value over other sources, comprehensive coverage, visible authorship with credentials, would you bookmark/recommend it.
- **Who/How/Why**: Who created it (clear bylines, author background, no fake profiles); How it was produced (disclose automation/AI where a reader would expect it); Why it exists (to serve readers first, not search traffic).
- Cannot be fixed by tricks: recovery from helpfulness problems is site-wide and slow (months).

Analyst: content audit by intent and uniqueness; flag thin, templated, near-duplicate or AI-bulk pages; check author/About/contact transparency. Developer: Person/Organization markup consistent with visible bylines; author pages crawlable.
MCP data: crawl text + similarity clustering, word/entity coverage vs SERP top results, GSC per-page query performance, SERP competitor pages.

---

## 10. Spam policies

Source: https://developers.google.com/search/docs/essentials/spam-policies (last updated 2026-08-28 per page) [F]. CHANGED 2024-26 (see 10.2).

### 10.1 Policy list (18 entries on the page)
Cloaking; doorway abuse; expired domain abuse; hacked content; hidden text and links; keyword stuffing; link spam; machine-generated traffic; malicious practices (incl. back-button hijacking); misleading functionality; scaled content abuse; scraping; site reputation abuse; sneaky redirects; thin affiliation; user-generated spam; policy circumvention; scam and fraud.
- Enforcement: automated (SpamBrain) and manual actions; outcome is demotion or removal.

### 10.2 The three policies added March 2024 and their detail
**Scaled content abuse** [F]: many pages generated mainly to manipulate rankings. Examples: generative AI (or other tools) producing many pages without added value; scraping feeds/search results with synonymizing or translation; stitching sources without value. Method is irrelevant: human or AI. Exclude such pages with `noindex`/robots if you cannot improve them.
**Site reputation abuse** [F]: third-party content published on an established host mainly to exploit its ranking signals (e.g., sponsored payday-loan reviews on an education site; low-quality casino reviews on a medical site). Not violations (per page): wire/press release syndication, user-generated platforms, clearly authored editorial columns, affiliate content with `rel=nofollow/sponsored`. EEA note: from August 2026 the page describes separate classification/ranking instead of manual penalties for EEA; outside EEA manual actions can apply. Relevant for Italian publishers: this is an EEA market. Also: the August 2026 spam update did not target link spam or this policy (separate systems) [S] https://www.relevantaudience.com/seo/google-august-2026-spam-update/.
**Expired domain abuse** [F]: repurposing expired domains mainly to rank with low-value content (e.g., affiliate content on a former government site). Buying an expired domain to genuinely continue the original purpose is not the target [B].

### 10.3 Spam updates (dated, [S])
2026: March, June and August spam updates (August: began 2026-08-18, done 2026-08-21). Google said no new policies, follow existing ones. Sources in section 21.

### 10.4 Analyst vs developer
- Analyst: policy-by-policy screening checklist (hidden text, doorways, parasite sections, programmatic page sets, link schemes, expired-domain history); manual actions report in GSC.
- Developer: no cloaking by UA/IP; no conditional redirects; sanitise UGC and use `rel=ugc`; patch hacked-content vectors.
- MCP data: crawl with Googlebot vs normal UA diff, hidden-text detection in rendered DOM, backlink data (third-party), Wayback/domain history, GSC Manual actions (not in API; manual), security issues.

---

## 11. Core updates and recovering from drops

Source: https://developers.google.com/search/docs/appearance/core-updates [F]
- Several broad updates a year; aim: "helpful and reliable results". Dates: Search Status Dashboard https://status.search.google.com/ (2026: March core update ran 2026-03-27 to 2026-04-08; May core update started 2026-05-21, up to two weeks [S]; a Feb 2026 Discover update [S]).
- Procedure: wait at least one week after completion; compare equivalent week-over-week periods in GSC; small position moves are normal; large drops warrant an honest whole-site assessment against the people-first questions.
- Do not make reactive tweaks; improvements may take days to months to be recognised; improvements can be rewarded between core updates.
- Analyst skill: segment by page type/query class/device/country to find what lost; separate core-update from technical (indexing, CWV, migration) and seasonal effects; correct dates via the dashboard.
- MCP data: GSC daily performance by page/query/device (16 months), annotated update calendar (status dashboard feed), index coverage history, SERP before/after for lost queries.

---

## 12. Structured data: rules and supported features

### 12.1 General guidelines [F]
Source: https://developers.google.com/search/docs/appearance/structured-data/sd-policies
- Formats: **JSON-LD recommended**, Microdata, RDFa.
- Pages must be crawlable and indexable (no robots.txt block, no noindex).
- Markup must reflect visible content; do not mark up content not visible to users; no misleading or impersonating markup.
- Include all required properties; add recommended properties to improve eligibility.
- No guarantee that valid markup shows a rich result.
- Violations can trigger a **structured data manual action** (removes rich result eligibility; does not affect normal web ranking).
- Validate with Rich Results Test and the Search Console enhancement reports.

### 12.2 Currently documented features [F]
Source: https://developers.google.com/search/docs/appearance/structured-data/search-gallery
Article, Breadcrumb, Carousel, Course list, Dataset, Discussion forum, Education Q&A, Event, Image metadata, Job posting, Local business, Math solver, Movie, Organization, Product, Profile page, Q&A, Recipe, Review snippet, Software app, Speakable, Subscription/paywalled content, Vacation rental, Video. (Page-listed; the summarizing fetch did not state removals explicitly.)

### 12.3 What was removed or reduced (CHANGED 2023-26)
- Aug 2023: HowTo rich results desktop-only (later gone); FAQ limited to well-known government/health sites. [S] https://developers.google.cn/search/blog/2023/08/howto-faq-changes
- June 12 2025: seven features phased out: Book Actions, Course Info, ClaimReview, Estimated Salary, Learning Video, Special Announcement, Vehicle Listing; Search Console and API support for them removed from January 2026. [S] https://developers.google.com/search/blog/2025/06/simplifying-search-results
- Nov 2025: further "simplify the search results page" update (post body not retrieved). [S] https://developers.google.com/search/blog/2025/11/update-on-our-efforts
- FAQ rich results: Google's FAQ page states they no longer appear; the fetched summary says the documentation was removed and the feature gone by May-June 2026; press says the FAQ appearance, rich result report and Rich Results Test support end June 2026. Dates differ between sources, so treat as "FAQ rich results are over; do not sell FAQ schema for rich results". [F]/[S] https://developers.google.com/search/docs/appearance/structured-data/faqpage
- Sitelinks search box removed Oct 2024; mobile breadcrumb/URL display simplified Jan 2025 (as listed in the blog archive). [S]
- Google's line: unused structured data causes no harm and has no visible effect; there is no urgency to remove it, though for FAQ the page now says remove (conflicting wording; skill should say "stop investing, optional removal").

### 12.4 Per-type essentials
- **Article** [F] https://developers.google.com/search/docs/appearance/structured-data/article: no required properties; recommended `headline`, `image` (multiple aspect ratios 16x9, 4x3, 1x1), `datePublished`, `dateModified` (ISO 8601), `author`. List each author separately as Person/Organization with a profile URL; `author.name` contains only the name; all visible authors must be in markup.
- **LocalBusiness** [F] https://developers.google.com/search/docs/appearance/structured-data/local-business: required `name`, `address` (PostalAddress); recommended `telephone`, `openingHoursSpecification`, `url` (specific location), `geo` (at least 5 decimals), `priceRange` (under 100 characters); use the most specific subtype; images crawlable.
- **Product** and e-commerce: section 17.
- **Organization**: logo, contact, identifiers, return policy (via `hasMerchantReturnPolicy`) [F] ecommerce intro page.
- **Video**: section 15. **Breadcrumb**: helps meaningful breadcrumb display [F].

### 12.5 Common mistakes
Marking up invisible content; review markup of self-serving or site-wide reviews [B]; stale prices/availability in JSON-LD vs page; JSON-LD generated by plugin duplicating theme markup; mismatched mobile vs desktop markup; relying on FAQ/HowTo.

### 12.6 Analyst vs developer
- Analyst: map page templates to eligible features and business value; stop recommending deprecated types.
- Developer: generate JSON-LD server-side from the same data source as the visible page; unit-test against required properties; reconcile with Merchant feed.
- MCP data: rendered-DOM JSON-LD/microdata extraction, parse+validate against Google's property requirements, compare to visible text, GSC rich-result enhancement reports (limited API; manual), URL Inspection rich results verdicts.

---

## 13. Hreflang and international

Source: https://developers.google.com/search/docs/specialty/international/localized-versions [F]
- Three equal methods: HTML `<link rel="alternate" hreflang>`, HTTP `Link` header, XML sitemap `xhtml:link`.
- Rules: every version references itself and all others; **return links required** ("if page X links to page Y, page Y must link back"); ISO 639-1 language, optional ISO 3166-1 region (`en-GB`, `it-IT`); region alone is invalid; `x-default` for unmatched users / selector pages.
- Mistakes: missing reciprocity; wrong codes (`UK`, `EU`); country without language; missing self-reference; hreflang pointing to redirected/noindex/canonicalised-away URLs [B].
- Italian relevance: `it-IT` vs `it-CH`; Italian sites targeting Italy only do not need hreflang; do not auto-redirect by IP (Googlebot crawls from US) [B].
- MCP data: crawl-based hreflang graph with reciprocity check, target status/indexability, canonical conflicts, sitemap-based hreflang parse.

---

## 14. Site moves and migrations

Source: https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes [F]
- Phases: learn best practices; prepare the new site; map old to new URLs; execute with server-side redirects; monitor.
- **Keep redirects "generally at least 1 year"**, as long as possible.
- Mistakes: leaving staging `noindex`/robots blocks; broken or chained redirects; unmonitored index errors; server capacity during heavier crawling.
- Expect weeks (small/medium sites) or longer for new URLs to show; processing is per URL.
- [B] Use Search Console Change of Address for domain moves (not for path-only changes); keep both properties verified.
- Analyst: pre-migration inventory of ranking URLs/backlinks, 1:1 redirect map, post-launch coverage checks. Developer: 301 map in server config, test harness for hop count and status, preserve canonical, hreflang, sitemap, structured data.
- MCP data: old-URL list from GSC/crawl, redirect tester (status, hops, target 200), indexation comparison, rank/impression tracking per migrated page.

---

## 15. Pagination, faceted navigation, parameters

### 15.1 Pagination [F]
Source: https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading
- Distinct URLs per page (`?page=n`); `<a href>` links to the next page; **self-referencing canonical on each page** (not all pointing to page 1); fragments (`#`) ignored; **`rel=prev/next` no longer used** by Google.
- Don't index sort/filter variations (noindex/robots); load-more/infinite scroll needs crawlable paginated URLs behind it.

### 15.2 Faceted navigation [F]
Source: https://developers.google.com/search/docs/crawling-indexing/crawling-managing-faceted-navigation
- Problem: overcrawling and slower discovery. Best control: robots.txt disallow patterns for filter parameters (with allow for needed bases); URL fragments for filters (not crawled); weaker: canonical to unfiltered, nofollow on filter links.
- If filtered pages must be indexed: `&` separators, consistent filter order, **404 for empty/invalid combinations** (not redirect to error page).
- MCP data: parameter inventory from crawl and GSC, crawl-depth/URL-space size, share of indexed URLs that are filter combinations, canonical/robots consistency.

---

## 16. Images, video, Discover, News

### 16.1 Images [F] https://developers.google.com/search/docs/appearance/google-images
- Use `<img>` (not CSS backgrounds) so crawlers find images; `srcset`/`<picture>` with a `src` fallback; formats BMP, GIF, JPEG, PNG, WebP, SVG, AVIF; descriptive filenames; alt text for accessibility, no keyword stuffing; image sitemap optional; preferred image via `og:image` or `primaryImageOfPage`; high-resolution, no extreme aspect ratios.
- Related: dimensions for CLS; never lazy-load LCP image (section 8).
### 16.2 Video [F] https://developers.google.com/search/docs/appearance/video
- Video must be on an **indexed watch page** that performs in Search; embedded with `<video>`, `<iframe>`, `<embed>`, `<object>`; not hidden or user-interaction dependent; JS-injected video must appear in rendered HTML.
- Distinguish watch page URL, player URL, video file URL; give `contentUrl` (or sitemap `video:content_loc`).
- Thumbnails: stable URL, accessible (no login/robots block), min 60x30 px (larger preferred), JPEG/PNG/WebP, at least 80% non-transparent pixels.
- Key moments via `Clip`/`SeekToAction` or YouTube timestamps.
### 16.3 Discover [F] https://developers.google.com/search/docs/appearance/google-discover
- No special markup: indexed + policy-compliant content is eligible, not guaranteed.
- Images at least 1200 px wide (over 300,000 px total, 16:9 suggested), enable `max-image-preview:large`; no clickbait; timely/insightful content; strong page experience.
- Discover traffic is volatile; Search Console Discover report keeps 16 months. Feb 2026 Discover update reported [S]. Gen-AI features in Discover are covered by the 2026 gen-AI report [S].
### 16.4 News
The Google News page I tried (/specialty/google-news) returned 404; not covered by verified evidence. Background [B]: Publisher Center is optional; use clear bylines, dates (`datePublished`/`dateModified`), consistent article markup; news sitemap is optional and for last-2-days content. Verify before use.

---

## 17. E-commerce: Merchant Center and product data

### 17.1 Structured data [F]
Sources: https://developers.google.com/search/docs/appearance/structured-data/product, https://developers.google.com/search/docs/specialty/ecommerce/introduction-to-structured-data-on-ecommerce-sites
- Two kinds: **product snippets** (page where purchase is not possible; review pros/cons support) and **merchant listings** (purchase pages; sizing, shipping, return policy).
- Key properties: name, image, offers (price, priceCurrency, availability), identifiers (GTIN/MPN/brand), ratings; shipping cost and return policy details for merchant listings; variants via ProductGroup/variant structure; Organization markup for return policy and loyalty programme.
- Best eligibility: structured data on the page **plus** a Merchant Center feed, which lets Google verify. Adding merchant-listing properties also makes snippets eligible.
### 17.2 Merchant Center product data [F]
Source: https://support.google.com/merchants/answer/7052112
- Required basics: `id` (max 50 chars, unique), `title` (max 150), `description` (max 5000), `link`, `image_link` (min 500x500 px **as of 2027-01-31**, CHANGED 2024-26), `price` (ISO 4217, must match the landing page), `availability` (`in_stock`, `out_of_stock`, `preorder`, `backorder`), GTIN or MPN+brand for new products.
- Top disapproval causes: wrong `google_product_category`, missing variant attributes (size/colour), low-quality images, feed/site mismatches.
- AI-generated titles/descriptions: use `structured_title` / `structured_description` with `digital_source_type`; AI images need IPTC `DigitalSourceType` = `TrainedAlgorithmicMedia` (also [F] using-gen-ai-content page). CHANGED 2024-26.
- Policy page (https://support.google.com/merchants/answer/6149970): prohibited and restricted content, secure checkout (HTTPS for credentials/cards), no gimmicky text ("FREE" obfuscation). [F]
### 17.3 Mistakes
Price/availability in JSON-LD differing from page and feed; missing GTIN; one generic title for all variants; thin copied manufacturer descriptions (thin affiliation); faceted URL explosion (section 15); out-of-stock pages 404ing without strategy (use 200+OutOfStock for temporary, 404/410 or redirect for permanent) [B].
### 17.4 Analyst vs developer
- Analyst: feed diagnostics, margin/price competitiveness, category taxonomy, which templates carry Product markup.
- Developer: single product data source feeding page, JSON-LD and feed; return/shipping policy as structured data; sitemap with lastmod for product changes.
- MCP data: product-page JSON-LD parse, Merchant Center API (diagnostics, item status), price/availability consistency checks, crawl of category/facet URL space, SERP with shopping features.

---

## 18. Local SEO and Google Business Profile

### 18.1 Business Profile guidelines [F summary] https://support.google.com/business/answer/3038177
- Represent the business as it exists in the real world; no prohibited content.
- Name: real-world name used consistently on storefront, website and stationery; no taglines, phone numbers, hours or special characters without real-world proof.
- Address: precise physical location; **no P.O. boxes or virtual offices**; address shown should have permanent signage with the business name.
- Hours: customer-facing hours (some categories, such as hotels, schools, theatres, should not list hours).
- Categories: as few as possible for the core business.
- Description: no low-quality text, misspellings, links; focus on services, not promotions.
- Service-area businesses: one central profile with defined service area; roughly **no more than about two hours' driving** from the base.
- Google can suspend profiles that break the rules.
### 18.2 Website side
- LocalBusiness structured data (section 12.4) with NAP identical to the profile; location pages must be unique, not doorway pages (doorway abuse in spam policies); Google lists keeping Business Profile and Merchant Center current as an AI-features best practice [F] ai-features page.
- [B] Ranking factors for local pack per Google: relevance, distance, prominence; reviews, responses and photos affect prominence. Review gating/fake reviews violate policy.
### 18.3 Italy-specific notes (for skills) [B]
Opening hours and special hours (festività), "Partita IVA" does not need to be public in the profile, SAB for artigiani/idraulici common; verify via postcard/video; NAP consistency across directories (PagineGialle etc.).
### 18.4 Mistakes
Keyword-stuffed business name; virtual-office addresses; multiple profiles for one location; mismatch between website and profile NAP; city doorway pages.
### 18.5 MCP data
Business Profile API (locations, categories, hours, attributes, reviews, insights) with OAuth; LocalBusiness parse from website; SERP local pack and map rank grids; citation consistency data.

---

## 19. Search Quality Rater Guidelines (version dated 2025-09-11)

Source: https://static.googleusercontent.com/media/guidelines.raterhub.com/en//searchqualityevaluatorguidelines.pdf (PDF converted locally; cover reads "General Guidelines, September 11, 2025"). [F]
Important framing: raters do not directly set rankings; the guidelines show what Google wants its systems to approximate. They are an evaluation tool, not a checklist of ranking factors.

### 19.1 Structure (section numbers from the table of contents)
Part 1 Page Quality (sections 1-4 plus YMYL, MC/SC/Ads, website understanding); Part 2 Understanding user needs and queries; Part 3 Needs Met rating (section 13 onward: Fully Meets, Highly Meets, Moderately Meets, Slightly Meets, Fails to Meet).

### 19.2 E-E-A-T (section 3.4) [F]
- "The most important member at the center of the E-E-A-T family is Trust" (accurate, honest, safe, reliable).
- Experience: first-hand or life experience; Expertise: knowledge/skill; Authoritativeness: go-to source for the topic (official government page is an example). Required type and amount depends on page purpose and topic.
- Trust examples: shops need secure payment and reliable customer service; reviews must be honest and help buyers; clear YMYL pages must be accurate to prevent harm.
### 19.3 YMYL (section 2.3) [F]
- Topics that can significantly affect health, financial stability, safety, or society's welfare. Categories: **Health or Safety; Financial Security; Government, Civics & Society; Other**. CHANGED 2024-26: "Society" became "Government, Civics & Society" and now explicitly covers election and voting information and trust in public institutions (since the early-2025 revision; press says January 2025 [S]).
- YMYL is a spectrum; clear-YMYL pages need the most scrutiny. Examples used: heart-attack symptoms, how to invest, who can vote.
### 19.4 Page Quality scale and lowest-quality pages (section 4) [F]
- Scale: Lowest, Low, Medium, High, Highest. Lowest pages are untrustworthy, deceptive, harmful or highly undesirable.
- Section 4.6.5 scaled content abuse (matches web spam policy, rated **Lowest "no matter how they are created"**; also when suspected after sampling several pages). Examples listed: automated tools producing many low-value pages; scraping feeds/search results with synonymising or translation; stitching content from pages without adding value; multiple sites to hide scale; keyword-heavy nonsense pages.
- Section 4.6.6: Lowest is required when all or almost all main content is copied/paraphrased/embedded/reposted with little effort, originality or added value. "The use of Generative AI tools alone does not determine the level of effort or Page Quality rating": tools can serve high or low quality creation. CHANGED 2024-26 (generative AI language added in 2025 revisions).
- Fake author/creator profiles (including AI-generated personas) are called out as deceptive [F, lines near 4.x on "fake owner or content creator profiles"].
- High vs Highest: distinguished by quality of main content, reputation of site/creator and/or E-E-A-T (section 7.x text).
### 19.5 Needs Met (section 13) [F]
- Fully Meets: only for queries with a single clear target (navigational/specific result); most queries cannot have it. Highly Meets: very helpful for dominant/common/reasonable minor interpretations. Moderately Meets: helpful. Slightly Meets: less helpful or helpful only for an unlikely interpretation. Fails to Meet: completely fails for almost all users.
### 19.6 How a skill should use this
- Use QRG language to explain why a page is weak (purpose, MC quality, who is responsible, reputation, Trust) and to structure a content review. Do not call E-E-A-T a ranking factor; call it an evaluation framework (Starter Guide wording).
- Check pages for: who is responsible (About, Contact, customer service), author identity, evidence of first-hand experience, original value vs other results, honest ads/affiliate disclosure, YMYL accuracy.
- MCP data: crawl of About/Contact/author/legal pages, byline and schema presence, outbound citations, reviews/reputation signals (third-party), SERP for intent (Needs Met reasoning).
- Unverified: press claims of AI Overview examples in the Sept 2025 text were not found in my conversion; do not cite them.

---

## 20. AI Overviews, AI Mode and generative AI content

### 20.1 Google's position for site owners [F]
Sources: https://developers.google.com/search/docs/appearance/ai-features ; https://developers.google.com/search/docs/fundamentals/ai-optimization-guide (published as a blog announcement 2026-05-15 [S] https://developers.google.com/search/blog/2026/05/a-new-resource-for-optimizing). CHANGED 2025-26.
- "No additional requirements to appear in AI Overviews or AI Mode, nor other special optimizations necessary."
- Eligibility: be indexed and snippet-eligible.
- Mechanisms named: retrieval-augmented generation on core ranking systems, and **query fan-out** (multiple related searches run concurrently).
- Recommendations: unique, expert, non-commodity content; clear structure for people; good images/video; crawlable, JS-friendly, semantic HTML; page experience; reduce duplicates; keep Merchant Center feeds and Business Profile accurate; structured data must match visible content.
- Myths to reject: llms.txt and "AI text files", special AI markup, chunking content, rewriting for AI, inauthentic brand mentions, over-focus on schema. Warns against third-party tools claiming internal Google metrics.
- Controls: `nosnippet`, `data-nosnippet`, `max-snippet`, `noindex` apply to AI features as well. Google-Extended is a separate robots token controlling use for Gemini training/grounding, not Search AI Overviews [B]; verify.
- Emerging: agentic experiences and protocols (Universal Commerce Protocol mentioned) [F].
- GEO consequence: for the plugin, GEO = (1) classic SEO hygiene, (2) differentiated source content, (3) entity/brand clarity, (4) feeds (Merchant, GBP), (5) measuring AI visibility via GSC plus third-party SERP/LLM sampling labelled as sampling. Non-Google engines (ChatGPT, Perplexity) are outside this file.

### 20.2 Measurement
- Search Console Performance report: AI Overviews/AI Mode traffic is counted under the Web search type (clicks, impressions, position as normal) [F ai-features page]. Performance report filters search type (web, multimodal, image, video, news) and groups by query, page, country, device, appearance, date [F]. https://support.google.com/webmasters/answer/7576553
- **Search Generative AI performance reports** (announced 2026-06-03; for Search and Discover): impressions, pages, countries, devices, dates; no clicks or CTR; initially subset of sites, all properties by 2026-08-12 per press. [S] https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports ; CHANGED 2026. Verify availability via the API before promising it in a tool.

### 20.3 Generative AI content guidance [F]
Source: https://developers.google.com/search/docs/fundamentals/using-gen-ai-content
- Allowed, if it meets Search Essentials and does not become scaled content abuse. Models predict word sequences and hallucinate: fact-check and review everything, including titles, descriptions, structured data and alt text. Consider telling readers about the production process. E-commerce: Merchant Center labelling (section 17.2). Raters: QRG 4.6.5-4.6.6 (section 19.4).
### 20.4 Skill implications
- Never recommend AI-bulk page generation (programmatic pages) without per-page unique data and human QA; flag as scaled-content risk.
- Provide a "human-added value" checklist: first-hand data, screenshots, original numbers, named expert review, update date.

---

## 21. Checklists per role (distilled)

### 21.1 Indexability triage (analyst first-hour)
1. Is the page 200, not redirected, not blocked by robots, not noindex (raw and rendered)?
2. Declared canonical = self (or intended)? Google-selected canonical agrees?
3. Mobile version has same content, meta, structured data?
4. Linked by crawlable `<a href>` from indexed pages; in sitemap with accurate lastmod?
5. GSC Page indexing status: if "Crawled/Discovered - not indexed" => content quality/duplication/crawl demand, not a "submit again" problem.
6. Snippet controls not accidentally restrictive (`nosnippet`, `max-snippet:0`)?
### 21.2 Technical build checklist (developer)
- Server: 200/301/404/410 correct, no soft 404, no chains over a couple of hops (hard limit 10), HTTPS, HSTS.
- robots.txt under 500 KiB, tested; sitemap(s) under 50 MB / 50,000 URLs each, sitemap index, UTF-8, absolute URLs; `priority`/`changefreq` ignored. [F] https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- Sitemap lastmod only when main content/structured data/links change (copyright-date changes don't count). [F]
- HTML under the first 2 MB for crawling (inline CSS/JS counts).
- CWV targets met at p75 for the main templates.
- JSON-LD from server data; validated.
### 21.3 Content/quality review (analyst)
Who/How/Why answered; unique value vs top 10; E-E-A-T proof (experience, credentials); no scaled templated sets; update dates honest; ads/affiliate disclosed; intent match (Needs Met reasoning).
### 21.4 Migration checklist
Section 14.

---

## 22. Data an MCP server must expose, by verification need

| Need | Data/tool | Source of truth |
|---|---|---|
| Crawlability/indexability | Fetch with status, redirect chain, headers, robots.txt allow/deny for Googlebot, meta robots | Own crawler |
| Rendering | Raw HTML vs rendered DOM, console/network errors, blocked resources, screenshot | Headless Chrome |
| Canonical/dup | Declared vs Google-selected canonical, sitemap membership, internal-link targets | Crawler + URL Inspection API |
| Index state | Page indexing verdict, last crawl time, rich results verdict | URL Inspection API (quota per property) |
| Performance in Search | Queries, pages, devices, countries, search type, dates (16 months, row limits) | Search Console API |
| AI features | Generative AI performance report (impressions only) | Search Console (check API availability) |
| Page experience | CrUX p75 LCP/INP/CLS/TTFB by URL/origin, phone/desktop; lab Lighthouse with LCP subparts | CrUX API, PSI API |
| Structured data | Parsed JSON-LD/microdata with required/recommended property check; visible-content match | Own parser |
| SERP | Organic features, titles/snippets as displayed, AI Overview presence/citations, local pack, shopping | SERP provider |
| Local | Business Profile data, reviews, local rank grids | GBP API, SERP provider |
| E-commerce | Merchant Center item status/diagnostics, price/availability consistency | Merchant API |
| International | hreflang graph with reciprocity and target validity | Crawler |
| Links | Backlink profile, anchors, spam signals | Third-party |
| Updates | Core/spam update calendar | https://status.search.google.com/ |

Design constraints for the tools: GSC and CrUX are per-site-owner OAuth/API-key data (private data stays inside EU per constitution VII); return raw numbers plus the Google threshold used; always carry an `as_of`/source field; label sampled third-party data as such; no vendor names in tool descriptions (constitution V) except Google services the user connects.

---

## 23. Change log 2024-2026 relevant to the plugin

| When | Change | Grade / source |
|---|---|---|
| Mar 2024 | INP replaces FID as Core Web Vital | [F] web.dev vitals |
| Mar 2024 | Scaled content abuse, site reputation abuse, expired domain abuse policies added; core update merged helpful content system | [B]/[F] spam policies |
| 2024 | Mobile-first indexing rollout completed | [B] |
| Oct 2024 | Sitelinks search box removed | [S] |
| Jan 2025 | QRG: YMYL "Society" becomes "Government, Civics & Society"; generative AI and spam definitions added | [S] seo-kreativ, QRG text [F] |
| May 2025 | AI Overviews/AI Mode guidance page; Google's "Top ways" AI-experience blog | [F]/[S] |
| Jun 12 2025 | Seven structured data types phased out | [S] |
| Sep 11 2025 | Current QRG version | [F] |
| Nov 2025 | Further SERP simplification update | [S] (body unread) |
| Dec 10 2025 | SEO Starter Guide refreshed | [F] |
| Jan 2026 | Search Console and API drop support for removed structured data types | [S] |
| Feb 2026 | Discover core update | [S] |
| Mar 27-Apr 8 2026 | March core update (spam update days earlier) | [S] status dashboard |
| May 15 2026 | Google publishes "Optimizing for generative AI search" guide | [S]/[F] |
| May 21 2026 | May core update | [S] |
| May-Jun 2026 | FAQ rich result feature and docs removed | [F summary]/[S], dates inconsistent |
| Jun 3 2026 | Search Generative AI performance reports announced | [S] |
| Jun 2026 | June spam update | [S] |
| Aug 2026 | Spam policies page updated 2026-08-28 (EEA treatment of site reputation abuse); August spam update 18-21 Aug (not links, not SRA); generative AI report on all properties 12 Aug | [F]/[S] |
| 2 MB crawl limit | Googlebot page states first 2 MB of supported files (older 15 MB lore) | [F] googlebot page |
| 31 Jan 2027 | Merchant Center min image 500x500 px | [F] |

---

## 24. Source index (all accessed 2026-10-02)

Google Search Central
- SEO Starter Guide: https://developers.google.com/search/docs/fundamentals/seo-starter-guide
- How Search works: https://developers.google.com/search/docs/fundamentals/how-search-works
- Search Essentials: https://developers.google.com/search/docs/essentials
- Spam policies: https://developers.google.com/search/docs/essentials/spam-policies
- Helpful content: https://developers.google.com/search/docs/fundamentals/creating-helpful-content
- Generative AI content: https://developers.google.com/search/docs/fundamentals/using-gen-ai-content
- AI features: https://developers.google.com/search/docs/appearance/ai-features
- Optimizing for generative AI search: https://developers.google.com/search/docs/fundamentals/ai-optimization-guide
- Core updates: https://developers.google.com/search/docs/appearance/core-updates
- Page experience: https://developers.google.com/search/docs/appearance/page-experience
- robots.txt intro: https://developers.google.com/search/docs/crawling-indexing/robots/intro
- Robots meta tag: https://developers.google.com/search/docs/crawling-indexing/robots-meta-tag
- Googlebot: https://developers.google.com/search/docs/crawling-indexing/googlebot
- HTTP status handling: https://developers.google.com/search/docs/crawling-indexing/http-network-errors
- Crawl budget: https://developers.google.com/search/docs/crawling-indexing/large-site-managing-crawl-budget
- Sitemaps: https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- Canonicalization: https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls
- JavaScript SEO: https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics
- Mobile-first: https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing
- Faceted navigation: https://developers.google.com/search/docs/crawling-indexing/crawling-managing-faceted-navigation
- Pagination: https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading
- Site move: https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes
- hreflang: https://developers.google.com/search/docs/specialty/international/localized-versions
- Title links: https://developers.google.com/search/docs/appearance/title-link
- Images: https://developers.google.com/search/docs/appearance/google-images
- Video: https://developers.google.com/search/docs/appearance/video
- Discover: https://developers.google.com/search/docs/appearance/google-discover
- Structured data policies: https://developers.google.com/search/docs/appearance/structured-data/sd-policies
- Search gallery: https://developers.google.com/search/docs/appearance/structured-data/search-gallery
- Product / LocalBusiness / Article / FAQ: https://developers.google.com/search/docs/appearance/structured-data/{product,local-business,article,faqpage}
- E-commerce intro: https://developers.google.com/search/docs/specialty/ecommerce/introduction-to-structured-data-on-ecommerce-sites
- Blog: https://developers.google.com/search/blog/2025/06/simplifying-search-results ; /2025/11/update-on-our-efforts ; /2026/05/a-new-resource-for-optimizing ; /2026/06/gen-ai-performance-reports ; /2023/08/howto-faq-changes
- Search Status Dashboard: https://status.search.google.com/
- Performance report help: https://support.google.com/webmasters/answer/7576553
web.dev
- Core Web Vitals: https://web.dev/articles/vitals ; LCP: https://web.dev/articles/optimize-lcp ; INP: https://web.dev/articles/optimize-inp ; CLS: https://web.dev/articles/optimize-cls ; TTFB: https://web.dev/articles/ttfb
Google support
- Business Profile guidelines: https://support.google.com/business/answer/3038177
- Merchant Center product data spec: https://support.google.com/merchants/answer/7052112 ; Shopping policies: https://support.google.com/merchants/answer/6149970
Rater Guidelines
- https://static.googleusercontent.com/media/guidelines.raterhub.com/en//searchqualityevaluatorguidelines.pdf (2025-09-11)
Secondary (only for dates/context, [S])
- https://www.relevantaudience.com/seo/google-august-2026-spam-update/ ; https://www.searchenginejournal.com/google-confirms-march-2026-core-update-is-complete/ ; https://www.techwyse.com/news/ai-search/google-may-2026-core-update ; https://ppc.land/google-kills-faq-rich-results-what-seos-saw-coming-since-2019/

---

## 25. Open verification items for the next pass

1. Read the Nov 2025 simplification post in full and list exactly which features were affected.
2. Confirm Googlebot 2 MB vs 15 MB (live page, plus Search Central blog), and exact robots.txt error handling (5xx/429/30-day rule).
3. Confirm the FAQ removal date; resolve the "remove markup" vs "no need to remove" wording.
4. Read the Search Generative AI performance report post and API availability (does the Search Console API expose it?).
5. Google News docs (correct URL), Merchant API/Content API sunset timeline, Business Profile API quotas.
6. Verify QRG for AI Overview examples (grep found none in the 2025-09-11 text) and whether a later version exists after 2025-09-11.
7. INP/LCP "Needs improvement" boundaries at exactly the threshold (inclusive vs exclusive) as used by CrUX API categories.
8. Italian-market specifics (Garante/consent impact on measurement, Italian local directories) need non-Google sources and are out of scope here.
