# Multi-agent personas and party mode for the seocli plugin

Research note, 2026-10-02. Scope: how to split the `seocli-seo` Claude Code plugin across
PERSONAS (to fight context rot and raise precision, the BMAD way) and how to add a PARTY MODE
(sub-agents debate to define a strategy). Written for the plugin rebuild in
`agent-skills/plugins/seocli-seo`. Nothing here is implemented yet.

Sources read: local BMAD install in `seocli-api/.claude/skills/bmad-party-mode/`,
`bmad-agent-*`, `_bmad/`; BMAD-METHOD GitHub; Claude Code docs (sub-agents, agent-teams,
skills, plugins-reference, plugin-marketplaces, mcp, hooks, mods reference); Anthropic
engineering posts (multi-agent research system, effective context engineering); Agent Skills
best practices; claude-seo repo (brief only; a sibling note covers it in depth). Facts tagged
**[verify]** come from docs I could not test empirically and must be checked on a real build
before the design depends on them.

Contents

1. Executive summary
2. How BMAD defines personas
3. How BMAD party mode orchestrates them
4. BMAD: what works, what to avoid
5. Claude Code primitives and limits that matter
6. Emulating a round-table: three mechanisms compared
7. Cost model
8. Recommended architecture for seocli
9. File layout
10. Frontmatter and file examples
11. Personas calling seocli MCP tools and respecting credits
12. Party mode design (protocol, prompts, wrap-up, persistence)
13. Mods (plugin-authoring) opportunities
14. claude-seo comparison (brief)
15. Risks, open questions, test plan
16. Sources

---

## 1. Executive summary

- **BMAD persona = a small structured record** (name, title, icon, role, identity,
  communication_style, principles[], menu[], persistent_facts[], activation steps) stored in a
  `customize.toml`, rendered by a generic ~60-line `SKILL.md` activation template. The persona
  lives in the **main session** and is *embodied* until dismissed. It is a conversational mode,
  not a worker.
- **BMAD party mode = an orchestrator skill with four execution modes** (`session`, `auto`,
  `subagent`, `agent-team`). Default `session` is one model voicing all personas (cheap, but
  voices converge). The real anti-groupthink mechanism is `subagent` mode: independent context
  windows, parallel first takes, orchestrator weaves replies. Anti-consensus rooms explicitly
  recommend it.
- **Claude Code gives us three real primitives**: (a) plugin subagents (`agents/*.md`,
  isolated context, `tools` allowlist, `model`, `skills` preload, `memory`), (b) skills with
  `context: fork`, (c) experimental agent teams (peer messaging, env flag, interactive only).
  Subagents *can* message each other via `SendMessage` only when named and when they hold that
  tool, but by default they see nothing of each other: the orchestrator is the bus.
- **Recommended**: persona = plugin subagent (worker, one-shot, evidence-bound) plus an
  optional thin "talk to this persona" skill for interactive use; a **lead/orchestrator skill**
  in the main session owns user dialogue, credits and synthesis; shared knowledge lives in
  **non-user-invocable knowledge skills preloaded via `skills:`**; **party mode = a skill that
  runs a moderated, two-round, parallel-subagent round-table over a shared evidence pack on
  disk**, ending in a decision record. Agent teams are an optional later upgrade, not the
  foundation (experimental, higher cost, no resume).
- **Credits are the new constraint BMAD never had.** Rule: personas never spend credits
  autonomously. They return a *proposed call list with cost*; the lead approves (or asks the
  user) and executes, or hands the persona a pre-approved budget envelope. Back it with a
  PreToolUse hook and, authoritatively, the server-side quota (seocli-api already owns quota).
- **Biggest technical unknown**: whether background subagents can call plugin MCP tools
  (docs say background subagents keep a fixed built-in tool list and "remove all others";
  fork mode makes subagents background by default in interactive sessions). **[verify]**
  first, because the persona-calls-MCP design depends on it. Fallback design is given in 11.4.

---

## 2. How BMAD defines personas

### 2.1 Shape of a persona

A BMAD agent is a directory under `.claude/skills/bmad-agent-<role>/` with two files:

```
bmad-agent-pm/
  SKILL.md         # generic activation procedure, ~60 lines, identical across personas
  customize.toml   # the persona record (defaults, "DO NOT EDIT -- overwritten on update")
```

`SKILL.md` frontmatter is only `name` and a trigger-oriented `description`
("Product manager for PRD creation... Use when the user asks to talk to John or requests the
product manager"). The persona content is **not** in the SKILL.md; it is data in
`customize.toml` under `[agent]`:

| Field | Role | Example (John, PM) |
|---|---|---|
| `name`, `title` | hardcoded identity, "non-configurable skill frontmatter" | John / Product Manager |
| `icon` | prefix on every turn so the active voice is visible | clipboard emoji |
| `role` | the job in the method | "Translate product vision into a validated PRD, epics, and stories..." |
| `identity` | who they are / reference canon | "Thinks like Marty Cagan and Teresa Torres. Writes with Bezos's six-pager discipline." |
| `communication_style` | voice, as a simile | "Detective's 'why?' relentless. Direct, data-sharp, cuts through fluff." |
| `principles[]` | value system, 3-6 imperative lines | "PRDs emerge from user interviews, not template filling." |
| `persistent_facts[]` | session-long context: literals or `file:` globs | org rules, standards docs |
| `activation_steps_prepend/append[]` | hook points before/after greeting | pre-flight loads |
| `[[agent.menu]]` | capabilities: `code` + `description` + exactly one of `skill` or `prompt` | `PRD -> bmad-prd`, `CE -> bmad-create-epics-and-stories` |

Amelia (dev) shows the same record with a sharper style ("Ultra-succinct. Speaks in file paths
and AC IDs") and operational principles ("No task complete without passing tests",
"Red, green, refactor"). Notice that the principles are *behavioural constraints*, not
biography. The best lines are falsifiable: you can tell whether the persona obeyed them.

### 2.2 The activation template (the "persona engine")

The generic `SKILL.md` runs eight steps. The ones that matter for us:

1. **Resolve the agent block** with a script that merges three TOML layers in order
   base -> team (`_bmad/custom/<skill>.toml`) -> personal (`<skill>.user.toml`).
   Merge rules: scalars override, tables deep-merge, arrays-of-tables keyed by `code`/`id`
   replace-or-append, other arrays append. This is how users retune a persona without forking.
2. **Adopt the persona**: "Fully embody this persona... Do not break character until the user
   dismisses the persona. When the user calls a skill, this persona carries through."
3. **Load persistent facts** (`file:` globs become context).
4. **Load config** (user name, language, artifact folders).
5. **Greet** in the user's language, lead with the icon, mention `bmad-help`.
6. **Dispatch or present the menu**: if the opening message maps to a menu item, run it;
   otherwise render the menu as a table and **stop and wait**. Fuzzy match on number, code or
   description.

Properties worth stealing: persona record is **data**, behaviour template is **shared**;
override layering; menu items are *references to skills* (persona does not contain procedures,
it points at them); visible icon prefix; explicit "stay in character" clause; menu optional
(skip when intent is clear).

### 2.3 Persona roster in this repo

`_bmad/config.toml` registers five agents with one-line descriptors that double as casting
notes: Mary (analyst, "treasure hunter narrating the find"), John (PM, "detective
interrogating a cold case"), Sally (UX, "filmmaker pitching the scene"), Winston (architect,
"seasoned engineer at the whiteboard... trade-offs rather than verdicts"), Amelia (dev,
"terminal prompt"). Each descriptor is a **voice simile plus a decision bias**. That pair is
what makes voices distinguishable when hidden from labels.

### 2.4 Custom personas ("party members")

`bmad-party-mode/customize.toml` adds a second persona format used for ad-hoc and lens
personas: `code`, `name`, `icon`, `title`, `persona` (voice, humor, ethos, pet peeves, how they
argue), optional `capabilities` (what they can do when spawned as a real agent; "woven into
the spawn prompt as guidance, not a hard tool grant"), optional `model`. Shipped examples are
**lens personas**, each a critical angle: Vex (security, "names the exploit path concretely"),
Grumbal (adversary, "assumes the code is broken"), Boundary (edge cases), Yui (craftsman),
Dana (pragmatist who "counters the perfectionists so the room isn't a pile-on"), and four
anti-groupthink roles: Wildcard (option generator), Level (claim checker), Killjoy (loop
stopper), Splinter (consensus challenger).

Design lesson: the roster is built from **complementary tensions** (perfectionist vs
pragmatist, optimist vs adversary, divergent vs evidence-checker), not from job titles. A room
of five agreeable experts is useless; a room with an explicit counterweight for every bias is
a strategy tool.

### 2.5 Authoring guidance (`create-party.md`)

"The `persona` field is the whole game." A flat title gives a flat voice. Dimensions that earn
their place: identity, **voice and ethos**, **agenda** (what they push for in any
conversation), **quirks** (catchphrase, bias, blind spot), likes/dislikes (focus-group
personas), capabilities. Concrete test: "'Skeptical CFO' is a placeholder; 'won't approve
anything without a payback under 18 months, and says so in the first thirty seconds' is a
persona." Also: draft first, let the user react, do not interrogate; check `code` collisions
before writing (a custom code equal to an installed agent silently overrides it).

---

## 3. How BMAD party mode orchestrates multiple personas

### 3.1 Activation and roster

`bmad-party-mode/SKILL.md` (~70 lines + 4 reference files, ~130 lines total) loads in this
order: resolve workflow customization -> resolve core config -> **route intent** (create a
party vs run one) -> **`resolve_party.py`** returns the active roster (default group or all
installed agents), other group names (names only, "so nothing you aren't using is loaded"),
`party_mode`, `memory_enabled`, and any `scene`/`open_cast` -> memory read -> welcome (who is
in the room, one-line roles) -> ask the topic.

Concepts:
- **Collective/pool**: installed agents + custom members, deterministic keyed union.
- **Group (`party_groups`)**: curated room: `id`, `name`, `members[]` (codes) optional,
  `scene` (freeform stage direction), `memory` flag.
- **Scene**: a freeform paragraph setting the setting, tone and behavioural rules. Same
  members, different scenes (code review crew on duty vs off-duty). The "Anti-Consensus Club"
  scene encodes *process rules*: "Do not vote, declare consensus, or speak as if the room has
  authority", "If the room agrees too quickly, name the hidden assumption", and "strongly
  recommend `--mode subagent`... separate context windows make it less likely one shared
  context will make every voice agree".
- **Open cast**: no roster; the scene names a universe and the model casts per topic.

### 3.2 The four modes

| Mode | Mechanism | Strength | Weakness |
|---|---|---|---|
| `session` (default) | One mind voices every persona inline | Fast, cheap, lively banter | One context = one set of priors; voices drift to agreement |
| `auto` | Inline by default; spawn real agents only when "divergent, uncolored thinking is the value" (evaluation/critique/red-team, personas would plausibly disagree, user asked to dig in) | Cost-aware blend | Judgment call on every round; "when in doubt, voice" |
| `subagent` | A real agent behind each persona every substantive round | Independent thinking, true disagreement | Cost; parallel replies don't react to each other |
| `agent-team` | Persistent team that messages each other directly (Claude Code experimental) | Real back-and-forth | Point-to-point messaging only; no shared feed; lead must relay |

Fallback chain: `agent-team` -> `subagent` -> `session`, silently.

### 3.3 Subagent mode details (the interesting one)

- **Standing cast**: where the harness can keep agents alive, spawn one per persona and *reuse
  the handle* round after round so "grudges, alliances, and callbacks accrue". Visible
  roster mapping persona -> handle. Agents finished with a turn are **idle, not done**; only
  the user ends the party. Fallback: spawn fresh each round and re-brief.
- **One shared room**: "every standing member hears everything said each round", including
  turns of personas who sat out. The orchestrator routes the *whole exchange* to all of them,
  never "only the slice it's about to answer". Skipping this makes it "separate consultations
  wearing a party's clothes".
- **Spawn brief**: objective + persona + room-so-far (+ `capabilities` note, `model`,
  the group's `scene` as binding direction, and "check anything stale with web search").
  Trust their *thinking* (do not script do/don't checklists: "that's what produces lifeless
  blobs") but hold the *form*: a length cap (a sentence or three) and "react to what was just
  said rather than file a report".
- **Parallel vs sequential**: parallel for independent first takes; sequential when you want
  reactions to actual words. "Keep it to a few voices a round -- more reads as a crowd."
- **Weaving**: because parallel replies don't see each other, the orchestrator *reorders*
  turns so a rebuttal lands after what it rebuts, adds connective phrasing, and may let one
  persona pick up another's dropped thread. Hard rule: "Never change what an agent argued --
  weave delivery, preserve substance."
- **Model choice**: fast/cheap for banter, stronger for deep work; per-member `model`, session
  `--model` pin wins.

### 3.4 Agent-team mode details

Host, not weaver: kick off, keep turns short, pull the thread back, surface to the user. Roster
persona -> member; members are standing. Crucial operational note in the BMAD text:
"Messaging is point-to-point -- there's no shared feed, so a member that sat a round out hasn't
seen what passed while it was idle... catching an idle member up is the lead's job".

### 3.5 Turn-taking, conflict, tone ("Keep It Feeling Like a Party")

The bar BMAD sets (paraphrased as rules):
1. Reads like people talking: short turns, brevity default, "a persona goes long only when
   asked".
2. Every voice unmistakable; **unequal** voices (someone dominates, someone has a pet topic);
   vary the spotlight.
3. **They clash and you don't resolve it**. "Your instinct is to reconcile the voices and tie a
   bow -- resist it."
4. One exchange, woven, never softened; turns as `{icon} **{name}:**`; never paraphrase a
   persona in third person.
5. Pull the user into the room (they are a guest in the argument).
6. Make the collision earn its keep: the clash should surface an angle none of them had alone.
7. Let a history form (grudges, callbacks).
8. Commit to the fiction (no fourth wall about the mechanism).
9. When it sags, change something (new voice, name the impasse, ask where to go); never force.
   "Never work in a summary or takeaways -- they're there if the user asks."

### 3.6 Memory

Per-party append-only **memlog** at `{memory_dir}/<party>/.memlog.md`, typed entries
(`dynamic|moment|callback|outcome`, optional `--by persona`), written silently by a script
(`memlog.py append/init`). On entry a **reader subagent** distils the log into a few-hundred-
token brief so the file can grow without bloating the room. Entry test: "would this color a
future session, or make a callback land, or improve the party?" New faces seen in a session are
named in entries and offered for saving into the roster at wrap-up. Forget = delete the folder;
correct = append a superseding entry.

### 3.7 Wrap-up

Only when the *user* signals done (or `--non-interactive` served its intent): read back best
takeaways, top up memory, offer a keepsake (self-contained HTML), offer to save new faces, run
`on_complete`. "A served opening intent means *what's next?*, never *we're finished*."

---

## 4. BMAD: what works and what to avoid

### 4.1 Works (keep)

1. **Persona = data + shared engine.** Easy to override, diff, lint. Menu entries point to
   skills so the persona stays small and procedures stay reusable.
2. **Complementary-tension casting** and explicit counterweights (pragmatist vs perfectionist,
   claim-checker vs option-generator, loop-stopper).
3. **Independent context per voice when the point is divergence** (`subagent` mode, and the
   Anti-Consensus scene that *prescribes* it).
4. **Mode ladder with graceful degradation** (`agent-team -> subagent -> session`).
5. **Lazy roster resolution**: only load the active room; list others by name.
6. **Moderator discipline**: weave without altering substance; do not reconcile; do not
   summarize unless asked; change something when it sags.
7. **Distilled memory**: append-only log + reader that returns a short brief.
8. **Scenes as process rules.** Cheap place to encode "no voting", "name the hidden
   assumption".
9. **Visible voice**: icon prefix per turn.
10. **Right-sizing / progressive loading**: step files loaded just in time (code-review skill
    uses "micro-file design", one step in context at a time) - the same context-rot argument
    as Anthropic's.

### 4.2 Avoid (or adapt)

1. **Roleplay theatre as the goal.** BMAD party mode optimises for *fun and voice* ("lively",
   "banter", "stay in fiction", "no summary"). For SEO strategy we want *auditable decisions*.
   Keep voices distinct, but end with a structured decision record; BMAD deliberately omits it.
2. **`session` mode as default for decisions.** One mind voicing several personas gives
   correlated opinions. Default to real subagents for evaluative rounds; reserve inline voicing
   for banter and framing.
3. **Persona engine in the main session for everything.** Embodied-until-dismissed personas
   accumulate context in the main thread (that *is* context rot). Use them for short
   interactive consults; run heavy work in isolated subagents that return condensed output.
4. **Python/uv scripts for resolution** (`resolve_party.py`, `memlog.py`). Fine for BMAD,
   conflicts with user machines' variance; for a distributed plugin prefer static files plus
   plain Markdown instructions; use `bin/` scripts only if truly needed. (seocli constitution X
   also bans Python in the product; plugin is client-side, but staying script-free is simpler.)
5. **Three-layer TOML override stack** for a first release. Powerful, but YAGNI for a plugin;
   Claude Code already offers project/user scope overriding for agents and skills (a project
   `.claude/agents/auditor.md` shadows the plugin one by priority **[verify]**).
6. **Relay-only team messaging** with a lead that must catch everyone up each round: expensive
   and error-prone. If using teams, keep rounds few and have a shared file as the feed.
7. **Unbounded rounds.** "Runs until the user signals done" is right for a chat party, wrong
   for a metered product. Seocli party mode needs a **round budget and credit budget** declared
   up front.
8. **Free-form persona prose with no output contract.** In a worker role the persona must
   return a fixed, compact schema (findings, evidence ids, confidence, falsifier, cost) or the
   lead cannot compare answers.
9. **Fourth-wall ban** (never say "you have 4 agents") conflicts with transparency about cost.
   In seocli, say plainly what is being spawned and what it costs.

---

## 5. Claude Code primitives and limits that matter

### 5.1 Plugin anatomy (what a plugin can ship)

From the plugins reference. Required: only `.claude-plugin/plugin.json` with `name`
(kebab-case; reserved prefixes `claude-`, `anthropic-`, `cc-plugin-` are rejected by
`claude plugin validate`; `seocli-seo` is fine). Components at plugin root (not inside
`.claude-plugin/`): `skills/<name>/SKILL.md`, `agents/*.md` (subfolders become part of the
scoped name, `plugin:folder:agent`), `commands/*.md` (legacy, prefer skills), `hooks/hooks.json`,
`.mcp.json`, `bin/` (put on the Bash PATH), `settings.json` (only `agent` and
`subagentStatusLine` keys take effect), `monitors/`, `output-styles/`. Everything is
**namespaced**: skill `party` in plugin `seocli-seo` is `/seocli-seo:party`; agent
`auditor` is `seocli-seo:auditor`; `@agent-seocli-seo:auditor` forces delegation.
Variables in Markdown bodies: `${CLAUDE_PLUGIN_ROOT}`, `${CLAUDE_PLUGIN_DATA}` (persistent,
survives updates; use for state, never write under ROOT), `${CLAUDE_PROJECT_DIR}`,
`${CLAUDE_SKILL_DIR}`, `${user_config.KEY}` (non-sensitive only in skill/agent bodies).
`userConfig` in the manifest prompts the user at enable time (e.g. default party size or credit
ceiling; sensitive values go to secure storage).

`marketplace.json` lives at `.claude-plugin/marketplace.json` in the marketplace repo:
`name`, `owner{name}`, `plugins[]` with `name` + `source` (relative `./plugins/x`, or
`{source:"github",repo:...}` / `git-subdir`). Entry name must equal manifest name. Validate with
`claude plugin validate` (`--strict` in CI). `/reload-plugins` picks up local edits.

### 5.2 Skills

- Frontmatter (all optional; `description` recommended): `name`, `description`, `when_to_use`,
  `argument-hint`, `arguments`, **`disable-model-invocation`** (manual `/name` only),
  **`user-invocable: false`** (Claude-only knowledge, hidden from `/` menu), `allowed-tools`,
  `disallowed-tools`, `model`, `effort`, **`context: fork`** + **`agent: <type>`** (run in an
  isolated subagent; no conversation history; background by default, `background: false` to
  wait), `hooks`, `paths`, `shell`.
- Progressive disclosure, three tiers: (1) name+description of every skill in the system
  prompt at all times (budget ~1% of context; long descriptions get truncated; check with
  `/skill-doctor`), (2) full SKILL.md body when invoked (then it **stays in context** for the
  rest of the session; after compaction re-attached up to 5k tokens each, 25k combined),
  (3) reference files read on demand, scripts executed without loading source.
- Authoring rules from Anthropic: body < 500 lines; references **one level deep** from
  SKILL.md; TOC on files > 100 lines; third-person descriptions that state *what + when*;
  gerund or noun names, lowercase-hyphen, <= 64 chars; concise ("Claude is already very
  smart"); match degrees of freedom to fragility (low freedom for credit-spending steps);
  evaluation-first (>= 3 evals, test on Haiku/Sonnet/Opus); use **fully qualified MCP tool
  names** (`Server:tool`) in skill text.
- Dynamic injection: `` !`cmd` `` runs a shell command at load and substitutes its output
  (aborts load on non-zero). Useful for injecting the current credit balance... but see 11.5:
  a shell cannot call the remote MCP.

### 5.3 Subagents (`agents/*.md`)

Frontmatter fields: `name` (required, no colon), `description` (required; drives automatic
delegation; include "use proactively" style triggers), `tools` (allowlist) /
`disallowedTools`, `model` (`sonnet|opus|haiku|fable|inherit|full id`), `permissionMode`,
`maxTurns`, **`skills`** (full skill content *preloaded* into the subagent at start; skills
with `disable-model-invocation: true` cannot be preloaded), `mcpServers`, `hooks`,
**`memory`** (`user|project|local` -> `MEMORY.md` dir, first 200 lines/25KB auto-loaded),
`background`, `omitClaudeMd`, `effort`, `isolation: worktree`, `color`, `initialPrompt`,
`experimental.cacheTtl`.

**Plugin agents ignore `hooks`, `mcpServers`, `permissionMode`** (security rule). Ship hooks in
`hooks/hooks.json` and MCP in `.mcp.json` instead.

Context isolation (non-fork): the subagent gets its own system prompt (the file body plus
environment info, *not* Claude Code's full system prompt), the delegation prompt, the CLAUDE.md
hierarchy (skip with `omitClaudeMd: true`; Explore/Plan skip it), a git-status snapshot,
preloaded skills, and a sibling roster. It does **not** get the conversation history. Only its
final message returns to the caller ("results summarized back to main context"). Fork =
inherits the whole conversation, same model and cache, cannot fork again; `/subtask` starts
one.

Resolution of model: per-invocation param > frontmatter > `CLAUDE_CODE_SUBAGENT_MODEL` env >
main model. Family aliases resolve to the main model when same family.

Nesting: a subagent may spawn subagents up to **3 layers** below main by default
(`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH`); at the limit the `Agent` tool is withheld. Omit
`Agent` from `tools` to forbid spawning. Concurrent cap: **20** running subagents
(`CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS`). For an agent running as the main session
(`--agent x` or settings `agent`), `tools: Agent(a, b)` restricts which types it may spawn.

Tools removed from all subagents: `AskUserQuestion`, `EndConversation`, plan-mode tools, 
`ScheduleWakeup`, `WaitForMcpServers`, `Workflow` (and `Agent` at depth limit). **A subagent
cannot ask the user anything.** It can only return text. Background subagents additionally keep
only a fixed list (Read, Grep, Glob, Bash, Edit, Write, WebFetch, WebSearch, TodoWrite, Skill,
ToolSearch, Monitor, SendMessage, Artifact...) and "remove all others" **[verify: does this
strip MCP tools?]**. With fork mode on (default in interactive sessions) "all spawned subagents
run in the background by default" and the caller cannot force foreground via
`run_in_background`; `background: true` in frontmatter forces it, but there is no documented
`background: false` for agents (skills have it). Foreground subagents get the full tool set and
route permission prompts to the user.

Resuming: completion returns an agent ID; `SendMessage` to the ID or name continues the same
subagent with full history and shared cache. Named subagents (Claude passes `name` on spawn)
are addressable by siblings, and **a subagent holding `SendMessage` can message other named
agents**; sibling roster is a *snapshot at start* (agents named later are absent). Default
visibility between siblings: none. Resumed agents report back to whoever resumed them.

Invocation: automatic by description; natural language ("use the auditor subagent"); @-mention
`@agent-seocli-seo:auditor`; session-wide `claude --agent seocli-seo:lead` or settings
`agent`. A plugin `settings.json` with `{"agent": "lead"}` would make a persona the *default
main-thread agent* whenever the plugin is enabled - powerful but invasive; make it opt-in.

### 5.4 Agent teams (experimental)

Enable `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Interactive sessions only (`-p` and SDK do not
spawn teammates). One lead + teammates, each a full independent session with its own context;
mailbox (JSON files under `~/.claude/teams/<team>/inboxes`) + shared task list with
dependencies and file-lock claiming; teammates message each other by name; you can open and
talk to any teammate. A teammate may be spawned **from a subagent definition**: `tools`
(+ SendMessage and Task tools added), `model` and body (appended to system prompt) are
applied; **`skills` and `mcpServers` frontmatter are NOT applied** (teammates load skills and
MCP from project/user settings instead). Hooks `TeammateIdle`, `TaskCreated`, `TaskCompleted`
can block with exit code 2. Limits: no `/resume` of in-process teammates, task status lag,
slow shutdown, **one team per session**, no nested teams, lead is fixed, split panes need
tmux/iTerm2, permission mode copied from lead at spawn. Cost: "significantly more tokens...
scale with active teammates"; recommended 3-5 teammates; start with research/review. While
teams are enabled, a *named* subagent launches as a teammate (surprise teams); disable with
`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=0`.

Implication: a plugin cannot assume teams are on (env flag is user/admin owned). Party mode
must work on plain subagents and *optionally* upgrade.

### 5.5 Slash commands and skills as commands

Skills **are** the commands now (`commands/` is legacy). A skill is invocable as
`/plugin:skill args` with `$ARGUMENTS`/`$0`/named `arguments`. Use
`disable-model-invocation: true` on anything that spends credits or starts a multi-agent run so
only the human triggers it; use `user-invocable: false` for pure knowledge (methodology).

### 5.6 Hooks

Events relevant here: `SessionStart` (inject credit/site context via `additionalContext`,
10k chars cap), `PreToolUse` (can `deny`/`ask`/`allow`, rewrite `updatedInput`; matcher
regexp on tool name, MCP tools are `mcp__<server>__<tool>` and for plugin-bundled servers
`mcp__plugin_<plugin>_<server>__<tool>` - always use a `.*` suffix), `PostToolUse` (append
`additionalContext`, e.g. running credit tally), `SubagentStart` (async, cannot block; can add
context; observability), `SubagentStop`, `TeammateIdle`/`TaskCompleted` (quality gates),
`PreCompact`. Hook types: `command`, `http`, `mcp_tool`, `prompt`, `agent`. Plugin subagents
cannot carry their own hooks, but plugin-level `hooks/hooks.json` apply session-wide and can
match subagent tool calls by tool name.

### 5.7 MCP from a plugin (remote HTTP + OAuth)

`.mcp.json` at plugin root, auto-started when the plugin is enabled:

```json
{
  "mcpServers": {
    "seocli": {
      "type": "http",
      "url": "https://mcp.seocli.example/mcp"
    }
  }
}
```

OAuth: do **not** set `headers.Authorization` (a hard-coded header disables the OAuth fallback;
this is exactly the failure seen in this session: `AUTH_HEADER_REJECTED ... OAuth fallback is
disabled when headers.Authorization is set`). With no headers, Claude Code runs dynamic
discovery; user authenticates via `/mcp` (browser flow, token in keychain, auto refresh) or
`claude mcp login <server>` (`--no-browser` prints URL). Pre-registered client: `oauth`
object with `clientId`, `callbackPort`, `scopes`, `authServerMetadataUrl`. For Logto with
CIMD (client-id metadata documents) leave `oauth` unset and test discovery; if CIMD is not
accepted fall back to a pre-registered `clientId` (spike already planned in seocli-api
CLAUDE.md). `headersHelper` exists for non-OAuth short-lived tokens (10 s timeout, env includes
`CLAUDE_PLUGIN_ROOT`) - not needed.

Naming: tools surface as `mcp__plugin_seocli-seo_seocli__<tool>`. Skills should say
"`seocli:<tool>`" for Claude (Anthropic guidance), and agent `tools:` allowlists must use the
full scoped form (wildcards: `mcp__plugin_seocli-seo_seocli__*`; denylist supports
`mcp__*`) **[verify exact wildcard acceptance in `tools:`]**.

Output limits: warning at 10k tokens, default max 25k (`MAX_MCP_OUTPUT_TOKENS`); over-limit
output is spilled to a file under `~/.claude/projects/.../tool-results/` and replaced with a
reference. Servers can raise a per-tool cap via `_meta["anthropic/maxResultSizeChars"]`. 
**Design consequence for seocli-api**: tools should return compact structured summaries plus
an artifact/handle for the bulk, so personas stay lean (matches Anthropic's "return 1-2k token
condensed summaries").

### 5.8 Context rot and orchestration guidance (Anthropic)

- Context rot: recall accuracy falls as tokens grow; context is an "attention budget"; goal is
  "the smallest set of high-signal tokens". Tools: just-in-time retrieval (pass identifiers,
  fetch on demand), compaction, **structured note-taking outside the window**, and
  **sub-agents with clean windows returning condensed summaries (1-2k tokens)**.
- Multi-agent research system: orchestrator-worker; multi-agent uses ~**15x** the tokens of a
  chat (agents ~4x); each worker needs "an objective, an output format, guidance on tools and
  sources, and clear task boundaries" or they duplicate work; embed **effort-scaling rules**
  (simple fact finding = 1 agent / 3-10 tool calls; complex = 10+ workers); parallel tool
  calling cut time up to 90%; persist plans outside the window; have workers write artifacts to
  the filesystem and pass references instead of routing everything through the lead; poor fit
  for tasks needing shared context or heavy interdependence.
- Agent Teams doc's own guidance for debate: "competing hypotheses... explicitly adversarial...
  scientific debate" beats sequential investigation because of anchoring.

### 5.9 Mods

Documented under `plugins/mods`. A mod is a plugin with `hooks/hooks.json`
(`"modules": ["./register.js"]`) and a JS/TS module exporting `register(on, options)`. Relevant
APIs (see 13): `$.ui.toast`, `$.ui.status`, `$.ui` panes/band (live panel), `$.session.usage()`
(context tokens, rate limits, cost), `$.mcp.call/connect`, `$.agent.register/spawn/list`,
events `agent.offer`/`agent.spawn` (withhold a persona or force a model / deny a spawn),
`tool.call` (deny/rewrite a tool call), `turn.complete`, `session.measure`,
`session.send/receive` (messages between sessions). Terminal and Desktop. Limits: hook 10 s,
store 4 MiB, rendering throttled. Mods are an optional layer: nothing in the persona design may
depend on them.

---

## 6. Emulating a round-table: three mechanisms compared

Question: can subagents talk to each other? **Partially, and not usefully by default.**

| | A. Orchestrated subagents (moderator-mediated) | B. Named subagents with SendMessage | C. Agent team |
|---|---|---|---|
| Who carries messages | Lead (main session) | Subagents directly (need `SendMessage`, names, roster snapshot) | Mailbox, peer to peer |
| Visibility | Lead decides what each sees | Only those addressed | Only those addressed; no shared feed |
| Parallel round | Yes (several `Agent` calls in one message) | Yes but ordering is racy | Yes |
| Resume with memory | `SendMessage` to ID (history kept) or re-brief | Same | Teammates persist in session; lost on `/resume` |
| Availability | Always | Always (needs a recent Claude Code, roster v2.1.206+) | Experimental env flag, interactive only, 1 team/session |
| Control of cost/turns | High (lead sets rounds) | Low (agents can ping-pong) | Low-medium |
| Fits metered credits | Yes | Risky | Risky |

**Recommended emulation (mechanism A) - "blind, cross-exam, verdict":**

```
Round 0  Lead prepares an EVIDENCE PACK on disk (facts only, no opinions) and a
         QUESTION (the decision to be made).
Round 1  BLIND TAKES (parallel). N persona subagents, each given: persona (its own
         agent file), the question, the pack path. No sight of each other.
         Output: strict short schema (position, 3 supporting evidence ids, biggest risk,
         falsifier, confidence 0-1, what I need to know).
Round 2  CROSS-EXAMINATION (parallel, after lead shuffles). Each persona receives the
         OTHER personas' round-1 outputs (verbatim) and must (a) attack the strongest
         opposing claim, (b) concede what it now believes, (c) restate position.
         Same agent resumed via SendMessage (keeps its own reasoning) or fresh spawn
         with its own round-1 text attached.
Round 3  (optional, only if a disagreement is decision-critical) one-on-one rebuttal
         between the two disagreeing personas, lead relays.
Verdict  The lead (not a persona) writes the DECISION RECORD: options, who backed what,
         surviving objections, chosen strategy, falsifiable checks, leading indicators,
         cost of execution. The user decides; the room never "votes".
```

Why this beats a simulated single-context debate: round 1 is genuinely independent (no
anchoring), round 2 is genuinely reactive (each saw the actual words), and the lead keeps the
user in control. Why not let subagents free-chat: unbounded tokens, convergence by politeness,
no audit trail.

"Moderator" = the lead skill itself. Add an optional **moderator persona** (Killjoy-style
loop stopper / Level-style claim checker) as a *round-2 participant* rather than as the lead,
so the process critic is independent of the orchestrator.

Interactive feel: while the subagents run, the lead streams nothing; after each round it
presents the woven exchange (BMAD weaving rules: reorder so rebuttals land after what they
rebut; never alter substance). Use the persona icon as turn prefix.

Optional upgrade when teams are enabled: spawn the same persona definitions as teammates for
rounds 2+ (they message each other), lead as moderator, shared file `debate.md` as the feed
(append-only, one section per persona) to avoid BMAD's catch-up problem. Detect by asking the
user to enable the env var once; never require it.

---

## 7. Cost model

Rules of thumb from Anthropic and the docs: single agent ~4x a chat; multi-agent ~15x. A
persona subagent starts with CLAUDE.md + preloaded skills + delegation prompt (say 4-12k
tokens of fixed overhead), then works, then returns a 0.5-2k summary. Prompt cache is shared
per resumed agent; in-process teammates/background subagents cache 5 min unless
`subagentPromptCacheTtl`/`experimental.cacheTtl: 1h` is set (1h writes cost more).

Illustrative budget (Sonnet-class workers, Opus lead optional; numbers are planning
estimates, to be measured):

| Operation | Agents | Approx tokens (in+out, all agents) |
|---|---|---|
| Single persona consult (inline skill) | 0 (main) | 3-8k added to main context |
| One persona as subagent, one task | 1 | 15-40k |
| Parallel audit by 4 personas | 4 + lead synthesis | 80-200k |
| Party mode, 4 personas, blind + cross-exam | 8 spawns (or 4 + 4 resumes) + verdict | 150-350k |
| Same with 6 personas, 3 rounds | ~18 | 400-800k |
| Agent team, 5 teammates, 20 min | 5 + lead | 1M+ |

Controls to build in:
1. **Effort ladder in the lead skill** (Anthropic's lesson): quick question -> inline, one
   persona; single-surface audit -> one subagent; strategy decision -> party mode. Never start
   multi-agent for a lookup.
2. **Cheap models for breadth**: personas default `sonnet`; lens/skeptic personas on `haiku`
   only for pure checking; reserve `opus`/`fable` for the Strategist/Skeptic in round 2 and the
   verdict (claude-seo does the same split: judgment-heavy agents on Opus, rest on Sonnet).
3. **Hard caps**: persona `maxTurns`, max personas per party (default 4, ceiling 6), max rounds
   (default 2, ceiling 3), output length caps in prompts.
4. **Pre-flight cost line** shown before spawning: "Party: 4 personas x 2 rounds, ~200k tokens,
   0 seocli credits (uses pack)". Two separate meters: *LLM tokens* (user's Claude plan) and
   *seocli credits* (the product quota).
5. **No data fetching inside the room**: the pack is built once by the lead (or by a
   `researcher` persona in Round 0) with credits explicitly approved. Debate rounds read the
   pack only. This keeps seocli credit burn independent of the number of voices.
6. **Cache hygiene**: put stable material (persona file, methodology skills) first and the
   volatile pack path last in prompts; reuse resumed handles in round 2.

---

## 8. Recommended architecture for seocli

### 8.1 Principles

1. **Personas are workers with a narrow remit and a fixed output contract**, not chat
   characters. The *chat* feel lives in the lead and in the party woven transcript.
2. **The main thread is a thin coordinator**: it holds the user's goal, the site brief, the
   budget and the decision log; it never carries raw tool dumps (those go to disk, personas
   read them).
3. **Knowledge is shared and loaded by reference** (knowledge skills), never copied into persona
   prompts. A persona preloads only the 1-3 skills it needs.
4. **One persona per phase/lens of the existing methodology** (constitution VIII:
   PERCEIVE -> ANALYZE -> VALIDATE -> ACT; four fields per recommendation: first-principles
   observation, dependency, falsifiability check, leading indicator). Phases map naturally to
   personas; the 4 fields become the *output contract* every persona must obey.
5. **Credits are a first-class concern of the lead**, enforced mechanically (hook + server).
6. **Graceful degradation**: everything works with plain subagents; teams and mods are
   upgrades.

### 8.2 Components

```
                     user
                      |
        /seocli-seo:seo  (LEAD / orchestrator skill, main thread)
        - intake: domain, goal, market, budget (credits + tokens)
        - effort ladder: inline | one persona | multi-persona | party
        - owns: site brief, credit ledger, decision log, evidence packs
        - dispatches via Agent tool; synthesises; asks user (only it can)
             |                   |                     |
     persona subagents     /seocli-seo:party      knowledge skills
     (agents/*.md)         (round-table skill)    (user-invocable:false,
      researcher            moderated rounds       preloaded via skills:)
      technical-auditor     over evidence pack      methodology, seo-rules-*,
      content-strategist    -> decision record      output-contract, credit-policy,
      serp-competitor       uses same persona       tool-catalog, glossary
      geo-specialist        agents + lens agents
      analytics (GSC/GA4)
      skeptic (falsifier)
      prioritizer (ACT)
      credit-steward (optional)
             |
     seocli MCP (remote, OAuth):  mcp__plugin_seocli-seo_seocli__*
     server-side quota/RLS is the authority on credits
```

### 8.3 Orchestrator (lead)

Form: a **skill**, `skills/seo/SKILL.md`, `/seocli-seo:seo`, runs in the main session (so it
can use `AskUserQuestion` and show woven output). Not a subagent (subagents cannot ask the
user and results are summarised away). Optional second form: `agents/lead.md` so power users
can run `claude --agent seocli-seo:lead` for a dedicated session whose `tools:
Agent(researcher, technical-auditor, ...)` restricts what it can spawn. Keep the SKILL.md under
~150 lines: intent routing table, effort ladder, dispatch template, credit rules, synthesis
template; everything else in `references/` (one level deep).

Dispatch template (the lesson from Anthropic: objective, output format, tools/sources, task
boundaries) - the lead must fill all four every time:

```
Objective: <one question, one site>
Inputs: pack at ${CLAUDE_PROJECT_DIR}/seo-workspace/<domain>/packs/<id>.md  (read it; do not refetch)
Allowed seocli calls: <none | list with max credits>
Output: contract in output-contract skill (<=400 words, evidence ids, falsifier, leading indicator, cost)
Boundaries: do not discuss <other personas' remit>; if you need data not in the pack, list it under NEEDS, do not guess
```

### 8.4 Personas (initial roster, 8, extendable)

Naming: functional names, not human names, for the worker agents (searchable, vendor-neutral;
human-style names add tokens and no precision). Voice is delivered through a short
`voice` section; the **party transcript** uses icon + short label.

| Agent | Phase | Remit | Default model | Calls MCP? |
|---|---|---|---|---|
| `researcher` | PERCEIVE | Collect SERP/keyword/page facts into evidence packs; the only persona with fetch budget | sonnet | yes (approved envelope) |
| `technical-auditor` | ANALYZE | Crawl/indexability/CWV/schema; first-principles root causes | sonnet | pack only, or analysis tool calls with envelope |
| `serp-analyst` | ANALYZE | Intent, SERP features, competitor gaps | sonnet | pack only |
| `content-strategist` | ANALYZE | Topics, clusters, briefs, E-E-A-T gaps (no content generation server-side, per constitution VII) | sonnet | pack only |
| `geo-specialist` | ANALYZE | AI-search visibility, citation patterns | sonnet | envelope |
| `analytics-reader` | ANALYZE | Search Console / GA4 (private data) | sonnet | yes (user-linked) |
| `skeptic` | VALIDATE | Falsification: attacks every claim, demands the check and the leading indicator | opus | no |
| `prioritizer` | ACT | Impact/effort/dependency ordering, 30/60/90 plan, owners | sonnet | no |

Optional lens personas for party mode only (BMAD-style counterweights): `pragmatist`
(ship-the-80%), `claim-checker` (evidence discipline), `loop-stopper`, `contrarian`
(consensus challenger). These are *rules-of-the-room* agents; keep them tiny.

Casting principle: for any party, include at least one persona from each of: *builder*
(proposes), *checker* (falsifies), *counterweight* (pragmatist). Three to five seats.

### 8.5 Shared knowledge skills (non-invocable)

`user-invocable: false` + good `description` so the lead and personas can pull them; personas
preload the few they need with `skills:` (full content injected, so keep each short):

- `seo-methodology`: the 4 phases, the 4 fields, what "falsifiable" means, target-number rules,
  primary-source preference (Google docs first).
- `output-contract`: exact schema for persona replies and for the decision record.
- `credit-policy`: how credits work, never-spend rules, envelope semantics, cost-estimate
  lookups, what to do on `INSUFFICIENT_CREDITS`.
- `tool-catalog`: *pointer only* ("tool reference lives in the MCP schema; call
  `tools/list`"); per project rules the reference is not duplicated outside code. Keep this to
  tool-selection heuristics ("for X prefer tool Y first because cheaper"), not parameters.
- `seo-rules-<area>` (technical, content, geo, local, ecommerce): rule tables the agents need;
  read lazily from `references/` by the persona (not preloaded) unless the area is the
  persona's core.
- `glossary`: shared vocabulary (also reduces cross-persona drift).

Avoid preloading everything: each preloaded skill is paid by **every** spawn.

### 8.6 Party-mode skill

`skills/party/SKILL.md` (`/seocli-seo:party <topic or decision>`), `disable-model-invocation:
true` (costs tokens; human-triggered). Detailed in section 12.

### 8.7 Persistence (BMAD's memlog, adapted)

Directory in the **user's project** (not in the plugin): `seo-workspace/<domain>/`:

```
brief.md            # site, goals, market, constraints (lead maintains, <= 1 page)
credits.md          # append-only ledger: date, persona, tool, credits, why
packs/<id>.md       # evidence packs (facts only)
rounds/<party-id>/  # r1-<persona>.md, r2-<persona>.md, record.md
decisions.md        # append-only decision log (date, decision, falsifier, due date, result)
```

Plain Markdown, human-readable, git-able; replaces BMAD's memlog script. Persona-level
learning (style preferences, recurring site quirks) via `memory: project` on selected agents
(`.claude/agent-memory/<agent>/MEMORY.md`, first 200 lines loaded). Use sparingly: auto-loaded
memory is rot if unmanaged. Keep durable *facts about the site* in `brief.md`/`decisions.md`
(explicit, auditable), reserve agent memory for *behavioural* learnings.

---

## 9. File layout

```
agent-skills/
  .claude-plugin/
    marketplace.json
  plugins/
    seocli-seo/
      .claude-plugin/plugin.json
      .mcp.json                           # remote http server, OAuth, no static headers
      settings.json                       # (optional) NOT shipping a default agent
      agents/
        researcher.md
        technical-auditor.md
        serp-analyst.md
        content-strategist.md
        geo-specialist.md
        analytics-reader.md
        skeptic.md
        prioritizer.md
        lens/                             # party-only counterweights
          pragmatist.md                   #   -> seocli-seo:lens:pragmatist
          claim-checker.md
          contrarian.md
        lead.md                           # optional: claude --agent seocli-seo:lead
      skills/
        seo/                              # LEAD / orchestrator  (/seocli-seo:seo)
          SKILL.md
          references/
            effort-ladder.md
            dispatch-template.md
            synthesis-template.md
        party/                            # round-table         (/seocli-seo:party)
          SKILL.md
          references/
            protocol.md                   # rounds, prompts, caps
            casting.md                    # which seats for which decision type
            transcript-style.md           # weaving + icon rules (from BMAD)
            decision-record.md
        ask/                              # interactive single-persona consult
          SKILL.md                        #   (/seocli-seo:ask technical-auditor ...)
        methodology/SKILL.md              # user-invocable: false
        output-contract/SKILL.md          # user-invocable: false
        credit-policy/SKILL.md            # user-invocable: false
        glossary/SKILL.md                 # user-invocable: false
        tool-heuristics/SKILL.md          # user-invocable: false
        seo-rules-technical/SKILL.md      # user-invocable: false (+ references/)
        seo-rules-content/SKILL.md
      hooks/
        hooks.json                        # credit guard, ledger, SessionStart
      bin/
        seocli-ledger                     # tiny shell helper to append credits.md  (optional)
      mods/                               # optional separate plugin, see section 13
```

Notes:
- Subfolder `agents/lens/` yields scoped names `seocli-seo:lens:pragmatist` (documented).
- Keep the lead, party and ask skills as separate skills so each stays well under 500 lines and
  only the needed one enters context.
- Mods should be a **separate plugin** (`seocli-seo-hud`) so the core plugin has zero JS and
  installs on all surfaces.
- `plugin.json` `name` = `seocli-seo`; `userConfig`: `credit_ceiling_per_run` (number),
  `default_party_size`, `workspace_dir` (default `seo-workspace`).

---

## 10. Frontmatter and file examples

All examples are original drafts for this design. Language: English for the plugin (matches the
agent-skills repo); user-facing replies follow the user's language (instruct in lead skill).

### 10.1 `.claude-plugin/plugin.json`

```json
{
  "name": "seocli-seo",
  "displayName": "SEO with seocli",
  "version": "0.1.0",
  "description": "SEO methodology as a team of specialist personas plus a strategy round-table, powered by the seocli MCP server.",
  "author": { "name": "seocli" },
  "license": "MIT",
  "keywords": ["seo", "mcp", "subagents", "strategy"],
  "userConfig": {
    "credit_ceiling_per_run": {
      "type": "number",
      "title": "Max seocli credits per run",
      "description": "The lead never approves personas to spend more than this in one run without asking you.",
      "default": 50,
      "min": 0
    },
    "workspace_dir": {
      "type": "string",
      "title": "Workspace folder",
      "description": "Where briefs, evidence packs and decision logs are written (relative to the project).",
      "default": "seo-workspace"
    }
  }
}
```

### 10.2 `.mcp.json` (remote HTTP, OAuth by discovery)

```json
{
  "mcpServers": {
    "seocli": {
      "type": "http",
      "url": "https://mcp.seocli.example/mcp"
    }
  }
}
```
(No `headers.Authorization`. If CIMD discovery fails with Logto, add
`"oauth": { "clientId": "<pre-registered>", "callbackPort": 8765 }`. Local dev variant lives in
a separate `.mcp.dev.json`, not shipped.)

### 10.3 A worker persona: `agents/technical-auditor.md`

```markdown
---
name: technical-auditor
description: Technical SEO specialist. Use proactively when the question is about crawlability, indexability, rendering, Core Web Vitals, structured data, redirects or site architecture, and an evidence pack or page data is available. Returns root causes with falsifiable checks; does not write content.
tools: Read, Grep, Glob, mcp__plugin_seocli-seo_seocli__analyze_page, mcp__plugin_seocli-seo_seocli__get_credit_balance
model: sonnet
maxTurns: 12
skills:
  - methodology
  - output-contract
  - credit-policy
  - seo-rules-technical
color: orange
---

# Technical Auditor

## Identity
You find why a page cannot be crawled, rendered, indexed or understood. You think from first
principles: what does Googlebot actually receive, in what order, at what cost?

## Voice
Plain, exact, unemotional. You name the failing mechanism, then the evidence id, then the fix.
Pet peeve: "improve site speed" without a metric and a threshold.

## Principles
1. Observe before you diagnose. Quote the evidence id for every claim.
2. One root cause per finding; list dependencies between findings explicitly.
3. Every finding carries a falsification check (what result would prove you wrong) and a
   leading indicator (what moves first, with a numeric target and a time window).
4. Prefer Google primary documentation over third-party lore; cite the page.
5. If the pack lacks what you need, write it under NEEDS. Never guess and never refetch what
   the pack already holds.

## Spending rule (hard)
You may call seocli tools only if the delegation prompt contains `ENVELOPE: <n> credits` and
the calls fit inside it. Before each paid call state the estimated cost; after it, state the
cost reported by the tool. If the envelope would be exceeded, stop and return what you have
with NEEDS. Never retry a failed paid call without the lead's approval.

## Output
Follow the `output-contract` skill exactly (max 400 words). Start with `STATUS: ok|partial|blocked`.
```

### 10.4 A lens persona for the room: `agents/lens/pragmatist.md`

```markdown
---
name: pragmatist
description: Party-mode counterweight. Forces the room to rank what matters for the client's goal and budget, and to cut gold-plating. Only used inside the seocli-seo party round-table.
tools: Read
model: sonnet
maxTurns: 4
omitClaudeMd: true
skills: [output-contract]
color: yellow
---

You are the room's pragmatist. Your job is to stop perfect-is-the-enemy-of-shipped.

Agenda: the smallest action set that moves the leading indicator within the stated window and
budget. You ask "what is the ranking cost of NOT doing this?" and "what does a small client
actually have capacity to do in 30 days?".

Voice: short, a little impatient, concrete numbers. You concede fast when the evidence is
strong, hold firm when someone confuses "true" with "important".

Rules for the room (binding):
- Read the pack and the other seats' texts you are given; do not call any tool except Read.
- Round 1: give position, top 3 actions, what you would drop. Round 2: attack the most
  expensive proposal in the room, concede one point, restate.
- Never reconcile for the sake of peace. Max 120 words per turn.
```

### 10.5 The skeptic: `agents/skeptic.md` (excerpt)

```markdown
---
name: skeptic
description: Falsification specialist for SEO claims and plans. Use proactively before any recommendation is presented to the user, and in every party round-table. Tries to break findings; never proposes new tactics.
tools: Read, Grep, Glob
model: opus
maxTurns: 8
skills: [methodology, output-contract]
color: red
---
You attack claims, not people. For every finding you receive: (1) restate it in one line,
(2) name the weakest assumption, (3) say which observation would refute it and whether the pack
can already answer that, (4) grade: SURVIVES / WEAKENED / REFUTED / UNTESTABLE. Untestable is a
defect. You do not suggest tactics. You do not soften grades to be polite.
```

### 10.6 Interactive consult skill (BMAD-style activation, but light): `skills/ask/SKILL.md`

```markdown
---
name: ask
description: Talk directly to one SEO specialist persona in this conversation (technical auditor, content strategist, SERP analyst, GEO specialist, skeptic, prioritizer). Use when the user says "ask the auditor", "talk to the content strategist" or wants a single specialist's view without a full audit.
argument-hint: "<persona> <question>"
arguments: [persona, question]
disable-model-invocation: true
---
Resolve `$persona` to one of the agents in `agents/` (fuzzy match). Then:

1. If the question needs more than ~3 tool calls or reads large data, delegate: spawn the
   agent via the Agent tool (foreground) with the dispatch template from the `seo` skill and
   relay its answer verbatim, prefixed with its icon and label.
2. Otherwise adopt the persona inline: read `agents/<persona>.md`, take on Identity, Voice
   and Principles, answer `$question`, prefix every message with the persona label, and stay
   in character until the user says "dismiss". Spending rule applies unchanged.
```
This reproduces BMAD's embody-until-dismissed UX but defaults to delegation so the main
context stays clean.

### 10.7 The lead: `skills/seo/SKILL.md` (core of it)

```markdown
---
name: seo
description: Orchestrates SEO work for a site by routing to specialist personas (research, technical, content, SERP, GEO, analytics, skeptic, prioritizer) and to the strategy round-table. Use when the user asks for an SEO audit, keyword or content strategy, competitor or SERP analysis, AI-search (GEO) visibility, or a prioritized SEO plan for a domain.
when_to_use: Any multi-step SEO request, or when unsure which specialist fits.
argument-hint: "<domain> [goal]"
---
# SEO lead

You are the coordinator. You do not do deep analysis yourself; you route, budget, verify and
synthesise. Reply in the user's language.

## 1. Intake (ask, do not guess; one AskUserQuestion round)
Domain, business goal, market/language, constraints (budget in credits, deadline). Create or
update `${user_config.workspace_dir}/<domain>/brief.md` (<= 1 page).

## 2. Pick effort (read references/effort-ladder.md)
| Request | Do |
|---|---|
| Lookup, one fact, one page | Answer inline with at most 3 MCP calls |
| One lens (e.g. "why isn't this indexed") | One persona subagent |
| Multi-lens audit | Researcher builds pack, then 2-4 personas in parallel |
| Strategy / trade-off / "what should we do first" | Pack, then `/seocli-seo:party` |
State the plan and the cost line before spawning anything.

## 3. Credits (hard rules; see credit-policy)
- Check balance once with `seocli:get_credit_balance`; record in credits.md.
- Only `researcher` (and personas given an ENVELOPE) may call paid tools.
- Never exceed `credit_ceiling_per_run` without asking the user.

## 4. Dispatch
Fill the template in references/dispatch-template.md completely. Launch independent personas
in ONE message (parallel). Foreground only (see risks). Each returns <= 400 words.

## 5. Verify then present
Send the findings to `skeptic` before presenting. Present: the answer first, then the 4 fields
per recommendation, then what was refuted or untestable, then credits spent. Append decisions
to decisions.md.
```

### 10.8 Party skill: `skills/party/SKILL.md` (core)

```markdown
---
name: party
description: Runs a moderated strategy round-table where several SEO specialist personas first form independent views and then challenge each other, ending in a decision record. Use when the user asks for party mode, a round-table, a debate between specialists, or help choosing between SEO strategies.
argument-hint: "<decision to make> [--seats a,b,c] [--rounds 2]"
disable-model-invocation: true
---
Follow references/protocol.md. Summary:
1. Frame the decision as one question with 2-4 candidate options. Confirm with the user.
2. Cast 3-5 seats (references/casting.md): >= 1 builder, 1 checker (skeptic), 1 counterweight.
3. Build or reuse the evidence pack. Debate rounds never call paid tools.
4. Show the pre-flight line (seats, rounds, tokens, credits=0) and wait for a go.
5. Round 1 blind takes in parallel -> files r1-<seat>.md. Present them woven.
6. Round 2 cross-exam in parallel with all r1 texts -> r2-<seat>.md. Present woven.
7. Write the decision record (references/decision-record.md). Offer a third round only if a
   decision-critical disagreement remains.
Do not vote. Do not reconcile. The user decides.
```

### 10.9 `hooks/hooks.json` (credit guard + ledger)

```json
{
  "hooks": {
    "SessionStart": [
      { "hooks": [ { "type": "command",
        "command": "\"${CLAUDE_PLUGIN_ROOT}\"/bin/seocli-session-context" } ] }
    ],
    "PreToolUse": [
      {
        "matcher": "mcp__plugin_seocli-seo_seocli__.*",
        "hooks": [ { "type": "command",
          "command": "\"${CLAUDE_PLUGIN_ROOT}\"/bin/seocli-credit-guard" } ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "mcp__plugin_seocli-seo_seocli__.*",
        "hooks": [ { "type": "command",
          "command": "\"${CLAUDE_PLUGIN_ROOT}\"/bin/seocli-ledger" } ]
      }
    ]
  }
}
```
`seocli-credit-guard` reads the hook JSON from stdin (tool name, input, `agent_type`), looks up
`ceiling` and spent-so-far in `credits.md`, and either exits 0, emits
`permissionDecision: "ask"` (surfaces to the user, even for subagent calls) or `"deny"` with a
reason the persona can read. **It estimates; the server is the authority.** Keep it ~30 lines
of shell. **[verify: whether PreToolUse fires for tool calls made inside subagents with the
subagent's agent_type; the docs state hooks apply session-wide, test it.]**

### 10.10 `marketplace.json`

```json
{
  "name": "seocli",
  "description": "seocli plugins for Claude Code",
  "owner": { "name": "seocli" },
  "plugins": [
    {
      "name": "seocli-seo",
      "source": "./plugins/seocli-seo",
      "description": "SEO specialist personas, strategy round-table and the seocli MCP server"
    }
  ]
}
```

---

## 11. Personas calling seocli MCP tools and respecting credits

### 11.1 Access model

1. The MCP server is declared once in the plugin `.mcp.json`; all agents inherit it from the
   session. Plugin agents cannot declare `mcpServers` themselves.
2. Restrict per persona with `tools:` using full scoped names. Principle of least tool: the
   skeptic and prioritizer have **no** seocli tools; most analysts get read-only pack access
   plus at most a balance check; only `researcher` (and `analytics-reader` for the user-linked
   Google data) get fetch/analysis tools.
3. Optional belt-and-braces: `disallowedTools: mcp__*` on lens personas.

### 11.2 Credit discipline: plan, approve, execute, record

```
persona (or lead) PLAN   ->  lead APPROVE (ceiling / ask user)  ->  EXECUTE  ->  RECORD
 "I need: serp(q=...,loc=...) ~4cr; page(url) ~2cr"   envelope=6     tool call     credits.md
```

- **Envelope**: a number in the delegation prompt. Personas must treat it as a hard cap and
  must report actual spend in their output (`SPENT: n`).
- **Estimate before call**: the server should expose either a `cost` field in each tool's
  description/annotations or an `estimate_cost(tool,args)` tool. Ask seocli-api to make
  estimates cheap (no credits) and idempotent. Personas call `estimate_cost` (free) when
  unsure.
- **Idempotency / reuse**: seocli-api already reports "the operation reused" (commit f30b1c8 in
  this repo: the response declares a reused operation) and has a SERP cache decision. Personas
  must prefer re-reading a pack to re-fetching; the lead passes pack ids.
- **Errors**: standardise `INSUFFICIENT_CREDITS`, `QUOTA_EXCEEDED`, `RATE_LIMITED` as domain
  errors; credit-policy skill tells personas: stop, do not retry, report, let the lead ask the
  user. Never loop on a paid call.
- **Surface balance**: `SessionStart` hook or the lead's first step reads the balance
  (`get_credit_balance` is free) and injects "balance: N" so everyone plans with the same number.
- **Subagents cannot ask the user**: the only valid "ask" path is returning `NEEDS`/`BLOCKED`
  to the lead; or a PreToolUse `ask` decision which shows the permission prompt in the main
  session even for subagent calls (docs: permission prompts from subagents surface to the user)
  **[verify wording for hook-driven ask in subagents]**.
- **Party mode**: zero fetch in debate rounds (see 7). If a seat discovers a data gap in round
  1, it lists NEEDS; the lead may run one *bounded* "fill the gaps" researcher pass between
  rounds, with the user's go-ahead and a ceiling.

### 11.3 Data privacy hooks into persona design

Constitution VII: private client data (Search Console, GA4) never goes to non-EU models. The
plugin runs on the user's Claude, so that constraint is a *server-side* LLM matter, but the
plugin should still: keep `analytics-reader` outputs aggregated; avoid writing raw GSC/GA4
exports to packs that other personas or a shared repo might read; and never put private rows in
a party transcript that the user may share. Add that to `credit-policy` or a short `data-policy`
skill and to the analytics persona's principles.

### 11.4 If background subagents cannot call MCP tools

Preferred design assumes personas call MCP. If testing shows MCP is unavailable to background
or plugin subagents:

1. **Lead-fetches design**: only the main thread calls MCP (always allowed). Personas become
   pure *analysts over packs*; `researcher` becomes a **skill the lead executes inline** that
   writes packs. Cost: main context holds the raw calls briefly (mitigate: tools return compact
   summaries + file handles; the lead writes them straight to disk and discards).
2. Or force foreground: invoke subagents with the Agent tool's foreground mode where possible,
   or `/subtask`-free flows; docs say foreground subagents have the full tool set. With fork
   mode on, all are background by default, so check `CLAUDE_CODE_FORK_SUBAGENT=0` (user env,
   document in README) as a workaround.
3. Or use the skill route: skills with `context: fork` + `background: false` run a subagent and
   wait for the result in the same turn; its tool set follows the chosen `agent`.

Test this on day one (section 15). The rest of the architecture is independent of the outcome.

### 11.5 Things that do not work

- `` !`...` `` injection in skills cannot call a remote MCP tool (shell only); use a hook or
  the lead's first step for balance.
- Putting the user's OAuth token into a hook or script: not available and not needed; hooks
  see tool names/inputs, not credentials.
- Agent-level `mcpServers`/`hooks` frontmatter: ignored for plugin agents.

---

## 12. Party mode design

### 12.1 Goals (differs from BMAD)

BMAD: lively conversation that may surprise the user. seocli: **a reproducible way to turn a
fuzzy strategic question into a decision with falsifiable checks**, with a human-readable
debate as the explanation. So keep BMAD's anti-groupthink mechanics and tone rules, but add a
protocol, budgets and a verdict.

### 12.2 Modes (mapped to Claude Code reality)

| seocli party mode | Mechanism | Use when |
|---|---|---|
| `quick` | **Inline single context**: lead voices all seats in one pass (BMAD `session`) | Brainstorm framing, naming, low stakes; 0 spawns |
| `debate` (default) | **Blind round + cross-exam round** via parallel subagents (section 6 mechanism A) | Real decision; recommended |
| `panel` | `debate` + a third, one-on-one rebuttal round between the two most opposed seats | Decision-critical disagreement |
| `team` (opt-in) | Agent team with same persona files; shared `debate.md` feed; lead moderates | User enabled the env flag; long research debates |

`auto` from BMAD is replaced by the lead's effort ladder (it already decides when to spawn).
Fallback chain: `team -> debate -> quick`, with a one-line notice (unlike BMAD's silent
fallback: the user pays tokens and deserves to know).

### 12.3 Protocol (`references/protocol.md`)

```
0. FRAME   decision question Q, options O1..On (2-4), success metric + window, constraints
           (budget credits, effort, deadline). Write rounds/<id>/frame.md.
1. CAST    3-5 seats from casting.md. Rule: >= 1 builder, 1 checker (skeptic), 1 counterweight.
           Declare seat -> agent mapping and models.
2. PACK    Build or reuse packs/<id>.md. FACTS ONLY (no opinions); each fact has an id F12.
           Pack size target <= 4k tokens (summaries + handles to detail files).
3. COST    Print pre-flight: seats, rounds, est. tokens, credits=0 (or approved gap-fill n).
           Wait for user go.
4. R1      Parallel Agent calls, one per seat, ONE message. Prompt = persona + Q + pack path +
           R1 schema. Seat writes rounds/<id>/r1-<seat>.md and returns it.
5. WEAVE   Lead presents R1 as a conversation (icon + label turns), ordering for flow, no
           substantive edits. Marks agreements and open clashes in one line each, NOT a summary.
6. R2      Parallel again. Each seat gets: its own R1, all others' R1 verbatim, and R2 schema:
             ATTACK (strongest opposing claim, quoted), CONCEDE (what changed), POSITION (final),
             CONFIDENCE delta, REMAINING DISAGREEMENT.
           Resume the same agent by ID when possible (keeps its reasoning); else fresh spawn
           with its R1 included.
7. WEAVE   Present R2 as rebuttals landing after what they rebut.
8. R3?     Only if lead flags a decision-critical clash: relay A->B->A once. Cap at one exchange.
9. VERDICT The LEAD writes decision-record.md: chosen option; who backed which; objections that
           survived; objections that were refuted and by which evidence ids; for each action the
           4 fields; credits/time cost; review date. Skeptic grades the record
           (SURVIVES/WEAKENED/UNTESTABLE) before it is shown. The user accepts/edits.
10. LOG    Append to decisions.md; update brief.md if goals changed.
```

### 12.4 Round schemas (keep short so weaving is cheap)

R1 (<= 150 words):
```
POSITION: <option or new option>
BECAUSE: <2-3 bullets, each cites a fact id>
BIGGEST RISK: <one line>
FALSIFIER: <observation that would prove me wrong; metric, threshold, window>
CONFIDENCE: 0.0-1.0
NEEDS: <data not in pack, or none>
```
R2 (<= 150 words):
```
ATTACK: <quote the claim from seat X> -> <why it fails, cite fact id>
CONCEDE: <what I now accept from whom, or "nothing" + why>
POSITION: <final>   CONFIDENCE: <delta>
REMAINING DISAGREEMENT: <with whom, about what>
```

### 12.5 Voice and tone (keep from BMAD)

Port these rules to `transcript-style.md` (adapted): turns prefixed with icon + short label;
short turns; the room **clashes and the lead does not reconcile**; pull the user in with the
decision question after each round ("Which of these two objections worries you?"); change
something when it sags (add a seat, name the impasse); weave without altering substance;
unequal voices allowed. Drop: the fiction-only rule and "no summary ever" (the verdict is the
deliverable) - but keep the *rounds* free of summary: the summary only appears in the record.

Add a small **anti-groupthink kit** from BMAD's Anti-Consensus Club as default room rules in
`protocol.md`: no voting; if all seats agree in R1, the lead runs a one-shot `contrarian` turn
("name the hidden assumption") before proceeding; if R2 repeats R1, stop and ask the user which
unresolved question matters (loop-stopper behaviour).

### 12.6 Casting examples (`casting.md`)

| Decision type | Seats |
|---|---|
| "Fix technical debt first or publish new content?" | technical-auditor, content-strategist, prioritizer, skeptic, pragmatist |
| "Which keyword cluster should we own in 6 months?" | serp-analyst, content-strategist, analytics-reader, skeptic, contrarian |
| "Do we invest in AI-search visibility now?" | geo-specialist, serp-analyst, prioritizer, skeptic, claim-checker |
| Migration / re-platform risk | technical-auditor, serp-analyst, prioritizer, skeptic, pragmatist |

Rules: max 5 seats by default (6 hard cap); models: builders `sonnet`, skeptic `opus`,
counterweights `sonnet`/`haiku`; do not include two seats with the same remit.

### 12.7 Memory

BMAD's memlog distils "dynamics, moments, callbacks, outcomes". For strategy work the useful
memory is different: **past decisions, their falsifiers, and outcomes**. Implement as
`decisions.md` (append-only, one block per decision: date, question, chosen option,
falsifier, review date, result filled in later). On party start the lead reads the **last N
decisions + any with a review date due** (a tiny reader step, not a subagent), and gives the
seats only the relevant ones through the pack. Optional "relationship memory" (running gags,
grudges) is YAGNI for a metered product; skip it.

### 12.8 Wrap-up

Triggered when the user accepts/declines the verdict (not when the first question is answered)
or says done. Steps: (1) final record saved; (2) offer to schedule a review (`/schedule` or a
note with the date and the falsifier metric); (3) offer a shareable keepsake only on request
(Markdown/HTML of the woven transcript + record, with private data scrubbed); (4) release
any resumed agents (they die with the session anyway); (5) append credits ledger totals
(party = 0 credits unless gap-fill).

### 12.9 Interaction with the lead during the room

The user can interject between rounds: "ask the skeptic about X", "add the GEO specialist",
"drop the pragmatist". The lead supports: add/remove seat (re-cast, reuse existing R1 files so
a new seat gets all prior material), pause, jump to verdict, change rounds. New seats get the
pack plus all prior-round text (BMAD's "one shared room" principle implemented through files,
cheaper than relaying through messages).

---

## 13. Mods (plugin-authoring) opportunities

Optional second plugin `seocli-seo-hud` (JS hooks module). Ideas, ordered by value:

1. **Credit meter in the status area**: `$.ui.status` plus a `tool.call`/`session.measure`
   hook that tallies seocli credits from tool results; shows `credits: 38/50 | party r2/2`.
   (User already asked for crediti + subscription in a status line - see project memory.)
2. **Toast when a long run ends** (`$.ui.toast`, 4 s default) when all party seats finish or a
   persona hits its envelope.
3. **Hard stop guard**: `tool.call` hook returning `{ deny: reason }` when spend would pass
   the ceiling - stronger and faster than a shell hook, and visible in the UI. Also
   `agent.spawn` to deny a spawn when the party exceeds seat or concurrency caps, or to set the
   model of a seat.
4. **Persona pane**: `$.ui.open` pane listing seats with state (idle/working/done) and a
   button per seat to open its transcript - a poor-man's panel for the party.
5. **Offer filtering**: `agent.offer` can withhold lens agents from normal delegation so they
   never get auto-picked outside party mode.

Constraints: mods are experimental in a sense of surface (CLI + Desktop only), 10 s hook
budget, store 4 MiB; they require Claude Code v2.1.287-class. Therefore the core credit safety
cannot rely on them; the shell hook + server quota are the baseline. `claude plugin validate`
and `claude plugin test` cover mods.

---

## 14. claude-seo comparison (brief; sibling note covers it)

From the repo README: 19 specialist agents `agents/seo-*.md` (5 judgment-heavy on Opus:
content, geo, sxo, cluster, drift; the rest on Sonnet); an orchestrator skill `skills/seo/SKILL.md`
that detects industry (SaaS, local, e-commerce, publisher, agency), dispatches up to 17
subagents in parallel and synthesises "through a 10-principle framework grouped into four
phases: PERCEIVE, ANALYZE, VALIDATE, ACT"; 26 sub-skills `skills/seo-*/`; Python scripts for
fetch/render; extension MCPs. Takeaways for seocli:

- Same shape as our recommendation (orchestrator skill + persona-ish agents + sub-skills).
- It is **fan-out audit**, not a **debate**: agents run independently and the orchestrator
  merges. There is no cross-exam, no skeptic seat, no decision record. Party mode is our
  differentiator.
- Model split by judgment weight is a good default to copy.
- 17 parallel agents is above the doc's recommended 3-5 for teams and heavy on tokens; we cap
  audits at 4 personas plus the pack.
- Its data comes from local scripts and many third-party MCPs; ours comes from one hosted MCP
  with quotas, so credits (absent there) shape our design.

---

## 15. Risks, open questions, test plan

### 15.1 Risks

| # | Risk | Mitigation |
|---|---|---|
| 1 | Background/plugin subagents cannot call MCP | Test first (15.2 T1); fallback 11.4 |
| 2 | OAuth for remote MCP in plugin fails with Logto CIMD | Spike already planned; fall back to pre-registered `clientId`; document `/mcp` login; never ship a static Authorization header |
| 3 | Cost surprise (15x multiplier) | Effort ladder, pre-flight line, caps, `maxTurns`, cheap models, packs |
| 4 | Voices converge anyway | Blind R1 with separate contexts; skeptic seat; contrarian one-shot rule; vary models (a Haiku checker catches different things) |
| 5 | Persona schema drift -> lead cannot compare outputs | `output-contract` skill preloaded by all; validate lengths; skeptic rejects non-conforming outputs |
| 6 | Skill-description budget (many skills) | Make knowledge skills `user-invocable: false` but still have terse descriptions; consider `name-only` overrides; measure with `/skill-doctor` |
| 7 | Preloaded skills inflate every spawn | Preload <= 3 per agent; push detail to `references/` read on demand |
| 8 | Surprise teams when env flag is on (named subagents become teammates) | Document; party skill detects and uses the `team` path deliberately or tells the user |
| 9 | Plugin-agent restrictions (no hooks/mcpServers/permissionMode) | Plugin-level hooks; tool allowlists; document that users may copy agents to `.claude/agents/` to customise |
| 10 | Stale roster in named-subagent messaging | Lead is the bus; do not depend on SendMessage between siblings |
| 11 | Private data leakage into packs/transcripts | data-policy rules; scrub on export; keep GSC/GA4 aggregated |
| 12 | Tool name drift = contract break with seocli-api | Persona `tools:` and skills reference tool names; keep a single `tool-heuristics` skill and a contract test (a script that diffs `tools/list` against names used in `agents/*.md`) |
| 13 | Namespaced names confuse users | README table: `/seocli-seo:seo`, `/seocli-seo:party`, `/seocli-seo:ask` |

### 15.2 Test plan (do before building much)

- **T1 MCP in subagents**: define a throwaway plugin agent with `tools:
  mcp__plugin_seocli-seo_seocli__get_credit_balance`; invoke (a) by @-mention, (b) by natural
  delegation with fork mode on and off, (c) in parallel x3. Record: tool visible? permission
  prompts? failure text. Same with the real tool-name wildcard.
- **T2 PreToolUse inside subagents**: does the guard hook fire with `agent_type`? Can it `ask`?
- **T3 SendMessage resume** for round 2: does a resumed agent keep reasoning and cache? Token
  delta vs fresh spawn with R1 re-attached.
- **T4 Preload cost**: measure spawn overhead with 0/2/4 preloaded skills.
- **T5 Convergence experiment**: same question, (i) one-context voiced room, (ii) blind
  subagents, (iii) blind + cross-exam; count distinct positions in R1 and flips in R2 and have
  the skeptic grade the final records. This is the evidence for "separate contexts raise
  precision".
- **T6 Eval set** (Anthropic: >= 3 evals, test on Haiku/Sonnet/Opus): 3 audit scenarios and 3
  decision scenarios on etruria.fishing (test domain); rubric = evidence ids present, falsifier
  present and numeric, credits within envelope, no hallucinated data.
- **T7 Teams path**: enable the flag, run the `team` mode on one decision, record cost and
  failure modes (no resume, mailbox lag).
- **T8 Scoped names**: confirm `seocli-seo:lens:pragmatist` resolves and that a project-level
  agent of the same name overrides the plugin's.

### 15.3 Open questions for the product owner

1. Human-like persona names (BMAD style: Mary, John) or functional names? Recommendation:
   functional names for workers, optional flavour names in the party transcript only.
2. Should `researcher` be a persona agent or the lead's inline step (depends on T1)?
3. Is a per-run credit ceiling a `userConfig` knob (client-side honour system) or only
   server-side (account quota)? Recommendation: both; the server stays authoritative.
4. Does seocli-api expose a free `estimate_cost` / balance tool? Needed by credit-policy.
5. Language policy: plugin text in English, responses in the user's language (lead instruction).
6. Shipping a default `agent` in plugin `settings.json`: recommended no (too invasive).

---

## 16. Sources

Local (read in full unless noted):
- `seocli-api/.claude/skills/bmad-party-mode/SKILL.md`, `customize.toml`,
  `references/mode-subagent.md`, `mode-agent-team.md`, `mode-auto.md`, `party-memory.md`,
  `create-party.md`, `scripts/resolve_party.py` (head)
- `seocli-api/.claude/skills/bmad-agent-pm/{SKILL.md,customize.toml}`,
  `bmad-agent-dev/customize.toml`, `bmad-code-review/SKILL.md` (head)
- `seocli-api/_bmad/custom/config.toml` (agent registry descriptors)
- `agent-skills/research/claude-seo/skills.md` exists (sibling research, not re-read here)

Online (fetched 2026-10-02):
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/agent-teams
- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/plugins-reference (manifest reference)
- https://code.claude.com/docs/en/plugin-marketplaces
- https://code.claude.com/docs/en/mcp
- https://code.claude.com/docs/en/hooks
- https://code.claude.com/docs/en/plugins/mods/reference
- https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- https://www.anthropic.com/engineering/multi-agent-research-system
- https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- https://github.com/bmad-code-org/BMAD-METHOD (summary level)
- https://github.com/AgriciDaniel/claude-seo (summary level)
