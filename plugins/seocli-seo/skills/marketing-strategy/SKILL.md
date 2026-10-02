---
name: marketing-strategy
description: Strategist knowledge for Italian agency work - the diagnosis loop, marketing-science priors with their caveats, the measurement ladder, unit economics, channel mix and budget rules for small budgets, KPI trees, hypothesis cards with kill and scale rules, decision log, client lifecycle, document templates and Italian market and compliance notes. Knowledge for the marketing strategist; not a command.
user-invocable: false
---

# marketing-strategy

## When to use

Load it when the task is a choice across channels or over time: what is the client's binding
constraint, how to split a budget, how to measure whether a channel works, what to put in a
strategy, a quarterly plan or a monthly report. It does not decide keyword-level SEO, bids or
creative: use `google-ads` and `meta-ads` for platform detail, `search-analytics` and
`technical-seo` for organic detail, `geo-visibility` for AI answers, and `methodology` for the
four phases and the four fields this skill builds on.

## Rules

Source ids refer to `## Sources` in the named reference. Tags: `[G]` primary, verified 2026-10-02.

1. **Strategy = diagnosis, guiding policy, coherent actions, and an explicit "not doing" list.** A
   plan that gives every channel equal priority is a wish list. Plugin convention.
2. **Every bet is a hypothesis card with the kill and scale rules written before data is seen**
   (`references/hypotheses.md`). Plugin convention, from `methodology` rule 5.
3. **Platform-reported conversions are a signal, not a measurement of incremental sales.** Two
   platforms can claim the same sale, so the sum of platform conversions is reconciled against the
   bank, CRM or shop total before it is reported. Plugin convention.
4. **Experiments anchor causal claims, including inside a marketing mix model.** Open-source MMM
   tools document calibration of model estimates with incrementality or lift experiments, and use a
   per-channel score to decide which channel to test first. `[G]` Meridian docs, Robyn docs
   (`references/measurement.md` S1, S2).
5. **Break-even ROAS = 1 / gross margin share; break-even CPA = margin per sale.** Arithmetic,
   stated with the margin it assumes (`references/economics.md`).
6. **Italian micro-firms (up to 9 employees) are 94.9% of firms, 27.2% of value added and 42.3% of
   employment (2022).** `[G]` Istat Annuario 2025 (`references/italy.md` S1).
7. **Non-technical cookies and trackers need valid consent in Italy.** Scrolling is not consent;
   cookie walls are generally prohibited; a declined choice is not re-asked for at least six months
   unless conditions change. `[G]` Garante cookie guidelines 2021 (`references/italy.md` S2). Flag
   the risk and send the client to its legal advisor; never give legal advice.
8. **Ranges, not points.** Forecasts carry conservative, base and optimistic cases with the
   assumption that moves them most. Never promise a rank, a ROAS or a date. Plugin convention.
9. **Facts from the evidence pack only.** Missing inputs go to NEEDS, unknowns are `n/d`. Pinned
   sentences in the persona apply; this skill supplies method, not numbers.

## Heuristics

Defaults, `[H]`, overridable per client. Ask for the client's own data before using any of them.

- **Strategist loop:** PERCEIVE (mark each fact known, assumed or unknown) -> ANALYZE (one binding
  constraint: no demand, weak offer, weak conversion, weak capture, broken measurement, capacity,
  cash) -> VALIDATE (the cheapest test that could prove the diagnosis wrong) -> ACT (plan, leading
  indicators, review date). Detail: `references/strategist.md`.
- **Priors, not laws.** 60/40 brand to activation and 95-5 out-of-market are directional priors
  from self-selected awards data and an extrapolation; never quote them to a small client as a
  formula. Use the client's own purchase cycle: in-market share is roughly months actively
  searching divided by months between purchases. `references/priors.md`.
- **Capture vs creation.** Search mostly captures existing demand. If impression share on core
  non-brand terms is above about 70-80%, more search budget has diminishing returns; growth must come
  from creation, new queries or new geography.
- **Measurement ladder:** platform numbers, then analytics plus CRM, then blended MER, then
  holdouts and geo tests, then platform lift studies, then MMM. Most Italian small businesses live on
  rungs 1-4. MMM is unrealistic for most (needs long weekly history, several channels with real
  variation, an analyst); mention it only for the few large e-commerce or retail clients and
  recommend a data specialist.
- **Small budgets:** under about 3,000 euro/month of media, at most 2 active paid channels,
  consolidated campaigns, one major change per test window. Reallocate 10-20% a month toward the
  best marginal channel; bigger jumps break learning and confound tests.
- **Decision tree for the first recommendation:** tracking unreliable -> fix it before scaling;
  offer or conversion weak -> fix before buying traffic; no demand -> creation or new geography;
  capture saturated -> creation, new queries, retention; else scale capture and keep a creation
  slice. Capacity and cash checked before any scale-up.
- **Small samples:** below about 30 conversions per variant per month an A/B test is underpowered;
  use larger changes, longer windows or judgement, and say so.
- **Reporting:** lead with decisions; what we said would happen, what happened, what we now
  believe, what we do next. 2-4 pages, in Italian for the client.
- **Specialist conflicts:** compare on incremental gross profit per euro and per week of effort,
  with time to first signal; log the disagreement and a revisit date, do not erase it.

## Do not recommend

- Applying 60/40 or 95-5 mechanically, or presenting either as a law.
- Judging SEO, brand or creation work on 30-day last-click, or Meta on last-click analytics alone.
- Summing conversions across platforms into one client-facing total.
- MMM for a client without years of weekly multi-channel data.
- More than two paid channels on a media budget under about 3,000 euro/month.
- Scaling spend before tracking, offer and landing page are fixed.
- Promising rankings, ROAS or lead volumes; quoting a benchmark as the client's expected result.
- A strategy with no "not doing" list, or hypotheses without a kill rule fixed in advance.
- Legal conclusions on cookies, claims or sector advertising: route to the client's legal advisor.

## Italian market notes

- Most clients are owner-run micro-firms: budgets often a few hundred to a few thousand euro a
  month, the owner decides, trust, reviews and word of mouth weigh heavily. Offline counts.
- WhatsApp is a normal business channel: track click-to-chat as a conversion and agree rules for
  any client group chat. Phone calls and walk-ins are often most of the leads; map every source.
- Invoicing is fattura elettronica; B2B payment terms of 30, 60 or 90 days strain cash, so check
  working capital before ramping spend. Agency fee and media are separate lines, media paid by
  the client to the platform.
- Consent rate drives how much conversion data exists; check the banner and Consent Mode before
  trusting any number. Verify cookie, analytics-transfer and sector-advertising questions with the
  client's legal advisor.
- Seasonality: judge tests against the same period last year, never across Ferragosto, late
  December, saldi or Black Friday. Regulated sectors (health, law, finance, gambling) need a
  claims gate before launch.

## References

Read on demand.

- `references/strategist.md` - role, loop, specialist debate protocol, handoff questions, mistakes.
- `references/priors.md` - Sharp, Binet and Field, 95-5, messy middle, positioning, Bullseye, with caveats.
- `references/measurement.md` - ladder, attribution pitfalls, incrementality tests, MMM reality.
- `references/economics.md` - unit economics, KPI trees, funnel map, channel mix and budget rules, forecasting.
- `references/hypotheses.md` - hypothesis card, kill and scale rules, decision log, review cadence.
- `references/lifecycle.md` - pre-sales, onboarding, strategy, execution, monitoring, reporting, churn signals.
- `references/templates.md` - strategy document, quarterly plan, monthly report.
- `references/italy.md` - business landscape, channels, consent and advertising regulation, agency practice.
