last_verified: 2026-10-02
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
| `email_required` | none | `seocli:delete_account` needs an email address in the login profile to send the confirmation link. Relay the message; the user asks the seocli team to add the address or to handle the deletion by other means. Never retry. |

The two `limit_exceeded` cases are told apart by the extra fields: `max_credits` present means spend,
`retry_after_seconds` present means rate.

## Sources

- seocli-api v0.4.0 error type definitions, read 2026-10-02.
