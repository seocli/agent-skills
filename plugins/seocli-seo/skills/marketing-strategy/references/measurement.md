last_verified: 2026-10-02
volatile: false

# Measurement

## Ladder: use the rung that matches budget and decision risk

| Rung | Method | Cost | Bias | Fit |
|---|---|---|---|---|
| 1 | Platform-reported conversions | free | high (self-attribution, view-through, modelled conversions) | Optimisation signal, direction |
| 2 | Analytics plus CRM matching, strict UTM convention | low | medium (consent loss, last-click bias) | Baseline for small clients |
| 3 | Blended: MER (total revenue / total marketing spend), CAC, payback, contribution margin | low | low if revenue data is right | Weekly and monthly "is spend working overall" |
| 4 | Simple causal tests: holdout, geo split, pause and resume with a control series, post-purchase survey | low-medium | low-medium | Verify a channel's incremental value |
| 5 | Platform lift studies | free-ish, spend thresholds | low | Larger budgets; often out of reach for small accounts |
| 6 | MMM calibrated with experiments | 2+ years of weekly data, several channels with real variation, analyst time | model- and prior-dependent | Large budgets only |

## MMM reality `[G]` for tool behaviour, `[H]` for applicability

- Meridian (open source) supports calibrating ROI priors with incrementality experiments and outputs
  a per-channel calibration score telling which channels most need an experiment (S1). Its geo
  experiment tool is separate.
- Robyn (open source, semi-automated) uses ridge regression and hyperparameter optimisation and can
  calibrate the model against ground-truth experiments such as geo tests and lift studies (S2).
- Take-away: MMM is not causal truth by itself; estimates depend on priors and data variation, and
  experiments anchor them.
- Applicability `[H]`: MMM generally needs hundreds of thousands to millions of euro of annual media
  across several varying channels. For most Italian small businesses skip it, use rungs 3-4. For the
  few large e-commerce or retail clients, recommend a freelance or in-house data specialist.

## Attribution pitfalls

- Platform numbers overlap: two platforms claim the same sale. Reconcile against bank, CRM or shop
  totals before reporting.
- Last-click undercounts upper funnel and social; view-through overcounts retargeting. Neither is
  truth.
- Consent loss: with Consent Mode, conversions are partly modelled. Check the real consent rate;
  it varies widely with banner design `[H]`.
- Offline conversions (phone, WhatsApp, in-store) are invisible by default and may be most of a
  local business's leads: call tracking, WhatsApp click events, offline conversion import, a code
  or coupon, or asking at the counter.
- Branded search hides other channels (a social ad is seen, then the brand is searched).

## Incrementality tests an agency can run

1. Geo holdout: pause ads in comparable provinces for 3-4 weeks, compare with control provinces
   (difference of differences). Provinces differ a lot in size; use matched pairs with enough volume.
2. Brand-search pause: pause the branded campaign by half the regions or days and compare total
   (paid plus organic) brand conversions. Results depend on competitor presence; test per client.
3. Platform holdout (lift study) when spend thresholds allow.
4. Time-based on/off: crude and seasonal; only with a control series.
5. Post-purchase "how did you hear about us" (free text plus options): cheap, catches word of mouth
   and dark social.

Decision rule `[H]`: run a test when monthly channel spend times the suspected misallocated share
exceeds the test cost. Example: 4,000 euro/month on brand search, half suspected cannibalised, a
two-week pause costs near nothing: run it.

Small samples: under about 30 conversions per variant per month, A/B tests are underpowered. Use
bigger changes, longer windows, or judgement labelled as such. Cover one full business cycle.

## Sources

- S1. Meridian, channel recommendation and calibration: https://developers.google.com/meridian/docs/post-modeling/channel-recommendation (accessed 2026-10-02).
- S2. Robyn documentation: https://facebookexperimental.github.io/Robyn/ (accessed 2026-10-02). The fetched page names ridge regression, hyperparameter optimisation and calibration against geo, lift and attribution ground truth; adstock and saturation are not on that page (read them on the project repository before citing).
