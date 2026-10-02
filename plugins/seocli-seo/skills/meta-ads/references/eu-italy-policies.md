last_verified: 2026-10-02
volatile: true

# Meta: EU and Italian rules, special categories, policies

Not legal advice: for any lawfulness question, tell the user to verify with the client's legal adviser.
Tags: `[G]` re-verified (see `sources.md`), `[H]`, `[U]` reported.

## Consent and privacy `[U]`

- GDPR and ePrivacy: consent via a CMP before tracking tags fire. The Italian data-protection
  authority's 2021 cookie guidance asks for a reject option as prominent as accept, no cookie walls,
  and renewed consent after at most about six months.
- Advertisers warrant a lawful basis for data sent through business tools and custom audiences.
- Transfers rest on the EU-US framework plus contractual safeguards; challenges are ongoing. Keep
  the data processing agreement and safeguards documented.
- Sensitive categories (health, orientation, religion, politics, union) cannot be used for targeting
  or signals; detailed targeting is limited in the EU; under-18s are limited to age and location, and
  the DSA bars personalised ads to minors.

## "Less personalised ads" and political ads `[U]`

- Following the Digital Markets Act decision on the pay-or-consent model, Meta reportedly committed to
  a choice between fully personalised and limited-data ads for EU users, rolled out from January 2026.
  Expect more variance, weaker optimisation signals and a stronger case for first-party data, server
  event quality and creative-led targeting. A per-ad split is not known to be exposed; monitor.
- Meta reportedly stopped delivering political, electoral and social-issue ads in the EU from October
  2025 (Regulation (EU) 2024/900). Not verified from a primary page. For NGOs, parties and public bodies
  plan other channels and check Meta's current policy before promising delivery.

## Special ad categories

- Declared at campaign creation; documented values: `HOUSING`, `FINANCIAL_PRODUCTS_SERVICES`,
  `EMPLOYMENT`, `ISSUES_ELECTIONS_POLITICS`, `NONE` `[G]` meta-sac. A gambling-and-gaming value
  mentioned in earlier notes is not on that page: do not rely on it.
- Wrong or missing declaration leads to rejection or restrictions. In the US and some countries
  targeting by age, gender or postcode and lookalikes are disabled for these categories `[U]`.
- Italy: the Decreto Dignita (2018) bans most gambling advertising; licensed operators still face very
  strict limits. Financial products need Meta's financial-services verification with proof of
  authorisation (Bank of Italy, Consob, IVASS registers; accepted proofs to be confirmed) `[U]`.

## Other Italian and EU rules `[U]`

- Consumer prices shown with IVA; crossed-out prices must be real and respect the Omnibus rule
  (show the lowest price of the previous 30 days).
- Influencer and branded content: disclosure (advertising self-regulation code, competition and
  consumer authority, communications regulator guidance); use the paid partnership label; secure
  usage rights from creators.
- Health, nutrition and cosmetics claims follow EU and national health rules; weight-loss and supplement limits.
- Age-restricted goods: alcohol with age gating; tobacco and vapes prohibited.
- DSA: public ad repository and transparency on why an ad is shown. The EU ad repository can be
  searched for competitor creative research, not for performance data.

## Policies and rejections

Governing documents: Meta Advertising Standards and commerce policies `[U]`.

Common rejection causes in Italian accounts:
- Personal attributes ("Hai il diabete?", "Sei in sovrappeso?"): rewrite neutral and benefit-first.
- Health and wellness claims, before/after images, miracle cures, disease claims for supplements.
- Misleading claims: fake countdowns, "gratis" when not free, exaggerated results.
- Landing page: broken, mismatched, non-working checkout, no privacy policy, auto-download, interstitials, unverified domain.
- Prohibited or restricted: gambling, alcohol (age gate), crypto (prior approval), adult, weapons,
  tobacco, unsafe supplements, income-promise schemes.
- Circumvention: cloaking, redirect chains, near-duplicate resubmission after rejection.
- Intellectual property: unauthorised logos, counterfeit suspicion, celebrity likeness (strictly enforced).
- Account restrictions: payment failure, unusual spend, policy strikes, identity or business verification needed.

Process `[H]`:
1. Read effective status, review feedback and account quality.
2. Edit the ad to fix the cause rather than resubmit; appeal only when you believe it is a mistake.
3. Do not create near-identical ads while one is in appeal; keep a rejection log per client.
4. Protect against lockout: domain verification, two-factor authentication, at least two admins;
   never circumvent.
5. Overlay: Italian advertising self-regulation code, health and nutrition claims, influencer disclosure.

Account states worth reading when data exists: active, disabled, unsettled, pending risk review,
grace period, pending closure, closed `[U]`.

## Sources

See `sources.md` (id meta-sac; others unverified).
