last_verified: 2026-10-02
volatile: true

# Tool map (server v0.5.0)

The machine-readable copy is `tests/contract/tools.json` in the plugin repository; the lint keeps the
two consistent with every file of the plugin. Status "now" means exposed by server v0.5.0.

## Available now

| Tool | Parameters | Cost | Use |
|---|---|---|---|
| `seocli:get_status` | none | free | Service name and version; session check. A `unauthorized` error means the account is pending or not logged in. |
| `seocli:manage_clients` | `action`: create, list, get, rename, archive, restore, delete; `id`, `domain`, `name`, `status` (list filter) | free | Clients are the sites the account follows. `create` needs `domain` (name defaults to the domain); `get`, `rename`, `archive`, `restore`, `delete` need `id`. `delete` is irreversible. |
| `seocli:get_credit_balance` | none | free | `included` (this period), `topup`, `total`. |
| `seocli:list_credit_movements` | `from`, `to` (`YYYY-MM-DD`, UTC, inclusive; default: start of the current credit period to today), `client_id` (uuid or `none`), `limit` (1-500, default 100), `cursor` | free | Credit ledger, newest first: `movements[]` with `kind`, signed `credits` (balance change) and `amount`; `summary[]` per client with `charged` over the whole period; `next_cursor` until the last page. A reservation subtracts the estimate, a release returns the unused part, a confirmation records the charge in `amount` with 0 change. |
| `seocli:get_price_list` | none | free | `version` and `items[]` with `operation` code, `description`, `unit`, `credits`. |
| `seocli:get_operation` | `id` | free | `status` (queued, running, completed, partial, failed), `kind`, `client_id`, `cost`, `outcome`, timestamps. |

Price-list codes today: `serp_queued`, `serp_live`, `geo`, `keyword_research`, `page_crawl`,
`page_crawl_rendered`, `html_check`, `pagespeed`, `search_console`, `analytics`. Always read the live
list; a code in the list does not mean the tool that uses it already exists.

## Planned (do not call until the live tool list contains them)

| Tool | Planned in | Capability in plain words |
|---|---|---|
| `seocli:redeem_invite` | E2.6 | beta invite with free credits |
| `seocli:check_serp` | E3.1 | search results check for a keyword |
| `seocli:check_geo` | E3.2, E3.3 | presence in AI answers |
| `seocli:get_visibility_history` | E3.4 | visibility over time |
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

- seocli-api v0.5.0 MCP server (tool descriptions and schemas) and its planning epics, read 2026-10-02.
