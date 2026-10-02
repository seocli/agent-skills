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
