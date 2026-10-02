# Deviations from DESIGN.md

Recorded when the build departs from `docs/DESIGN.md`. Newest last.

1. **`.mcp.json` URL (section 8.3).** The design shows `https://<production-host>/mcp`. The build uses
   `${SEOCLI_MCP_URL:-http://127.0.0.1:8080/mcp}`, which Claude Code documents as supported
   (`${VAR:-default}` expansion in MCP server configuration, including plugin `.mcp.json`). No
   production host exists yet; at launch (M10) the default becomes the production URL and the local
   address moves to the environment variable. The separate `.mcp.dev.json` is therefore not needed.
2. **`planned_commands` in `tests/contract/tools.json`.** Not in the design. The hub names commands that
   do not exist yet (`audit`, `page`, ...); the dead-reference check (9.1 #7) accepts exactly this
   list and fails if a command is both implemented and listed. Remove entries as commands ship.
3. **No CI workflow.** Section 8.1 lists `.github/workflows/plugin.yml`; the product owner ruled out
   GitHub workflows for now. The lint runs in the `pre-commit` hook instead.
4. **`contract/refresh.sh` not created in M0.** It needs the server's `tools/list` over authenticated
   MCP; until the server answers with a static development token, `tools.json` is maintained by hand.
5. **Knowledge-skill section "Claim tags".** `methodology` defines the claim tags in its own section
   rather than under `## Rules`, because lint check 10 forbids the `[U]` tag inside `## Rules`.
6. **`ads-audit` without user-supplied exports (D3).** The hub already rules out pasted exports, so
   `ads-audit` is checklist-and-questions only: the user states answers from the platform screens, they
   are recorded as `user_supplied` qualitative facts and never summed or scored. No euro weighting
   unless the user states the amounts.
7. **Persona and lint additions.** `tests/lint.mjs` now also checks that the 11 expected agent files
   exist, that `model` is opus/sonnet/haiku, that `maxTurns` is numeric, and that backticked
   persona-like names (`*-analyst`, `*-developer`, `*-strategist`, `*-manager`) refer to an existing
   agent or skill (the "agent name that does not exist" part of 9.1 #7).
8. **`strategy` reference files.** Four files (`casting`, `schemas`, `weaving`, `verdict`) instead of the
   design's `protocol`; the protocol itself is the Steps section of `SKILL.md`, the round schemas and
   the dispatch brief are in `schemas.md`.
9. **`page` Lighthouse invocation.** Design 5.6 runs `npx lighthouse`; the build uses only a Lighthouse
   the user already installed (`command -v lighthouse` or `npx --no-install`), so `npx` never downloads
   anything, and only against localhost.
10. **Moderator and skeptic write nothing.** They return text and the lead writes `woven.md` and
    `verdict.md`, because persona tools are Read/Grep/Glob only.
11. **`planned_commands`** now `audit`, `keywords`, `geo`, `report` (`page`, `ads-audit`, `strategy` ship).
12. **AskUserQuestion everywhere (product-owner rule, section 5.10).** Not in the original design, which
    allowed "AskUserQuestion or a numbered list". Now AskUserQuestion is the rule for every decision
    point, with the numbered list only as fallback. `allowed-tools: AskUserQuestion` is added to the four
    command skills; lint requires it plus the fallback. Personas cannot use the tool (not available in
    sub-agents), so a pinned `ASK` sentence and a `QUESTIONS` field in the output contract were added.
13. **Client web fetch for site understanding (product-owner rule, section 5.11).** Sections 1.2 and 8.5
    treat third-party web text as a source the plugin does not use. Exception: the lead (never a persona)
    may fetch a bounded set of public pages of the client's own site with the host's web-fetch tool
    (`WebFetch`) to infer what the business is. The text is untrusted, the step is free, it yields no SEO
    metrics and does not replace seocli data. New knowledge skill `site-profile`; `WebFetch` added to
    `allowed-tools` of the four command skills (Claude Code documents that field as a pre-approval, not a
    restriction); personas keep `tools: Read, Grep, Glob`. Lint now requires both on command skills.
