last_verified: 2026-10-02
volatile: true

# Operations, cost and cache

## Background operations

Long or paid work becomes an operation. A launching tool returns its `operation_id`, a `status`,
`reused` and `balance` (`included`, `topup`, `total` right after the reservation, or the current
balance when reused): show the remaining credits without calling `seocli:get_credit_balance` again. The final result is read with `seocli:get_operation`.

- Statuses: `queued`, `running`, `completed`, `partial`, `failed`.
- `partial`: present what exists, with the reason, and list the rest under NOT ASSESSED. The charge is proportional.
- `failed`: no charge. Report the outcome; do not relaunch paid work without asking.
- Polling schedule and limit: see the Heuristics of `seocli-tools`. When the turn ends with the work
  still running, write the id to `operations.md` with the date and purpose.
- When the client supports MCP tasks, a task id equals the operation id; polling is then optional.

## Cost fields

`cost.estimated` is the server's estimate at launch, `cost.reserved` the amount held, `cost.charged`
the final amount. Until the operation completes, `charged` is not final. A launch that exceeds
`max_credits` is refused before anything is reserved.

## Reuse and cache

- `reused: true` with a `notice`: the same request in the last 10 minutes; nothing new is charged. Ask with AskUserQuestion: keep the existing result (recommended) / relaunch with different parameters.
- Cached results (planned for SERP and AI-answer checks): show the data date and the word "cache". The
  price is reduced when served from the cache (5% if another account paid, nothing if this account
  already did). Offer a forced refresh at full price only when freshness changes the decision.
  These tools are not available yet; treat this as the expected behaviour, not a promise.

## Sources

- seocli-api v0.4.0 operation model and planning epics (stories 2.2, 2.3, 3.1), read 2026-10-02.
