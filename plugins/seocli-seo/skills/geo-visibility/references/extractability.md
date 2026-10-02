last_verified: 2026-10-02
volatile: false

# Extractability checklist for the GEO developer

Hygiene with independent justification. Nothing here is shown to lift AI citations alone (see
`tactics-evidence.md`); eligibility (items 1-2) is the part Google and the other vendors document.

1. **Server-rendered primary content.** Test with a raw fetch and with JavaScript off; use the User-Agent of each search bot (`bot-access-matrix.md`). Content that exists only after client rendering may be invisible to AI fetchers; vendors do not document their JS handling, so treat it as a risk and measure. Rendering mechanics: `technical-seo`.
2. **Indexable and snippet-eligible:** no `noindex`, no `nosnippet`, no `max-snippet:0` on pages meant to be cited (S1).
3. **Semantic HTML:** one `h1`, descriptive `h2`/`h3`, real `table` with `th` for comparisons, `ol` for steps, `time datetime` for dates, descriptive `alt`. No text inside images, no key facts only in PDFs.
4. **Passage design:** each section self-contained (restate the subject), answer first, then evidence; numbers with unit, date and source in the same sentence. Do not fragment pages artificially.
5. **Visible author, publication date and honest "updated" note;** `Last-Modified`, sitemap `lastmod` and visible dates move only on real content changes.
6. **Structured data** matching visible content, for rich results and entity clarity (Organization with `sameAs`, Article, Product only with real data). Not a GEO lever.
7. **robots.txt and WAF:** explicit per-bot groups; verified bot IPs allowed; no CAPTCHA on public content.
8. **Feeds and profiles:** keep Merchant Center feeds and Business Profile current when relevant (S2).
9. **Referral tracking:** watch referrers from AI assistants (chatgpt.com, perplexity.ai, claude.ai, gemini.google.com, copilot.microsoft.com); expect undercount.
10. **Optional:** an AI text file for documentation sites read by coding agents, generated from the sitemap or CMS so it does not rot. Markdown alternates only for such sites, canonicalised to the HTML page.

Private content needs authentication; robots.txt does not stop user-triggered fetchers.

## Sources

- S1, S2, S5, S6, S7 in `sources.md`, accessed 2026-10-02. Checklist items 3-5, 9, 10 are the plugin authors' engineering practice `[H]`.
