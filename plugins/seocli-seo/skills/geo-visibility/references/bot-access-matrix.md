last_verified: 2026-10-02
volatile: true

# AI bot access matrix

Three families per vendor: training, search or index, user-triggered. Re-verify every 60 days; vendors
rename bots. Source ids in `sources.md`.

| Vendor | Training | Search / index | User-triggered | Source |
|---|---|---|---|---|
| OpenAI | GPTBot (disallow to opt out) | OAI-SearchBot (allow to appear in ChatGPT search) | ChatGPT-User (robots.txt rules may not apply) | S5 `[G]` |
| Anthropic | ClaudeBot | Claude-SearchBot (blocking may reduce visibility) | Claude-User (blocking stops retrieval for user questions) | S7 `[G]` |
| Perplexity | none: PerplexityBot is not used for training | PerplexityBot | Perplexity-User (generally ignores robots.txt) | S6 `[G]` |
| Google | Google-Extended token (Gemini training; no effect on Search) | Googlebot (also feeds AI Overviews and AI Mode) | user-triggered fetchers (separate class) | S3, S4 `[G]` |
| Microsoft | none documented here | Bingbot (feeds Bing and Copilot) | n/d | `[U]` |

Reading the matrix:

- Blocking training bots does not remove a site from Google AI features or from the other vendors' search surfaces (settings are independent).
- Blocking a search bot removes the site from that engine's citations. Blocking Googlebot removes it from Search entirely.
- User-triggered fetchers may ignore robots.txt because a person asked for the page. Content that must stay private needs authentication, not robots.txt.
- Report training access and search citability as two separate findings, never merged.

## Pattern: visible in AI search, opted out of training

```
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
User-agent: Google-Extended
Disallow: /
```

A sample, not a default: each line is the client's business decision. Show the consequence of each.

## Checks

1. Parse robots.txt per bot token (a crawler obeys the most specific group naming it; a `*` group does not reach a named bot that has its own group). `[H]`
2. Fetch a key page with each bot's User-Agent: expect 200 and the same text as a browser; 403, 429 or a challenge page means WAF blocking.
3. Compare published IP lists (OpenAI, Perplexity, Anthropic JSON files) with server-log hits before trusting the User-Agent alone.
4. Logs: hits per bot in the last 7 days; zero hits from an allowed search bot is a stage-1 finding.
5. Do not block by IP alone: the bot then cannot read robots.txt (S7).
6. Bots from other vendors (Applebot-Extended, CCBot, Bytespider, Meta's agents) exist; their policy is out of scope until verified. `[U]`

Planned tools: `seocli:get_audit_issues` (E6.3) and `seocli:check_page_html`
(E6.7) may carry robots and rendering findings; none replaces a live fetch per bot. Never call a
planned tool until the session's tool list shows it.

## Sources

- S3, S4, S5, S6, S7 in `sources.md`, accessed 2026-10-02.
