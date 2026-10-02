# seocli agent-skills

Claude Code marketplace **seocli**, with the plugin **`seocli-seo`**: SEO, GEO and paid-media
methodology for Claude Code, backed by the [seocli](https://github.com/seocli) MCP server.

The plugin holds instructions only (skills and, in later versions, sub-agent personas). Every number
about a site, a SERP or an account comes from a seocli tool; the plugin never calls third-party SEO
or ads APIs and ships no scripts.

Status: version 0.3.0 contains the lead skill and the methodology. Specialist personas, the strategy
round-table and the SEO flows arrive as the seocli server gains the matching tools; until then the
plugin says plainly which capabilities are "not available on your seocli server yet".

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

## What is inside (0.3.0)

| Skill | Role |
|---|---|
| `/seocli-seo:seo` | The lead: session check, available tools, credits, price list, clients, operation status, routing and the effort ladder |
| `methodology` | Four phases (PERCEIVE, ANALYZE, VALIDATE, ACT), falsifiable recommendations with four fields, a 0-8 rubric, provenance labels (not a command) |
| `seocli-tools` | The server contract: tools, prices, `max_credits`, errors, operations, workspace (not a command) |

## Planned personas

Eleven specialists, each in an isolated context with a narrow remit and read-only tools: moderator,
skeptic, pragmatist, marketing-strategist, seo-analyst, seo-developer, content-strategist,
geo-analyst, geo-developer, google-ads-manager, meta-ads-manager. Only the lead calls seocli tools;
personas read an evidence pack and return findings and recommendations with an output contract. The
design is in `docs/DESIGN.md`.

## Credits and costs

- Seocli tools that do work (crawls, checks, research) spend **credits** from your seocli account.
  The plugin shows an estimate before every paid call, asks for an explicit yes above a threshold
  (option `confirm_above_credits`, default 500), passes a `max_credits` ceiling equal to the approved
  estimate and never retries a paid call on its own. The server enforces the ceiling.
- Today's tools (status, clients, balance, price list, operation status) are free.
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

The lint (Node, no dependencies) checks manifests, size limits, frontmatter, section structure,
persona permissions, pinned sentences, dead references, the tool-name contract
(`tests/contract/tools.json`), vendor names, claim tags and the kill list.

## Licence

[MIT](./LICENSE). See [NOTICE](./NOTICE) for attributions.
