last_verified: 2026-11-09
volatile: true

# Errors

A failed call is a tool result with `isError` and `{error:{type, message, ...}}`. `message` is Italian.

| `type` | Extra fields | Behaviour |
|---|---|---|
| `invalid_input` | none | Fix the parameter the message names. Ask the user when ambiguous; never guess silently. |
| `not_found` | none | Wrong id, or another account's resource. List clients or operations again. |
| `unauthorized` | none | Account pending or login missing: relay the message and explain `/mcp` login or activation. |
| `insufficient_credits` | `balance`, `estimated`, `missing` | Stop. Show the three numbers. Mention top-up only once a top-up tool exists. Never retry. Then ask with AskUserQuestion: reduce scope / continue without the paid part / stop. |
| `limit_exceeded` (spend) | `estimated`, `max_credits` | Show the estimate and ask with AskUserQuestion (raise to the estimate / reduce scope / stop). Relaunch with `max_credits` at least the estimate only after a yes. |
| `limit_exceeded` (rate) | `retry_after_seconds` | Wait or tell the user. Never loop. |
| `service_unavailable` | none | No charge. For free tools retry once after a pause; for paid tools ask first. |
| `failed_operation` | none | No charge. Report the outcome. No automatic paid retry. |
| `profile_required` | `profile_url` | The account data (contact and billing) are missing. Give the user the `profile_url`: the form is filled once in the seocli dashboard, never in the chat (never ask for tax code, VAT number or address here). Every tool except `get_status` answers this way until then, with no charge. Do not retry until the user says the form is done. |
| any type with `code` | `code`, `dashboard_url` | The fix is in the seocli dashboard, not in the chat and not a login problem: `google_not_connected`, `google_reconnect`, `search_console_permission_missing` point to the dashboard page Google; `search_console_not_associated`, `search_console_site_not_accessible` point to the client settings. Give the user `dashboard_url`; never retry until the user says it is fixed. |

The two `limit_exceeded` cases are told apart by the extra fields: `max_credits` present means spend,
`retry_after_seconds` present means rate.

## Sources

- seocli-api v0.4.0 error type definitions, read 2026-10-02.
- seocli-api `get_search_console_data` errors with `code` and `dashboard_url` (story 7.4), read 2026-11-09.
