last_verified: 2026-10-02
volatile: true

# Tool map (server v0.9.0)

The machine-readable copy is `tests/contract/tools.json` in the plugin repository; the lint keeps the
two consistent with every file of the plugin. Status "now" means exposed by server v0.9.0.

## Available now

| Tool | Parameters | Cost | Use |
|---|---|---|---|
| `seocli:get_status` | none | free | Service name and version; session check. A `unauthorized` error means the account is pending or not logged in. |
| `seocli:manage_clients` | `action`: create, list, get, rename, archive, restore, delete; `id`, `domain`, `name`, `status` (list filter) | free | Clients are the sites the account follows. `create` needs `domain` (name defaults to the domain); `get`, `rename`, `archive`, `restore`, `delete` need `id`. `delete` is irreversible. |
| `seocli:get_credit_balance` | none | free | `included` (this period), `topup`, `total`, and `plan`: the account plan (`pending` = waiting for activation, `beta`; later `active`, `suspended`). |
| `seocli:list_credit_movements` | `from`, `to` (`YYYY-MM-DD`, UTC, inclusive; default: start of the current credit period to today), `client_id` (uuid or `none`), `limit` (1-500, default 100), `cursor` | free | Credit ledger, newest first: `movements[]` with `kind`, signed `credits` (balance change) and `amount`; `summary[]` per client with `charged` over the whole period; `next_cursor` until the last page. A reservation subtracts the estimate, a release returns the unused part, a confirmation records the charge in `amount` with 0 change. |
| `seocli:get_price_list` | none | free | `version` and `items[]` with `operation` code, `description`, `unit`, `credits`. |
| `seocli:check_serp` | `keywords` (1-10), `country` (ISO, default IT), `language` (default it), `device` (desktop/mobile), `client_id` **or** `domain` (pre-sales, not saved), `refresh` (force new data at full price), `max_credits` | **paid**: 3 credits per page of 10 results read; reading stops at the page where the site appears; estimate 30 per keyword, charge = pages read. Shared 24 h cache: data already paid by this account = free; any other check = full price (pages needed by the site), with `collected_at` showing the data date; `refresh` = full price | Per keyword: `position` (`ranked: false` if not in the first 100), `url`, `other_urls`, up to 10 `competitors` (position, domain, url), `features` (people_also_ask, local_pack, shopping, ai_overview *seen on the page*), `pages_read`, `mode` live, `origin` (`fresh` or `cache`: then `collected_at` is the date of the cached data, up to 24 h old); `failed` keywords make the answer `partial` and are not charged. Titles of third-party results only in `external_sources` (`type` serp_result, `url`, `title`, `collected_at`). A client check goes to the client's history. Repeating a finished check returns the cached data (free if already yours); only a request still running answers `partial` "richiesta identica". |
| `seocli:check_geo` | `keyword`, `country`, `language`, `device`, `client_id` **or** `domain`, `engines` (`ai_overview` default, `chatgpt`, `gemini`, `perplexity`, `claude`), `question` (a natural question in the user's language that you write and show the user first; default: the keyword), `samples` (1-30, default 10), `refresh` (skip the cache), `max_credits` | **paid** per sample and per engine: AI Overview 8, ChatGPT 30, Gemini 50, Perplexity 10, Claude 70 credits (estimate = samples × sum of the chosen engines; 10 samples on all four AI engines = 1,600, above the default ceiling of 500: always show it and pass `max_credits`); only successful samples are charged. Shared 7-day lot cache: samples already paid by this account are free; samples from the cache paid by others and new samples cost 8 each; `refresh` reads all samples fresh | Background: returns an operation id at once; read the result with `seocli:get_operation` (samples take 10-45 s each, run in parallel). Engines run in the same operation; only the question reaches them (never the site or client data). Client checks go to the client's GEO history. |
| `seocli:get_visibility_history` | `client_id`, `keyword` (optional), `kind` (`serp`, `geo`, `all`), `from`/`to` (`YYYY-MM-DD`, default last 90 days) | free | Client history only (pre-sales checks are never stored). `series[]`: SERP per keyword (points with position or none, URL; changes `rise`, `fall`, `entered`, `left`) and GEO per engine and question (one point per check with samples, rate and Wilson interval; changes `new_citation`, `lost_citation`, `insufficient_sample`). Each series has `chart` (`title`, `unit`, `inverted_axis`): draw it (see `SKILL.md`). `truncated` when a series exceeds 500 points. |
| `seocli:get_operation` | `id` | free | `status` (queued, running, completed, partial, failed), `kind`, `client_id`, `cost`, `outcome`, timestamps. For a finished GEO check also `result`: per engine (`engines[]`, each with `search_activated`: an engine that answered without searching the web is a valid outcome), samples, AI Overview presence, `cited` and `mentioned` as `{count, total, rate, low, high}` (Wilson 95% interval), `ai_overview_absent` (Google showed no AI Overview in any sample: say so, do not suggest more samples), `insufficient_sample` (AI Overview seen but fewer than 10 successful samples and no citation: never say "not cited"), an approximation note, `fresh_samples` / `cache_samples` and `oldest_sample_at` (say how old the cached samples are); cited sources in `external_sources` with how many samples cited them. |

Price-list codes today: `serp_queued`, `serp_live`, `geo`, `keyword_research`, `page_crawl`,
`page_crawl_rendered`, `html_check`, `pagespeed`, `search_console`, `analytics`. Always read the live
list; a code in the list does not mean the tool that uses it already exists.

## Planned (do not call until the live tool list contains them)

| Tool | Planned in | Capability in plain words |
|---|---|---|
| `seocli:delete_account` | E3.5 | delete the account and its data |
| `seocli:manage_monitors` | E4.1 | scheduled monitors |
| `seocli:get_summary` | E4.3 | signals and summary by severity |
| `seocli:research_keywords` | E5.1 | keyword research |
| `seocli:select_keywords` | E5.2 | keyword shortlist |
| `seocli:find_competitors`, `seocli:compare_competitor` | E5.3 | competitors on shared keywords |
| `seocli:crawl_site` | E6.1 | site crawl |
| `seocli:get_audit_issues` | E6.3 | audit issues per page and across pages |
| `seocli:check_performance` | E6.5 | speed and Core Web Vitals |
| `seocli:recheck_urls` | E6.6 | targeted re-check after fixes |
| `seocli:check_page_html` | E6.7 | page analysis of unpublished HTML |
| `seocli:manage_google` | E7.1 | connect the user's Google account |
| `seocli:get_search_console_data`, `seocli:find_cannibalization`, `seocli:inspect_indexing` | E7.4 | Search Console data |
| `seocli:get_analytics_data` | E7.5 | GA4 data |
| `seocli:create_baseline` | E7.7 | client starting snapshot |
| `seocli:manage_branding` | E8.1 | agency branding for reports |
| `seocli:get_report_data` | E8.2 | report data |
| `seocli:set_billing_details`, `seocli:buy_credits`, `seocli:list_invoices` | E9.1, E9.4, E9.5 | billing and top-ups |

No tool exists, and none is planned, for Google Ads, Merchant Center, Meta Ads or backlinks. Say so
plainly when asked; do not accept pasted exports as a substitute data source in this version.

## Sources

- seocli-api v0.9.0 MCP server (tool descriptions and schemas) and its planning epics, read 2026-10-02.
