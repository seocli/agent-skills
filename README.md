# seocli agent-skills

Claude Code marketplace **seocli**, with the plugin **`seocli-seo`**: SEO, GEO and paid-media
methodology for Claude Code, backed by the [seocli](https://github.com/seocli) MCP server.

The plugin holds instructions only (skills and sub-agent personas). Every number about a site, a SERP
or an account comes from a seocli tool; the plugin never calls third-party SEO or ads APIs and ships no
scripts.

Status: version 0.4.0 has five commands, twelve knowledge skills and twelve specialist personas. A
site-wide audit, keyword research and branded reports are not available yet: the plugin says plainly
"not available on the seocli server yet" and never fills the gap with web searches, memory or invented
numbers.

## Install

```
/plugin marketplace add seocli/agent-skills
/plugin install seocli-seo@seocli
```

Then run `/seocli-seo:seo` to check the connection, your credits and your clients.

## How it connects to seocli

The plugin declares one remote MCP server, `seocli` (`plugins/seocli-seo/.mcp.json`), over HTTP with
OAuth. There is no token to paste: in Claude Code run `/mcp`, select `seocli` and sign in in the
browser. The server address is `${SEOCLI_MCP_URL:-http://127.0.0.1:8080/mcp}`: set the environment
variable `SEOCLI_MCP_URL` before starting Claude Code to point at another server; without it the
plugin uses a local server on port 8080. The production address will become the default at launch.

## What is inside (0.4.0)

| Command | Role |
|---|---|
| `/seocli-seo:seo [client or domain] [goal]` | The lead: session check, credits, price list, clients, operation status and routing. Runs the SERP, AI-answer (GEO), Google Shopping, Search Console and GA4 checks, history, summary and monitors, with an estimate before every paid call |
| `/seocli-seo:page <file or localhost-url>` | Developer review of one page in your repository (meta tags, headings, canonical, robots, JSON-LD, links, rendering); patches applied only after approval; no credits |
| `/seocli-seo:strategy "<decision>"` | Strategy round-table: specialists argue one decision from the same evidence pack and a moderator drafts a falsifiable decision record for you to accept; started only by you; no credits |
| `/seocli-seo:ads-audit <google or merchant or meta>` | Paid-campaign audit as a checklist with targeted questions; judges only the answers you give (no account data yet) |
| `/seocli-seo:keywords <seed, url, domain or client>` | Keyword shortlist with one extra-clicks score, queries close to page one, competitor gaps, topic groups; difficulty, competitor and SERP calls cost credits (estimate first) |
| `/seocli-seo:kb build or update or show or list [sector]` | Sector knowledge base (below); no credits |

Knowledge skills, loaded by the lead and the personas (not commands): `methodology`, `seocli-tools`,
`site-profile`, `domain-kb`, `technical-seo`, `search-analytics`, `content-quality`, `local-seo`,
`geo-visibility`, `google-ads`, `meta-ads`, `marketing-strategy`.

Not available yet: a site-wide audit (`/seocli-seo:audit`, needs the crawl tools), a dedicated AI-visibility flow (`/seocli-seo:geo`; today the lead runs the
AI-answer checks), branded reports (`/seocli-seo:report`) and the server-side HTML check of a page.

## Specialists

Twelve personas, each in an isolated context with a narrow remit and read-only tools (the domain
researcher also searches the web and writes knowledge base drafts): moderator, skeptic, pragmatist,
marketing-strategist, seo-analyst, seo-developer, content-strategist, geo-analyst, geo-developer,
google-ads-manager, meta-ads-manager, domain-researcher. Only the lead calls seocli tools; personas read
an evidence pack and return findings and recommendations with an output contract. The design is in
`docs/DESIGN.md`.

## Domain knowledge base

`/seocli-seo:kb build|update|show|list [sector]` researches the public web about your client's sector
(glossary, entities, calendar, regulations, audience questions, sources, topic map). When the seocli
server has the `manage_kb` tool, the knowledge base lives on seocli, private to your account and the same
in claude.ai, Claude Desktop and Claude Code; writing it needs the Pro plan (during the beta every active
account can write). Without the tool it stays in your home directory at `~/.seocli/kb/<sector-slug>/`; a
local knowledge base is imported to seocli once, after a question. It holds sector knowledge only, never
client data, costs 0 seocli credits (Claude tokens only; quick about 10 sources, deep about 30) and may
ask permission to write under `~/.seocli/`.

## Credits and costs

- Seocli tools that do work (SERP positions, AI answers, Google Shopping searches and offers, and the
  scheduled monitors of those checks) spend **credits** from your seocli account. The plugin shows an estimate before every paid
  call, asks for an explicit yes above a threshold (option `confirm_above_credits`, default 500), passes
  a `max_credits` ceiling equal to the approved estimate and never retries a paid call on its own. The
  server enforces the ceiling.
- Free tools: status, clients, balance, credit movements, price list, operation status, history,
  summary, the Google connection, Search Console and GA4 reads, indexing inspection and the knowledge
  base.
- Using sub-agents also consumes your Claude plan tokens; the plugin shows the two meters separately.
- Client data is stored in `seo-workspace/` in your project, with a `.gitignore` that excludes everything.

## Credits to claude-seo

The methodology structure (four phases, falsifiable recommendation fields), several checklists and
thresholds (technical, schema deprecations, SXO page-type fit, SERP-overlap clustering, drift
severities, Search Console gotchas, local SEO) are adapted from **claude-seo** by Agrici Daniel and
contributors (Florian Schmitz, Lutfiya Miller, Dan Colta, Matej Marjanovic, puneetindersingh),
https://github.com/AgriciDaniel/claude-seo, v2.4.1, MIT licence. Text was re-written for seocli's
data model and the Italian market; no code was copied.

## Development

```
git config core.hooksPath .githooks
node tests/lint.mjs
```

On every change to the plugin, raise `version` in `plugins/seocli-seo/.claude-plugin/plugin.json` and in
`.claude-plugin/marketplace.json` (semver): Claude Code updates an installed plugin only when its version
changes.

The lint (Node, no dependencies) checks manifests, size limits, frontmatter, section structure,
persona permissions, pinned sentences, dead references, the tool-name contract
(`tests/contract/tools.json`), vendor names, claim tags and the kill list.

## Licence

[MIT](./LICENSE). See [NOTICE](./NOTICE) for attributions.
