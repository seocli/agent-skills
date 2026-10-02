last_verified: 2026-10-02
volatile: false

# Rubric: scoring the four fields (0-2 each, reject below 6 of 8)

| Field | 0 | 1 | 2 |
|---|---|---|---|
| observation | no number, or no source | number without window or sample size | number with tool, period, n, provenance, and a comparison (prior period or expectation) |
| dependency | absent | named but not ordered or not checkable | ordered, each item checkable, says what the action unblocks |
| falsifier | "will improve" | metric and window only | metric, scope, control, window, threshold and a pre-committed action on failure |
| leading | absent or same as the outcome | early signal without a threshold | earlier than the outcome, different from it, with source, direction, threshold, time |

## Worked example (illustrative numbers)

Recommendation: rewrite the titles of 48 category pages that rank 1-3 but have a CTR below the site curve.

- observation (2): "48 pages, 120k impressions in 90 days (Search Console, final data), average position 2.4,
  CTR 3.1% against 8.9% for the site at position 2-3."
- dependency (2): "Titles editable via the template (2 dev-days); no competing URL for the same queries
  (cannibalisation check done); SERP features unchanged during the test."
- falsifier (2): "24 treated vs 24 control pages, 4 weeks, clicks per page; reject if the lift is below +5%
  or the interval includes zero; on rejection revert titles and stop title work on this template."
- leading (2): "CTR of the page-query pair at stable position rises by 1 point within 14 days on the treated arm."

Total 8 of 8. Removing the control group from the falsifier drops it to 1; the item scores 7 and still ships,
with the weakness named. Removing the threshold and the failure action drops it to 0 or 1.

## Sources

- Project method definition: docs/DESIGN.md sections 4.3 and research/seo/analyst-methods.md section 14 (internal).
