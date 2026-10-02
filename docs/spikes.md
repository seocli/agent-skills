# Spikes (DESIGN section 10, milestone M0)

Status 2026-10-02: **not run**. A non-interactive nested session with `--permission-mode
bypassPermissions` was refused by the harness of the build session, so the procedures are recorded
for a human to run. T1 and T2 are dropped (personas carry no MCP tools). Run each from a scratch
plugin outside the repository, with `claude -p --plugin-dir <scratch> --model haiku
--no-session-persistence --max-budget-usd 0.6 --output-format json`, and record the outcome below.

Scratch plugin for T8 and T4 (copy as is):

```
spike/.claude-plugin/plugin.json      {"name":"spike","version":"0.0.1","description":"spike"}
spike/skills/know/SKILL.md            frontmatter: name: know, description: ..., user-invocable: false
                                      body: "The secret codeword is PELICAN-7421."
spike/agents/probe.md                 tools: Read, Grep, Glob; model: haiku; maxTurns: 3; skills: [know]
                                      body: "Answer in one line from what you were preloaded with. Do not read files."
```

## T8. Scoped names and preload of `user-invocable: false` skills

Verify: (a) the sub-agent type is addressable as `spike:probe`; (b) `skills: [know]` (plain name)
resolves inside the same plugin; (c) the preloaded text reaches the sub-agent although the skill is
hidden from the `/` menu (ask `probe` for the codeword; expect PELICAN-7421); (d) the skill is still
model-invocable by the main thread via the Skill tool; (e) `/spike:know` is absent from the menu.
If (b) fails, try `spike:know` and update every `skills:` entry in DESIGN 8.4 and lint check 3.

Result: TODO.

## T4. Preload cost

Verify: token usage of one `probe` spawn with 0, 1, 2 and 3 preloaded knowledge skills of known size
(`usage` in the JSON output of the parent run, or the `/context` view). Record tokens per skill kB and
the fixed spawn overhead. Needed to confirm the 20-40k per spawn planning figure and the 6k combined
preload limit in lint check 2.

Result: TODO.

## T3. Resume by id

Verify: after the main thread spawns `probe` (no `name`), it can continue the same sub-agent by the
returned agent id with SendMessage, and the resumed agent keeps its earlier reasoning. Record whether
the resume costs less than a fresh spawn with the first answer attached, and whether a named agent
becomes a teammate while `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` is set (DESIGN 6.13). Decides how
party-mode round 2 is implemented.

Result: TODO.

## Also to confirm on a real build

- `${SEOCLI_MCP_URL:-http://127.0.0.1:8080/mcp}` expands in the plugin `.mcp.json` (documented by
  Claude Code; confirm with `claude mcp list` after `/plugin install`, with and without the variable).
- OAuth discovery against the local server (it answers 401 on `/mcp` without a token): `/mcp`
  authentication completes with Logto and CIMD (seocli-api story 1.2). If CIMD is refused, add
  `oauth.clientId` and `callbackPort` to `.mcp.json`.
- The tool name prefix in a real session is `mcp__plugin_seocli-seo_seocli__<tool>`.
