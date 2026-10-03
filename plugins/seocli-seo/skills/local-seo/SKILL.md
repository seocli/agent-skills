---
name: local-seo
description: Local SEO knowledge - Google Business Profile rules, name and address consistency, service-area businesses, review policy and review markup, location pages and the swap test, LocalBusiness structured data, and an audit model for Italian small businesses. Read on demand by the analyst and the content strategist; not a command.
user-invocable: false
---

# local-seo

## When to use

Load it for a business with a physical location or a service area: Business Profile questions, local
pack visibility, reviews, citations, location or "service in [town]" pages, local structured data. Use
`technical-seo` for crawling, canonicals and schema mechanics, `content-quality` for page quality and
spam policy, `search-analytics` for Search Console data. Tags: `[G]` Google primary source (id in
`references/*.md` Sources, accessed 2026-10-02); `[H]` default in Heuristics.

## Rules

1. **Local results rest on relevance, distance and prominence.** There is no way to request or pay for a
   better local ranking. [G:local-ranking]
2. **Prominence** draws on review count and ratings and on links and citations that point to the business;
   Google advises verifying the profile, keeping it complete and current, replying to reviews and adding
   photos and videos. [G:local-ranking]
3. **Name** is the real-world name used consistently on storefront, website and stationery. Do not add
   taglines, store codes, capitalised words, phone numbers, URLs or location text. [G:gbp-guidelines]
4. **Address** is precise and real. P.O. boxes and virtual offices are not acceptable, and a shown address
   should have permanent signage with the business name. [G:gbp-guidelines]
5. **Service-area businesses** should not claim an area beyond about two hours of driving from their base
   and should hide the address when they work from a residence. [G:gbp-guidelines]
6. **Hours** are regular customer-facing hours. **Categories**: as few as possible, the most specific ones.
   **Description**: services, products, mission and history; no promotions, links or low-quality text.
   [G:gbp-guidelines]
7. **Google may suspend a profile** that breaks the guidelines. [G:gbp-guidelines]
8. **Reviews policy.** Do not offer incentives (payment, discounts, free goods or services), do not
   discourage negative reviews or solicit only positive ones, do not pressure customers to review while on
   the premises. Encouraging genuine reviews without incentives is allowed. [G:maps-policy]
9. **Review markup.** If the reviewed business controls the reviews about itself, the page gets no star
   results. Reviews must be visible on the page; no fake or undisclosed incentivised reviews; do not
   aggregate reviews from other sites; several reviews need an aggregate rating. [G:review-snippet]
10. **LocalBusiness markup.** Required: `name` and `address` (postal address). `geo` with at least 5
    decimals, `priceRange` shorter than 100 characters, `url` a working link to the specific location,
    the most specific subtype. Display is not guaranteed. [G:local-business]
11. **Doorway abuse** includes multiple pages or domains aimed at specific regions or cities that funnel
    users to one page, and pages made to rank for similar queries that lead to less useful intermediate
    pages. [G:spam-policies]
12. **Local business** is a documented search feature (knowledge panel details such as hours, ratings,
    directions, booking). [G:gallery]

## Heuristics

- **Swap test** for location pages `[H]`: replace the town name; if the page is still coherent, it is a
  doorway page. A location page needs a real address or real service evidence, local proof (team, photos,
  jobs done, testimonials) and mostly unique copy (aim above 60%).
- Warn at 30 location pages and stop at 50 without a written justification `[H]`.
- The same name, address and phone on the site, in markup and on the profile; mismatch severity: name
  Critical, address High, phone Medium `[H]`.
- Classify the business first: shop, service-area business or hybrid; a service-area business skips map
  and physical-address checks `[H]`.
- Review rhythm beats a one-off burst: steady monthly reviews, owner replies to all `[H]`.
- Audit model, evidence labels and limits: `references/audit-model.md`. Data requests (lead executes):
  `seocli:manage_google` (E7.1) for profile data, `seocli:check_serp` for the local pack.
- Say what seocli cannot verify: true map position per point, verification status, profile insights `[H]`.

## Do not recommend

- A virtual office or P.O. box as the address; keyword text in the business name.
- Several profiles for one location; buying, trading or incentivising reviews; selective review requests.
- Review markup written by the business about itself.
- City-by-city doorway pages with only the town name swapped.
- A dedicated page per neighbouring town without distinct local content, for a service-area business.

## Italian market notes

- Italian directories to check for consistency `[H]`: PagineGialle, Tripadvisor, TheFork, MioDottore,
  Immobiliare.it, plus Camera di Commercio listings; keep the exact same name, address and phone.
- The classic doorway pattern is "idraulico a [comune]" repeated for every nearby municipality: use one
  strong service page plus a few pages that carry real local evidence.
- Italian trade law expects company identifiers (ragione sociale, partita IVA, sede) on the site; show
  them in the footer and in markup where they exist, and tell the client to verify with a legal advisor `[H]`.
- Craftspeople (idraulico, elettricista, fabbro) are usually service-area businesses: hide the home address.
- Set special hours for Ferragosto, holidays and local patron-saint days; closed days hurt trust `[H]`.
- Reply to reviews in Italian, with a formal "Lei" register for professional services `[H]`.

## References

- `references/business-profile.md`: profile rules, verification, reviews and sources; read on demand.
- `references/location-pages-schema.md`: location pages, swap test, LocalBusiness markup for Italy; read on demand.
- `references/audit-model.md`: weighted local audit model, evidence labels, reported items; read on demand.
