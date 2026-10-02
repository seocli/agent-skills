# Generative Engine Optimization (GEO / AEO / LLMO): evidence review, 2026

Research input for the seocli Claude Code plugin rebuild (`agent-skills/plugins/seocli-seo`) and for the
seocli-api MCP tool design. Compiled 2026-10-02. All "accessed" dates below are 2026-10-02 unless stated.

## 0. How to read this document

### 0.1 Evidence labels

Every claim carries one of these labels. They describe the strength of the evidence, not whether the
claim is plausible.

| Label | Meaning |
|---|---|
| **[OFFICIAL]** | Statement by the platform operator in its own documentation. Authoritative about what the operator says it does or supports; says nothing about how well it works. |
| **[ACAD-PRIMARY]** | arXiv/conference abstract I read directly. Peer review status noted where known. I read abstracts and fetched summaries, not full papers (exception: the GEO paper, summary of full HTML). |
| **[IND-PRIMARY]** | Industry study from the party that ran it (Ahrefs, SparkToro, SE Ranking). Sample size stated when I could verify it. Vendor incentives apply. |
| **[SECONDARY]** | I only saw the claim through a search-engine summary or a third-party write-up; I did not open the original. Treat as a lead, re-verify before putting in a client deliverable. |
| **[WEAK]** | Marketing-blog assertion, single case, or numbers I could not trace to a method. Do not build tactics on it. |

Strength grades used in the evidence table: **A** = official statement or replicated/controlled evidence,
**B** = single large observational study or single controlled study, **C** = correlational vendor
study with unclear controls, **D** = anecdote or untraceable.

### 0.2 Caveats about the research process

- Fetches went through a summarizing model; numbers were taken from its output. Where a number matters
  for a decision, re-open the URL.
- The search tool is US-only. Italian-language sources are thin; Section 9 says so explicitly.
- Several arXiv IDs are from 2026 (2603.xxxxx to 2609.xxxxx). I verified title, author and abstract via
  arxiv.org/abs fetches on the access date. I did not read full texts, so methodological critiques here
  are limited to what abstracts disclose.
- "AI Mode", "AI Overviews", "ChatGPT search", "Copilot" change quickly. Anything dated before mid-2026
  may be stale; the per-claim date is given where known.

### 0.3 One-paragraph state of the field

Google says plainly that generative-AI optimization is SEO and that no special files, markup, chunking
or AI-specific rewriting is needed. Academic work says GEO gains reported in 2023 hold only when the
source is already in the retrieved context, are conditional on the engine and domain, and are fragile.
Industry correlational studies converge on: authority and third-party presence (brand mentions,
referring domains, earned media) correlate most strongly with being cited; freshness helps on ChatGPT
more than on Google; structure and extractable facts help once retrieved; llms.txt and schema show no
measurable citation lift in controlled or large-sample tests. The measurement problem is as important
as the tactics: AI answers are non-deterministic, so single-run rank or "was I cited" readings are
close to noise; only appearance rates over repeated, stratified samples with confidence intervals are
defensible.

---

## 1. Terminology and the pipeline model

### 1.1 Names

- **GEO** (Generative Engine Optimization): coined in the KDD 2024 paper (Aggarwal et al.); optimization
  of content for visibility inside LLM-synthesized answers. [ACAD-PRIMARY]
- **AEO** (Answer Engine Optimization): older term from voice/featured-snippet era; now used
  interchangeably with GEO by practitioners.
- **LLMO**: Japanese/European industry label; same scope, often broader (training-data presence).
- Google's position: "optimizing for generative AI search is optimizing for the search experience, and
  thus still SEO". [OFFICIAL, see 2.1]

Recommendation for seocli: use "AI visibility" in user-facing copy, keep "GEO" as the search keyword.
Do not promise a separate discipline; present it as an extension layer on SEO fundamentals.

### 1.2 The stochastic pipeline

A 2026 critical survey of 45 studies (Martinez, arXiv 2607.14035, submitted 2026-07-15) describes GEO as
"a stochastic, partially observable pipeline" with stages: search activation (does the engine search at
all?), crawling and indexing, retrieval, reranking and context allocation, citation, prominence,
factual absorption, fidelity, user behavior. [ACAD-PRIMARY, abstract only; unknown peer-review status]

Practical consequence: "I was not cited" can be a failure at any stage. A GEO diagnostic must locate the
stage:

| Stage | Question | Typical check |
|---|---|---|
| 0. Activation | Did the engine use web search for this prompt? | Look for citations / search-queries field in the answer; many ChatGPT answers use parametric memory only |
| 1. Crawl access | Is the engine's bot allowed and reachable? | robots.txt per bot, WAF, IP allowlist, server logs |
| 2. Index | Is the page indexed by the underlying index (Google, Bing, engine's own)? | Search Console, Bing Webmaster, `site:` |
| 3. Retrieval | Does the page rank for the engine's sub-queries (query fan-out)? | Capture fan-out queries where exposed; run them in classic SERP tool |
| 4. Selection | Is the page chosen among retrieved candidates? | Citation present or not, over N samples |
| 5. Absorption | Does the answer reuse the page's facts/wording? | Compare answer text to page; "citation absorption" |
| 6. Prominence/sentiment | Is the brand named, positioned, described accurately? | Mention extraction + sentiment/accuracy review |
| 7. Behavior | Do users click or convert? | Referral analytics (utm_source=chatgpt.com etc.), GSC/Bing AI reports |

The survey's headline: the most reproducible factors are topical relevance and context position, and
only already-retrieved content can causally improve its citation likelihood; "generic heuristics
transfer poorly" and "citation-oriented rewrites can impair retrieval". Source: https://arxiv.org/abs/2607.14035
(accessed 2026-10-02). Strength: B (survey, single author, heterogeneous underlying evidence).

### 1.3 Citation vs mention vs absorption

- **Citation**: a URL/domain attached to the answer as source. Observable in Perplexity, ChatGPT search,
  Gemini/AI Overviews/AI Mode, Copilot, Claude web search.
- **Mention**: brand/entity named in answer text, with or without link. Mentions can come from parametric
  memory with no retrieval at all.
- **Absorption**: the cited page actually contributes language, evidence or structure to the answer.
  Defined in arXiv 2604.25707 (submitted 2026-04-28): across 602 controlled prompts on ChatGPT, Google AI
  Overview/Gemini and Perplexity with 21,143 valid search-layer citations and 72 extracted features,
  Perplexity and Google cite more sources, but ChatGPT cites fewer with "substantially higher average
  citation influence". High-influence pages are longer, structured, semantically aligned and rich in
  definitions, numerical facts, comparisons and procedural steps. [ACAD-PRIMARY, abstract; not known to be
  peer reviewed] https://arxiv.org/abs/2604.25707 (accessed 2026-10-02). Strength: B.

An MCP server must keep these three separate (Section 8).

---

## 2. Official platform guidance

### 2.1 Google (AI Overviews, AI Mode)

Primary pages fetched:
- https://developers.google.com/search/docs/appearance/ai-features (accessed 2026-10-02)
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide (page states "last
  updated 2026-07-10"; accessed 2026-10-02). Third-party coverage dates the first publication to
  2026-05-15 [SECONDARY, previsible.io/gigazine].

Claims [OFFICIAL]:
1. No additional requirements to appear in AI Overviews or AI Mode, "nor other special optimizations
   necessary". Page must be indexed and eligible to show a snippet.
2. Best practice SEO "continues to be relevant" because the features use core ranking systems with
   retrieval-augmented generation and **query fan-out**.
3. Do not need: new machine-readable files, AI text files (llms.txt), special markup, Markdown; such
   files "will neither harm nor help" visibility. (Same page in guide form.)
4. Do not need to chunk content into tiny pieces; systems understand multi-topic pages.
5. Do not need to write in a special way for generative AI; synonyms/intent are understood.
6. Seeking inauthentic "mentions" across the web is not effective (spam systems).
7. Structured data is not required for generative AI search; keep using it for normal SEO as long as it
   matches visible content.
8. Creating many variants primarily to manipulate = scaled content abuse policy.
9. Content should be unique, "non-commodity", people-first; include high quality images/video.
10. Controls: `nosnippet`, `data-nosnippet`, `max-snippet`, `noindex`. Google-Extended is the AI-training
    control and "does not impact a site's inclusion in Google Search nor is it used as a ranking signal".
    Source: https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
    (accessed 2026-10-02). Google-Extended governs Gemini training and grounding in Gemini Apps and Vertex AI
    API for Gemini; blocking it does not remove you from AI Overviews.
11. Measurement: appearances in AI features are counted in the Search Console Performance report under the
    "Web" search type (ai-features page). Third-party coverage reports a dedicated "Search generative AI
    performance report" launched 2026-06-03 to a subset of sites and rolled out more widely by 2026-08-31,
    plus a control toggle to block content from generative AI search features [SECONDARY: techwyse.com,
    previsible.io, feedbagel.com; I could not confirm on a Google primary page, and the guide fetch said
    "Generative AI performance report"]. Re-verify in Search Console before documenting as a feature.

Caveat on reading Google's statements: they describe Google's own surface. They do not cover ChatGPT,
Perplexity, Claude, Copilot. They are also self-reported; independent tests (Section 4.6, 5) are
consistent on llms.txt and mostly consistent on schema.

Italian availability: AI Overviews launched in Italian for Italy on 26 March (year not stated in the
snippet I saw; Italian news coverage places it 2025) and AI Mode in Italian around 8 October 2025
[SECONDARY: italiaoggi.it, tg24.sky.it/tecnologia/2025/10/08/ai-mode-google-italia, smartworld.it].
Accessed 2026-10-02. Strength: B for dates (multiple Italian outlets), not verified on Google primary.

### 2.2 OpenAI (ChatGPT search)

Source: https://developers.openai.com/api/docs/bots (redirect from platform.openai.com/docs/bots; accessed
2026-10-02). [OFFICIAL]

| Bot | Purpose | robots.txt |
|---|---|---|
| OAI-SearchBot | Surface sites in ChatGPT search features | Allow it to be included in ChatGPT search answers; changes take ~24 h to apply |
| GPTBot | Training of foundation models | Disallow to opt out of training; unrelated to search inclusion |
| ChatGPT-User | User-initiated fetches (ChatGPT, Custom GPTs) | "robots.txt rules may not apply"; not used for search inclusion |

- To appear in ChatGPT search: allow OAI-SearchBot and allow requests from the published IP ranges
  (https://openai.com/searchbot.json).
- Each bot is independent: blocking GPTBot does not remove you from ChatGPT search.
- Implication for audits: robots.txt rules need a per-bot matrix, not a single "AI bots blocked" flag.

Not found in official docs: any statement of ranking factors for ChatGPT search. Anything claiming "how
ChatGPT ranks" comes from third-party reverse-engineering [SECONDARY/WEAK].

### 2.3 Perplexity

Source: https://docs.perplexity.ai/guides/bots (accessed 2026-10-02). [OFFICIAL]
- **PerplexityBot**: surfaces and links sites in Perplexity results; "not used to crawl content for AI
  foundation models". Respects robots.txt. IP list at perplexity.com/perplexitybot.json.
- **Perplexity-User**: user-triggered fetches; "generally ignores robots.txt" because a user requested it.
  IP list at perplexity.com/perplexity-user.json.
- Recommend combining User-Agent and IP verification in WAF rules.

Publisher program: launched 2024-07-30 with six partners (TIME, Der Spiegel, Fortune, Entrepreneur, Texas
Tribune, WordPress.com); Comet Plus announced 2025-08-25 at 5 USD/month with 80% of revenue to
publishers and a 42.5 million USD initial pool; payout "based on visits, citations and agent actions".
Contact publishers@perplexity.ai. [SECONDARY: websitebuilderexpert.com, tipranks.com, llmpulse.ai].
Accessed 2026-10-02. Strength: B for the facts, irrelevant to organic ranking: a program membership is
not shown to change citation probability (no evidence found).

### 2.4 Anthropic (Claude)

Source: https://support.claude.com/en/articles/8896518 (redirect from support.anthropic.com; accessed
2026-10-02). [OFFICIAL]
- **ClaudeBot**: collects web content that may contribute to training. Block with
  `User-agent: ClaudeBot` / `Disallow: /`.
- **Claude-User**: fetches pages when a user request in Claude needs it; blocking may reduce visibility in
  Claude's answers.
- **Claude-SearchBot**: crawls to improve Claude's search result quality; blocking may reduce visibility in
  Claude search.
- Respects robots.txt and Crawl-delay; blocking by IP is discouraged because it prevents robots.txt reads.
  Public IP list: https://claude.com/crawling/bots.json.

Not found: any ranking/citation criteria for Claude web search.

### 2.5 Microsoft (Bing, Copilot)

- Blog "Optimizing Your Content for Inclusion in AI Search Answers" (Oct 2025), hosted at
  preview-about.ads.microsoft.com/en/blog/post/october-2025/optimizing-your-content-for-inclusion-in-ai-search-answers.
  I saw only search-snippet summaries [SECONDARY]: AI systems break pages into sections and evaluate each
  for clarity, structure and ability to answer intent; use block-level structure, aligned titles/headings
  and schema, Q&A and short lists where useful; avoid hidden content, walls of text, PDFs for key info,
  image-only information. Accessed 2026-10-02. Contrast with Google's "do not chunk" statement: Microsoft
  openly recommends structure for extraction; not a contradiction in practice (clear structure is cheap and
  harmless) but the two vendors disagree on how much it matters.
- **Bing Webmaster Tools "AI Performance" report**, launched February 2026: daily citations and unique
  pages cited across Copilot, Bing AI summaries and partner integrations; page-level stats; "grounding
  queries" (the retrieval queries that triggered citations); CSV export only, no API as of the coverage; no
  CTR data. [SECONDARY: searchinfluence.com/blog/bing-ai-performance-report-copilot-citations, momenticmarketing.com,
  mean.ceo]. Accessed 2026-10-02. Strength: B. This is the only first-party citation dataset from a
  major engine I found; an MCP server should be able to ingest the CSV (Section 8).

### 2.6 Gemini (app)

Google-Extended governs whether crawled content may be used for Gemini model training and for grounding in
Gemini Apps / Vertex AI (Section 2.1). Gemini-app grounding with Google Search is subject to Search
eligibility. I found no official Gemini-specific ranking guidance beyond the AI-features pages.

### 2.7 Summary matrix: access controls

| Goal | Allow | Block / note |
|---|---|---|
| Appear in Google AI Overviews/AI Mode | Googlebot, indexable, snippet-eligible | `nosnippet`/`max-snippet` reduce use; Google-Extended irrelevant |
| Appear in ChatGPT search | OAI-SearchBot | GPTBot independent; ChatGPT-User may ignore robots.txt |
| Appear in Perplexity | PerplexityBot (+IPs) | Perplexity-User ignores robots.txt |
| Appear in Claude search | Claude-SearchBot, Claude-User | ClaudeBot = training only |
| Appear in Copilot/Bing | Bingbot, verified in Bing Webmaster | AI Performance report shows citations |
| Opt out of training only | Block GPTBot, ClaudeBot, Google-Extended | Does not remove from search surfaces |

All rows [OFFICIAL] except the Bing row [SECONDARY]. Accessed 2026-10-02.

---

## 3. Academic evidence

### 3.1 The founding paper: Aggarwal et al., "GEO: Generative Engine Optimization", KDD 2024

- arXiv 2311.09735. Abstract: black-box optimization framework; GEO "can boost visibility by up to 40% in
  generative engine responses"; efficacy varies across domains. https://arxiv.org/abs/2311.09735 (accessed
  2026-10-02). [ACAD-PRIMARY, peer reviewed (KDD 2024)]
- Details from the full HTML (via fetch summary, https://arxiv.org/html/2311.09735, accessed 2026-10-02):
  - **GEO-bench**: 10,000 queries across 25 domains, drawn from MS MARCO, ORCAS, Natural Questions and
    GPT-4-generated queries; 8K/1K/1K train/val/test.
  - **Nine methods**: Authoritative, Statistics Addition, Keyword Stuffing, Cite Sources, Quotation
    Addition, Easy-to-Understand, Fluency Optimization, Unique Words, Technical Terms.
  - **Metrics**: Position-Adjusted Word Count (share of answer text attributed to the source, decayed by
    citation position) and a Subjective Impression composite (relevance, influence, uniqueness,
    position, volume, click probability, diversity), the latter LLM-judged.
  - **Top results**: Quotation Addition about +41% on position-adjusted word count; Statistics Addition
    +31-37%; Cite Sources about +30%; Fluency Optimization +15-30%; Keyword Stuffing negligible.
  - **Distributional effect**: lower-ranked sources benefit more (one cited example: +115% for a source
    ranked fifth, while the top-ranked source lost about 30%, for the Cite Sources method).
  - Validation on Perplexity.ai: gains up to about 37% in subjective impression.
  - Stated limitations: engines evolve; query distribution shifts; no assessment of effects on classic
    search ranking.
- Critical reading (mine, based on the survey 2607.14035 and the abstract-level design):
  - The experimental unit is *a source already in the engine's context window*. The experiment measures how
    much of the generated answer is attributed to a rewritten source versus others in a fixed set of 5
    sources. It does not measure whether rewriting makes a page get retrieved. The 2026 survey says exactly
    this: gains "are valid within its experimental setting but conditional on a source already being
    present in a fixed context" [ACAD-PRIMARY].
  - The generative engine in the main experiments was a research-built pipeline over GPT-style models, with
    Perplexity as an external check. Production engines add retrieval, reranking, safety and personalization.
  - The "subjective impression" metric is LLM-judged, a known source of bias.
  - Quotation/statistics additions in the benchmark are LLM-inserted; real-world equivalents must be true.
    Inserting fabricated statistics is both unethical and a spam/misinformation risk (Section 6).
- Strength for tactics: **B** as a mechanism signal (adding verifiable stats, quotes and citations makes a
  retrieved passage more reusable); **C** as a promise of "+40% visibility" for a live site.

### 3.2 Which sources AI search cites: Chen, Wang, Chen, Koudas (arXiv 2509.08919, 2025-09-10)

- "Generative Engine Optimization: How to Dominate AI Search". https://arxiv.org/abs/2509.08919
  (accessed 2026-10-02). [ACAD-PRIMARY, status unknown]
- Findings: AI search (ChatGPT, Perplexity, Gemini and similar) shows "systematic and overwhelming" bias to
  **earned media** (third-party authoritative sources) over brand-owned and social content, in contrast to
  Google's more balanced mix. Engines differ in domain diversity, freshness, cross-language stability and
  sensitivity to phrasing. Recommendations: machine-readable and justifiable content, earned-media
  authority, engine- and language-specific strategies, mitigating "big brand bias".
- I did not see numeric effect sizes in the abstract; the search summary I saw is consistent with the
  abstract. Strength: B (observational, multi-engine, includes cross-language tests in Chinese, Japanese,
  German, French, Spanish vs English per a secondary summary; Italian not in the list) [SECONDARY for the
  language list].

### 3.3 Structure as a citation driver

- arXiv 2603.29979 (2026-03-31), "Structural Feature Engineering for Generative Engine Optimization": three
  levels (document architecture, information organization, visual formatting); across six generative
  engines, reported +17.3% citation rate and +18.5% quality metric. https://arxiv.org/abs/2603.29979
  (accessed 2026-10-02). [ACAD-PRIMARY abstract; effect-size base unknown; likely an author-built testbed]. Strength: C-B.
- arXiv 2604.19113 (2026-04-21), "Think Before Writing" (FeatGEO): feature-level optimization (structural,
  content, linguistic properties); consistent citation visibility gains across engines and model sizes;
  "document-level content properties have stronger influence on citation behavior than isolated lexical
  modifications". https://arxiv.org/abs/2604.19113 (accessed 2026-10-02), accepted to ACL 2026 per the
  ACL Anthology listing seen in search results [SECONDARY for the venue]. Strength: B for the direction
  (structure > wording tricks), C for magnitudes.
- arXiv 2604.25707: high-absorption pages are longer, structured and fact-dense (Section 1.3). Strength: B.

### 3.4 Robustness, manipulation and defense

- arXiv 2605.29107 (2026-05-27), "GEO-Bench: Benchmarking Ranking Manipulation in Generative Engine
  Optimization": unified benchmark of black-box prompt attacks (TAP, Zero-Shot) and white-box attacks (STS,
  RAF, StealthRank) plus ten defenses over five datasets using Llama-3.1-8B-Instruct as ranker. Findings:
  effectiveness/stealth trade-off; black-box rewriting matches or beats gradient attacks for rank
  promotion and yields more fluent text; black-box attacks evade keyword and perplexity detection on some
  domains. https://arxiv.org/abs/2605.29107 (accessed 2026-10-02). [ACAD-PRIMARY]. Note: this is a
  *different* "GEO-Bench" from Aggarwal et al.'s; disambiguate in tool names and docs.
- arXiv 2609.02964 (2026-09-02), "When Optimization Becomes Manipulation": "GEO Defender" (Shield Reranker +
  training-free Shield Generation) reduces attack success from 50.32% to 6.20% while retaining 94.12% of
  benign-evidence use; factually consistent manipulated documents evade fact-checking and perplexity
  filters. https://arxiv.org/abs/2609.02964 (accessed 2026-10-02). [ACAD-PRIMARY, very recent, not peer
  reviewed yet]. Implication: engines are actively building defenses; manipulation tactics have a shrinking
  half-life.
- Chen et al., "Unveiling the Resilience of LLM-Enhanced Search Engines against Black-Hat SEO
  Manipulation", WWW/The Web Conference 2026 [SECONDARY: named in a search result summary; not opened].

### 3.5 Measurement science

- arXiv 2603.08924, Sielinski, "Quantifying Uncertainty in AI Visibility" (submitted 2026-03, latest
  version 2026-08): repeated sampling over Perplexity, OpenAI SearchGPT and Gemini (daily collections
  over nine days plus 10-minute-interval sampling, consumer product topics); citation distributions are
  power-law; sample-to-sample variability is large; bootstrap confidence intervals show many reported
  domain-level differences fall within the noise floor; rankings unstable throughout the frequently cited
  set; argues citation visibility must be reported with uncertainty and gives sample-size guidance.
  https://arxiv.org/abs/2603.08924 (accessed 2026-10-02). [ACAD-PRIMARY]. Strength: B. This is the most
  directly useful paper for tool design.
- arXiv 2608.30052 (2026-08-30), Żatuchin: 234 controlled tests on ChatGPT and OpenAI API; the top
  recommendation changed across six identical runs on four of six prompts; **query language, not user
  location, selects which country's suppliers appear**; language and exit IP act as separable factors.
  https://arxiv.org/abs/2608.30052 (accessed 2026-10-02). [ACAD-PRIMARY, small, one author, one engine].
  Strength: C-B. Directly relevant to Italian (Section 9).

### 3.6 Multilingual bias

- arXiv 2509.13930, "Linguistic Nepotism" (ICML 2026 Spotlight): across eight languages and six open-weight
  models, models preferentially cite English sources for English queries, amplified for lower-resource
  languages and for documents in mid-context; models sometimes trade relevance for language preference.
  https://arxiv.org/abs/2509.13930 (accessed 2026-10-02). [ACAD-PRIMARY, peer reviewed]. Caveat: open-weight
  models in a controlled RAG setup, not production engines. Strength: B for the mechanism, C for transfer.
  The direction for Italian: Italian is a mid-resource language; expect a bias toward the query language
  when documents are available in it, and toward English when they are not.

### 3.7 University theses

I found Italian theses on GEO (Università di Pavia, thesis on strategies from SEO to GEO and risks of
automatic synthesis; Università Politecnica delle Marche, Computer Engineering, case studies for evaluating
generative tools; Politecnico di Torino webthesis 35594) via search listings only, not read:
- https://unitesi.unipv.it/retrieve/33a2506e-04cc-4b44-aa33-1f1552609643/TESI%20PER%20CONSEGNA1.pdf
- https://tesi.univpm.it/retrieve/f0d8d40b-3a7b-41a2-a80c-1253267f4fcc/Thesis_final1.pdf
- https://webthesis.biblio.polito.it/35594/1/tesi.pdf
[SECONDARY, unread]. Do not cite them for claims without opening them. Bachelor/master theses are weak
evidence by default (no peer review; small samples).

### 3.8 Academic evidence summary

1. Content-level edits that add verifiable evidence (quotes, stats, citations) increase how much of a
   retrieved page is reused; keyword stuffing does nothing [A-/B].
2. Optimization does not create retrieval. If the page is not in the candidate set, the edits are moot
   [B, survey].
3. Engines favor earned/third-party sources and differ strongly among themselves [B].
4. Outputs are stochastic and rankings are noise-dominated; appearance rate over repeated samples is
   the stable observable [B].
5. Manipulation research is active and defenses are improving [B, new].

---

## 4. Industry studies on what correlates with being cited

All entries are correlational unless stated. None can separate "cited because X" from "X and citations
share a cause" (large brands do everything well). I give the sample size from the source.

### 4.1 Brand mentions and authority

- **Ahrefs brand correlation study**, 75,000 brands, signals vs AI visibility across Google AI Overviews,
  and in a December 2025 follow-up also ChatGPT and AI Mode. Reported Spearman correlations: YouTube
  mentions about 0.737, branded web mentions about 0.664 (0.66-0.71 in follow-up), branded anchors 0.527,
  brand search volume 0.392, backlinks 0.218, content volume 0.194; top-quartile brands by mentions earned
  more than 10x the AI Overview citations of the next quartile; 26% of brands had zero AIO mentions.
  Original URL listed by Ahrefs results: https://ahrefs.com/blog/ai-brand-visibility-correlations (the
  fetch tool could not read it; figures come from secondary summaries:
  medium.com/@lorenzoswanson786, radiantelephant.com, thenextweb.com/news/ahrefs-youtube-mentions-ai-visibility-brand-search).
  Accessed 2026-10-02. [IND-PRIMARY, read via SECONDARY]. Strength: C-B. Caveat: the dependent variable is
  *brand mention/visibility*, not citation of a URL; brand-search volume and mentions are mutually
  endogenous; YouTube mention counts proxy for brand size.
- **SE Ranking**, 129,000 domains / 216,524 pages / 20 niches, ChatGPT citations: referring domains the
  strongest single predictor (sites up to 2,500 referring domains averaged 1.6-1.8 citations; over 350,000
  averaged 8.4; a jump near 32,000 referring domains from 2.9 to 5.6); traffic matters only above about
  190,000 monthly visits; domain trust scores 91-96 averaged 6 citations and 97-100 averaged 8.4.
  https://seranking.com/blog/chatgpt-citation-factors/ and SEJ coverage
  https://www.searchenginejournal.com/new-data-top-factors-influencing-chatgpt-citations/561954/ (accessed
  2026-10-02; numbers via search summary [SECONDARY]). Strength: C. Note the tension with Ahrefs (backlinks
  weak for mentions in AIO vs referring domains strongest for ChatGPT citations): different dependent
  variables, engines and samples. Both agree on "big, widely referenced domains win".
- **Chen et al. 2025** (3.2): earned-media bias, an academic observation consistent with the above.
- **Google's counter-statement**: seeking inauthentic mentions is not effective [OFFICIAL]. Reconcile:
  mentions that arise from real coverage correlate with visibility; manufactured mention campaigns are
  filterable on Google and show no demonstrated causal effect anywhere.

Recommendation: "be mentioned by third parties that are themselves retrievable" is the best-supported
off-page lever, but the causal evidence is thin. Present it to clients as "digital PR with measurable
mention counts" and track it, not as a guaranteed lever.

### 4.2 Freshness

- **Ahrefs**, 16.975 million cited URLs from Brand Radar across ChatGPT, Perplexity, Gemini, Copilot,
  Google AI Overviews and organic SERPs. Average days since publication: AIO top-3 1,432; organic SERP
  1,416; Perplexity 1,166; Gemini 1,118; Copilot 1,056; ChatGPT references 1,023; ChatGPT citations 958.
  Headline: AI assistants cite content about 25.7% newer than organic results; ChatGPT shows the
  strongest freshness bias; Google is least influenced. Caveats stated by Ahrefs: cited content is still
  on average 2.9 years old; quality matters more than update frequency.
  https://ahrefs.com/blog/do-ai-assistants-prefer-to-cite-fresh-content/ (fetched 2026-10-02).
  [IND-PRIMARY]. Strength: B for the descriptive finding (large N), C for causality ("update to get cited").
- Claims such as "content updated within 30 days gets 3.2x more citations" and "76.4% of ChatGPT's most
  cited pages were updated in the last 30 days" appeared in a search-result summary of other sites
  [WEAK; I could not trace them to Ahrefs' text, which says creating new content typically beats frequent
  updates]. Do not repeat these.
- Operational note: "last updated" signals can be gamed (changing dates without content changes). Google's
  policy view of fake freshness is negative [no source fetched; general SEO knowledge]. Update dates should
  reflect real content changes.

### 4.3 Structure and extractable facts

- 3.3 above (academic). Supported across arXiv 2604.25707, 2603.29979, 2604.19113.
- Microsoft recommends block-level structure [OFFICIAL, via SECONDARY]; Google says chunking not required
  [OFFICIAL]. Reasonable synthesis: write self-contained passages (definition first, numbers with units
  and dates, comparison tables, step lists) because retrieval operates on passages, but do not fragment pages
  artificially.
- Statistics, quotations and citations: Aggarwal et al. (3.1). Strength B (benchmark), real-world C.

### 4.4 Source-type mix

- Chen et al.: earned media dominant in AI engines (3.2).
- Reddit/Quora/YouTube prominence in AI citations is widely reported by vendors (SE Ranking title says
  brand mentions on Quora and Reddit matter; Ahrefs YouTube result). Share figures vary by month and engine
  and I did not verify any specific share [SECONDARY]. Treat as "UGC platforms are a large and volatile
  share of citations".

### 4.5 Engine differences

- ChatGPT: fewer sources, higher influence per source, strongest freshness bias [3.3, 4.2].
- Google AIO/AI Mode: more sources, more tolerance for older pages, drawn from core ranking [4.2, 2.1].
- Perplexity: many sources, ordering in-text references newer to older per Ahrefs [SECONDARY via search
  summary of the Ahrefs study].
- Conclusion: **do not report one blended "AI visibility score"** without per-engine breakouts.

### 4.6 llms.txt

Proposal: a Markdown file at `/llms.txt` that lists curated content for LLMs, proposed by Jeremy Howard
(Answer.AI) in September 2024 [background knowledge; proposal page https://llmstxt.org not re-fetched in
this session, treat as SECONDARY for specifics].

Evidence on adoption and effect (all through search summaries unless stated):
- Google: "You don't need to create new machine readable files, AI text files... will neither harm nor help"
  [OFFICIAL, fetched]. Reports that Chrome's Lighthouse started auditing llms.txt exist in
  techwyse.com/news/ai-search/google-ai-search-optimization-guide-llms-txt-lighthouse-audit [SECONDARY,
  unverified; note it does not change Google's ranking statement].
- SE Ranking, 300,000 domains, November 2025: 10.13% had the file; no relationship with citation frequency in
  AI answers; removing the variable from the prediction model improved accuracy; among the 50 most-cited
  domains exactly one had it [SECONDARY via somethinginc.com summary; original at seranking.com]. Strength: B-.
- OtterlyAI 90-day experiment: of 62,100+ AI bot visits, 84 (0.1%) requested llms.txt [SECONDARY/WEAK,
  vendor, via somethinginc.com].
- Adoption figures vary wildly by sample (8.7% among top 1,000 sites per Rankability June 2026; 36% among
  84 "prominent" sites per another source) [WEAK: selection effects].
- Net: no demonstrated citation benefit. Cost to produce is small. Use as an optional low-priority
  hygiene item; never sell it as a ranking tactic. Possible legitimate value: agents/coding assistants that
  deliberately read it (documentation sites). I did not find robust evidence even there [no source].

### 4.7 Structured data / schema

- **Ahrefs matched difference-in-differences study** (published 2026-05-12): 1,885 pages that added JSON-LD
  between Aug 2025 and Mar 2026 vs 4,000 control pages; AI Mode +2.4%, ChatGPT +2.2% (both
  statistically indistinguishable from zero), AI Overviews -4.6% (statistically significant, small); every
  treated page already had 100+ AIO citations before schema, i.e. it measures marginal lift for pages
  already in the consideration set. Source: techwyse.com/news/ai-search/schema-markup-ai-citations-ahrefs-study
  and elevarus.com summary [SECONDARY; original Ahrefs post not opened]. Strength: B (design is good,
  my reading is secondhand).
- **AirOps**, 16,851 queries: pages with JSON-LD cited 38.5% vs 32.0% without; FAQPage 45.6%, BreadcrumbList
  46.2%, Organization 44.3% [SECONDARY via analyzify.com hub]. Uncontrolled correlation; sites with schema are
  also better-maintained. Strength: C.
- Mechanism claims: standalone LLM crawlers/fetchers strip JSON-LD and read visible HTML; Google uses
  structured data as context at indexing for AI features (attributed to a Google engineer in a secondary
  write-up) [WEAK/SECONDARY, unverifiable here].
- Google official: structured data not required for generative AI; keep it matching visible content; use it
  for rich results eligibility [OFFICIAL].
- Net: schema is justified by rich results, entity clarity and Merchant Center/product feeds, not by a
  demonstrated AI-citation lift. Where Product/Offer/Organization/LocalBusiness data feeds a shopping or
  knowledge surface, correctness matters. Strength of "schema helps AI citations": **C-D**.

### 4.8 Traffic impact (why measurement matters)

- Pew Research Center (metered data from 900 US adults, March 2025): about 58% had at least one Google search
  with an AI summary; users clicked a traditional result in 8% of visits with an AI summary vs 15% without;
  clicks on cited sources in the summary were very rare. Report: pewresearch.org/short-reads (July 2025 PDF
  pl_2025.05.23_metered-data-ai_report.pdf) [SECONDARY, numbers via search summaries; Pew is a reliable
  primary but I did not open it]. Accessed 2026-10-02. Strength: B (note US, English, March 2025).
- Google claims click quality from AI Overview results is higher than traditional [OFFICIAL, ai-features
  page, as summarized]. No independent confirmation seen. Strength: D for that claim.

---

## 5. Evidence table: claim, source, strength

Legend: Strength A/B/C/D as defined in 0.1. Platform scope in the last column.

| # | Claim | Source (URL, accessed 2026-10-02) | Strength | Scope |
|---|---|---|---|---|
| 1 | No special optimization or files are needed for AIO/AI Mode; standard SEO eligibility applies | developers.google.com/search/docs/appearance/ai-features | A (official) | Google only |
| 2 | llms.txt, special markup, chunking, AI-specific rewriting are unnecessary on Google | developers.google.com/search/docs/fundamentals/ai-optimization-guide | A (official) | Google only |
| 3 | Google-Extended does not affect Search inclusion or ranking | developers.google.com/search/docs/crawling-indexing/google-common-crawlers | A | Google |
| 4 | Allow OAI-SearchBot to be eligible for ChatGPT search; GPTBot is separate | developers.openai.com/api/docs/bots | A | ChatGPT |
| 5 | PerplexityBot indexes for Perplexity results; Perplexity-User ignores robots.txt | docs.perplexity.ai/guides/bots | A | Perplexity |
| 6 | Claude has three bots (ClaudeBot training; Claude-User; Claude-SearchBot) | support.claude.com/en/articles/8896518 | A | Claude |
| 7 | Adding quotations, statistics, source citations to a retrieved page increases its share of the generated answer by roughly 30-40% in a benchmark | arxiv.org/abs/2311.09735 and /html/ | B (peer reviewed, 10k queries, fixed-context design) | Research engine + Perplexity check |
| 8 | Keyword stuffing yields negligible gains in generative engines | same | B | same |
| 9 | Optimization effects are conditional on being already retrieved; heuristics transfer poorly | arxiv.org/abs/2607.14035 | B | Survey of 45 studies |
| 10 | AI engines favor earned/third-party media over brand-owned content, more than Google does | arxiv.org/abs/2509.08919 | B | ChatGPT, Perplexity, Gemini and others |
| 11 | High-influence cited pages are longer, structured, fact-dense (definitions, numbers, comparisons, steps) | arxiv.org/abs/2604.25707 | B (602 prompts, 21,143 citations) | ChatGPT, Google, Perplexity |
| 12 | ChatGPT cites fewer sources but with higher influence per source than Perplexity/Google | same | B | same |
| 13 | Structural features improve citation rate (+17.3%) across six engines | arxiv.org/abs/2603.29979 | C-B | Six engines, author testbed |
| 14 | Document-level features beat lexical tweaks for citation visibility | arxiv.org/abs/2604.19113 | B- | Multiple engines |
| 15 | Brand web mentions correlate more strongly with AI visibility than backlinks (0.66 vs 0.22) | Ahrefs 75k brands (secondary: thenextweb.com, medium.com) | C-B | AIO, ChatGPT, AI Mode |
| 16 | YouTube brand mentions are the strongest single correlate (about 0.74) | same | C | same |
| 17 | Referring domains is the strongest single predictor of ChatGPT citations; step change near 32k | seranking.com/blog/chatgpt-citation-factors/ (via SEJ) | C | ChatGPT |
| 18 | AI assistants cite content 25.7% fresher than organic results; ChatGPT strongest | ahrefs.com/blog/do-ai-assistants-prefer-to-cite-fresh-content/ (16.975M URLs) | B (descriptive) / C (causal) | 5 AI platforms |
| 19 | Adding JSON-LD produces no measurable citation lift (1,885 treated vs 4,000 control) | techwyse.com summary of Ahrefs 2026-05-12 | B | AIO, AI Mode, ChatGPT |
| 20 | Pages with JSON-LD are cited more (38.5% vs 32.0%) | analyzify.com summary of AirOps (16,851 queries) | C | Mixed |
| 21 | llms.txt shows no relationship with citations (300k domains) | seranking.com via somethinginc.com | B- | Multiple |
| 22 | Fewer than 1 in 100 repeat runs return the same brand list | SparkToro/Gumshoe, 600 volunteers, 2,961 runs (secondary: searchenginejournal.com, searchengineland.com) | B | ChatGPT, Claude, Google AI |
| 23 | Citation visibility differences are often inside the noise floor; report CIs | arxiv.org/abs/2603.08924 | B | Perplexity, SearchGPT, Gemini |
| 24 | Query language, not location, selects which market's suppliers are recommended | arxiv.org/abs/2608.30052 | C-B (234 tests) | ChatGPT |
| 25 | Models cite English sources preferentially for English queries; stronger for lower-resource languages | arxiv.org/abs/2509.13930 | B | Open-weight RAG, 8 languages |
| 26 | Black-box rewriting attacks rival gradient attacks for rank promotion and evade perplexity detectors | arxiv.org/abs/2605.29107 | B | Llama-3.1-8B ranker |
| 27 | A two-stage defense cuts manipulation success 50.32% to 6.20% | arxiv.org/abs/2609.02964 | B- (new) | Several LLMs |
| 28 | Users click organic links 8% vs 15% of visits when an AI summary appears | Pew (secondary summaries) | B | US adults, March 2025 |
| 29 | Bing Webmaster Tools AI Performance report lists citations and grounding queries | searchinfluence.com etc. | B (several write-ups) | Copilot/Bing |
| 30 | Perplexity pays publishers 80% of Comet Plus revenue | websitebuilderexpert.com etc. | B (facts), no citation-effect evidence | Perplexity |
| 31 | Content updated within 30 days gets 3.2x citations | search-result summary of unspecified blogs | D | n/a, do not use |
| 32 | GEO increases visibility by up to 40% | Aggarwal et al. | B for benchmark; C for production; headline overstates | n/a |

---

## 6. Optimization tactics ranked by evidence

Rank is by combined evidence strength and expected value, not by effort. Each tactic lists what to
verify and the main way it fails.

### Tier 1: eligibility (prerequisites; evidence A)

1. **Allow the right bots and make the site reachable.** Per-bot robots.txt, CDN/WAF rules, IP allowlists
   for OAI-SearchBot, PerplexityBot, Claude-SearchBot, Bingbot, Googlebot. [OFFICIAL 2.2-2.4]
   Verification: server logs show hits from each bot within 7 days; test with each UA string against
   robots.txt parser; look for 403/429 from WAF.
   Failure mode: blanket "block AI" rules copied from publisher blogs that also block search bots.
2. **Be indexed and snippet-eligible.** No `nosnippet`, no `max-snippet:0`, no `noindex`, no client-side-only
   rendering of main content. [OFFICIAL 2.1]
3. **Server-rendered main content.** AI fetchers generally read raw HTML; text that exists only after
   client-side JS is likely invisible to them (Microsoft: avoid hidden content, image-only details
   [OFFICIAL via SECONDARY]; JS execution by AI bots is not documented by OpenAI/Anthropic/Perplexity in the
   pages I read). Mark as: documented risk, not measured. Check via "view source" and `curl`.
4. **Canonical, fast, stable URLs** so cited links resolve; broken citations are common failure for
   tracked links (observation; no study cited).

### Tier 2: content properties with reproducible support (evidence B)

5. **Make the page the best passage for specific sub-queries.** Topical relevance and context position are
   the most reproducible factors [ACAD survey]. Procedure: list the likely fan-out queries (from Bing
   grounding queries, "People also ask", AI Mode follow-ups), then check that a self-contained passage
   answers each in the first 1-2 sentences under a matching heading.
6. **Add verifiable evidence: statistics with source and date, direct quotations from named experts, and
   citations to primary sources.** Best-supported content edit (Aggarwal et al.: +30-41% in benchmark). Strict
   rule: every number must be true and sourced; do not generate them. [ACAD B]
7. **Extractable structure**: definition-first paragraphs, comparison tables, numbered steps, descriptive
   headings, short lists where the content is truly a list. Consistent across arXiv 2604.25707, 2603.29979,
   2604.19113 [ACAD B/C]; recommended by Microsoft; compatible with Google's "do not over-chunk".
8. **Unique information gain**: original data, first-hand tests, local/specific facts that other pages lack.
   Google calls it "non-commodity" [OFFICIAL]; the survey suggests commodity content is replaceable at the
   rerank stage (inference, not a tested claim). Strength: B- (inferential).
9. **Fluent, plain writing.** Fluency Optimization gave +15-30% in the original benchmark [ACAD B-]. Plain
   language also reduces misquotation risk.

### Tier 3: authority and off-site presence (evidence C-B, causality uncertain)

10. **Earned third-party coverage** in sources the engines already retrieve (industry publications,
    reputable directories, Wikipedia where eligible, reviews, UGC platforms that are legitimate for the
    brand). Supported by Chen et al., Ahrefs 75k brands, SE Ranking. Measure by tracked mention count and
    source quality, not by volume bought. Google warns inauthentic mentions are filtered [OFFICIAL].
11. **Entity consistency**: same name, description, address, social profiles, Organization/LocalBusiness
    markup, Google Business Profile and Merchant Center data kept current [OFFICIAL says keep Business
    Profile and Merchant Center current]. Value is plausible for entity disambiguation; AI-citation effect
    unmeasured.
12. **YouTube presence** for brand mentions: strongest correlate in Ahrefs but confounded with brand size
    [C]. Do not recommend as a standalone tactic for small sites; recommend where video is already a natural
    format.
13. **Freshness where freshness is the intent**: news, prices, versions, "best X in 2026", regulations.
    Real updates with visible date; strongest on ChatGPT per Ahrefs. [B descriptive]. Not a reason to
    churn evergreen pages.

### Tier 4: low or no demonstrated effect (do cheaply or skip)

14. **Schema/JSON-LD** for AI citation: no lift in the matched study. Implement for rich results and entity
    clarity; do not promise AI gains. [B against]
15. **llms.txt**: no relationship in 300k-domain study; Google says no effect. Optional, 30 minutes of work,
    never a priority. [B- against]
16. **FAQ blocks for AI**: FAQPage schema correlated in AirOps (45.6%) but the matched study found no lift
    from JSON-LD generally; Google removed FAQ rich results for most sites in 2023 [background knowledge,
    not fetched]. If the FAQ content answers real user questions, keep it as visible text; do not add FAQs
    only for bots.

### Tier 5: not supported or harmful

17. Keyword stuffing and "AI keywords" lists [B against].
18. Invisible text or prompt-injection strings aimed at crawlers (Section 7).
19. Mass-produced AI content variants [Google: scaled content abuse, OFFICIAL].
20. Fabricated statistics/quotes (Section 7).
21. Paid mention schemes and fake reviews (Google filters; reputational and legal risk).

### Tactic summary table

| Tactic | Evidence | Cost | Risk | Recommend |
|---|---|---|---|---|
| Per-bot robots.txt / WAF audit | A | low | none | always, first |
| Server-rendered text, indexability | A | low-med | none | always |
| Passage-level answers for fan-out queries | B | med | none | yes |
| Sourced stats, expert quotes, primary citations | B | med | fabrication if sloppy | yes, with fact check |
| Definition/table/steps structure | B-C | low | over-fragmenting | yes |
| Original data / information gain | B- | high | none | yes where feasible |
| Earned media / mentions | C-B | high | spam if bought | yes, ethical only |
| Real freshness updates (news/price topics) | B/C | low-med | fake dates | targeted |
| Entity consistency, GBP/Merchant feeds | plausible | low | none | yes |
| JSON-LD for AI lift | B against | low | none | do for rich results only |
| llms.txt | B- against | very low | none | optional |

---

## 7. Anti-patterns and manipulation risks

### 7.1 Anti-patterns for analysts and tool builders

1. **Treating one answer as ground truth.** A single run of one prompt says almost nothing (Section 10).
2. **Reporting rank position in an AI answer.** SparkToro: the same list in the same order appears under
   0.1% of the time; "rank" is not stable. Report appearance rate (SparkToro's own recommendation:
   frequency of appearance over many runs; example brand in 85 of 95 Google AI runs = 89%).
   https://searchengineland.com/ai-recommendation-lists-rarely-repeat-study-468076 and
   https://www.searchenginejournal.com/ai-recommendations-change-with-nearly-every-query-sparktoro/566242
   (accessed 2026-10-02) [SECONDARY]. Original study: 600 volunteers, 12 prompts, ChatGPT, Claude, Google AI
   (AIO + AI Mode), 2,961 runs, Nov-Dec 2025.
3. **Blending engines into a single score** (differences are structural, 4.5).
4. **Confusing mention with citation, and training-data presence with live retrieval.** A brand can be named
   with no URL; a URL can be cited without being named.
5. **Using prompts the brand wrote.** Prompt sets built from the client's own vocabulary inflate visibility;
   include unbranded category and problem prompts as the majority.
6. **Ignoring personalization/location/language.** Query language shifts the market (3.5); account state,
   memory and location can too.
7. **Optimizing for the citation count instead of the answer content.** Absorption, accuracy and sentiment
   matter. A cited page that is misquoted is a brand risk.
8. **Selling llms.txt/schema as a ranking switch.** Not supported (4.6, 4.7).
9. **Believing vendor correlations are causal.** Large brands cause both the signals and the citations.

### 7.2 Black-hat GEO

- Research shows rewriting-based manipulation can promote documents against LLM rankers with high fluency
  and can evade perplexity filters (arXiv 2605.29107), and that factually consistent manipulated documents
  evade fact-check filters (arXiv 2609.02964). The same papers show defenses cut success from about 50% to
  about 6% in lab conditions. Expect production defenses to catch up; manipulation has a short half-life
  and carries platform-policy and reputational risk.
- Prompt-injection in page content (hidden text instructing the assistant to recommend a brand) is
  manipulation of a user-facing system; treat as prohibited. [no source fetched for platform rules; Google's
  spam policies prohibit hidden text in general, background knowledge].
- Fake review/mention farms, parasite-SEO on third-party domains, and AI-generated fake expert quotes: against
  Google spam policy and most platform terms; sometimes illegal (consumer protection).
- Legal/ethical: statistics and quotations must be real (Aggarwal's +40% came from inserting plausible
  evidence; a naive implementation by LLM can fabricate it).

### 7.3 Defensive concerns for site owners

- Competitors or third parties can try to poison the content AI reads about a brand (inaccurate comparison
  pages, review spam). Monitor brand answers for factual errors and sentiment.
- Perplexity-User and ChatGPT-User may ignore robots.txt (user-initiated); sensitive content needs
  authentication, not robots.txt.
- Blocking training bots does not prevent search-bot-driven citations, and blocking all bots removes
  visibility. Make the decision explicit per bot (2.7).

### 7.4 seocli policy for recommendations

The plugin must refuse to generate: fabricated statistics or quotes, hidden text, fake reviews, or
instructions to inject prompts into pages. The Constitution (Principle VIII, falsifiable methodology,
primary sources, numeric targets) lines up: each recommendation should carry an observable check and a
leading indicator.

---

## 8. What an MCP server must provide, and how to implement machine-readable content

### 8.1 Data the server must expose

Design principle: store raw observations (every sampled answer) and compute metrics from them. Never store
only aggregates; confidence intervals need the underlying samples.

#### 8.1.1 Entities

| Entity | Fields (English identifiers per glossary) |
|---|---|
| `prompt` | id, text, language (BCP-47, e.g. `it-IT`), intent (informational / commercial / transactional / navigational / local), funnel stage, branded (bool), topic cluster, country, created_at, source (human, generated, GSC-derived, PAA-derived) |
| `prompt_set` | id, name, version, prompt ids, stratification metadata, frozen_at (sets must be versioned; changing a set breaks the time series) |
| `engine` | id, product (chatgpt_search, perplexity, google_ai_overview, google_ai_mode, gemini, copilot, claude_search), interface (web UI, API with search tool), model/version label if exposed, retrieval mode |
| `sample` | id, prompt_id, engine, run_index, collected_at, locale, country/exit region, account state (logged in/out, memory on/off), interface, temperature/params if API, raw_answer_text, raw_html or JSON payload ref, latency, status (answered, refused, no_search, error) |
| `search_activation` | sample_id, used_web_search (bool or unknown), sub_queries (fan-out queries when exposed), sub_query_count |
| `citation` | sample_id, position, url, normalized_url, domain, registrable_domain, title, snippet if shown, citation_kind (inline, footnote, card, carousel), is_brand_owned, source_type (brand-owned, earned media, UGC, marketplace, reference, competitor, social, video) |
| `mention` | sample_id, entity_id, surface_form, char_span, rank_in_list (if list), sentiment (-1..1), stance (recommended, neutral, negative, not-recommended), accuracy_flags (wrong price, wrong claim, outdated), has_link (bool), linked_url |
| `entity` | id, canonical_name, aliases (incl. Italian/English variants, misspellings), domains owned, competitor flag |
| `ai_overview_observation` | prompt_id, collected_at, locale, device, present (bool), cited_urls, text, language, "AI Mode available" flag, SERP organic rank of own page for same query |
| `crawl_access` | domain, bot (Googlebot, Bingbot, OAI-SearchBot, GPTBot, ChatGPT-User, PerplexityBot, Perplexity-User, ClaudeBot, Claude-SearchBot, Claude-User, Google-Extended), robots_rule, effective_allowed, http_status_for_UA, verified_by_ip (bool), last_seen_in_logs |
| `page_features` | url, fetched_at, status, rendered_vs_raw_text_ratio, word_count, headings tree, has_definition_first, table_count, list_count, stat_count (numbers with units), quote_count, outbound_citation_count, jsonld_types, last_modified_visible, last_modified_header, canonical, language, hreflang |
| `first_party_report` | source (bing_ai_performance, gsc_generative_ai), date, url, impressions or citations, grounding_query, clicks if available |

#### 8.1.2 Derived metrics (computed, with intervals)

- **Appearance rate**: share of samples where the entity is mentioned (or the domain is cited), per
  engine x prompt x period, with a Wilson or bootstrap confidence interval and the sample count n.
- **Citation share / share of voice**: entity's citations over all citations in the sample set,
  with bootstrap CI across prompts and runs (Sielinski 2603.08924).
- **Mention-to-citation conversion**: P(cited | mentioned) and P(mentioned | cited).
- **Source-type mix**: shares of earned/brand-owned/UGC/video per engine.
- **Absorption proxy**: lexical or embedding overlap between cited page passages and answer text.
- **Stability**: Jaccard of cited domain sets across repeated runs of the same prompt; flag prompts too
  volatile for tracking.
- **Sentiment/accuracy** counts for brand mentions.
- **Competitor gap**: prompts where competitors appear and the brand does not.

#### 8.1.3 Tools (capability-named, no vendor names, per Constitution V)

Proposed names, following the seocli rule that tool descriptions are capability-based:

1. `ai_answer_sample`: run N samples of a prompt on an engine; returns raw answers, citations, mentions,
   fan-out queries when exposed, plus run metadata. Parameters: prompt, engine(s), n, locale, country,
   interface. Requires an explicit cost/credit estimate before running, since repeated sampling is the
   cost driver (seocli has quota management).
2. `ai_visibility_report`: aggregate over a `prompt_set` and period; returns appearance rate, citation
   share, CIs, per-engine breakout, deltas versus previous period with a significance flag.
3. `ai_overview_check`: for a keyword/locale, report whether an AI Overview is present, cited URLs, and
   organic rank of the target domain.
4. `ai_citation_sources`: top cited domains and URLs for a prompt set, with source-type classification.
5. `ai_crawl_access_audit`: per-bot robots.txt evaluation and live fetch with each UA, WAF detection.
6. `page_extractability_audit`: server-rendered text check, passage structure, evidence density (stats,
   quotes, sources), freshness fields, schema/visible-content consistency.
7. `ai_prompt_set_generate`: draft stratified prompt sets from seed keywords, GSC queries and PAA, in the
   target language; output is a proposal for the human to freeze.
8. `ai_first_party_import`: ingest Bing AI Performance CSV and Search Console generative AI data
   (while no API exists for Bing; check Search Console API availability before promising it).
9. `ai_brand_accuracy_review`: list answers where the brand was misdescribed, for human triage.

Do not include a tool called "optimize for ChatGPT" or any that returns a single score without CI.

#### 8.1.4 Output conventions

- Every aggregate returns `n_samples`, `n_prompts`, CI bounds, collection window, engine/interface
  version. If n is below the minimum for a stated CI width, return `insufficient_data` instead of a
  number.
- Return raw text and citation URLs as untrusted data (they come from third-party engines and could
  contain injected instructions; do not execute). Constitution IV: never log tokens/cookies; do not
  expose provider names in errors.
- Store `collected_at` in UTC and engine "interface" (consumer web vs API with search tool): the same
  prompt can differ between them. Many vendors measure via API; consumer UI results can differ
  materially [SECONDARY, vendor claim; verify with a paired test before relying on it].
- Privacy: prompts about a client's private data (Search Console, GA4) must not be sent to non-EU models
  (Constitution VII). Public keyword prompts to engines are fine.

### 8.2 How a developer implements machine-readable content

Principle: make the primary content easy for a text extractor and for passage-level retrieval; then add
metadata that helps classic search and entity understanding. Nothing below is proven to lift AI
citations on its own (Section 4); most items are hygiene with independent justification.

1. **Server-rendered HTML for all primary content.** Test with `curl -A "OAI-SearchBot" URL` and with
   JS disabled. If a framework hydrates the content only client-side, enable SSR/SSG.
2. **Semantic HTML**: one `<h1>`, nested `<h2>/<h3>` that read as questions or topics, `<table>` with
   `<th scope>` for comparisons, `<ol>` for steps, `<dl>` for definitions, `<time datetime>` for dates,
   `<figure>/<figcaption>`, descriptive `alt`. Avoid layout tables; avoid text inside images; avoid PDFs
   for key facts [Microsoft guidance, SECONDARY].
3. **Passage design**: each section self-contained (restate the subject, do not rely on "as above"),
   answer first, then evidence. Numbers carry unit, date and source link in the same sentence.
4. **Visible author, date, update note, and sources.** Show a real "last updated" with change notes;
   keep `Last-Modified` and `<time>` consistent with the actual change date.
5. **Structured data (JSON-LD)** matching visible content: `Organization` (with `sameAs`), `WebSite`,
   `Article`/`BlogPosting` (author, datePublished, dateModified), `Product`/`Offer`/`AggregateRating` only
   with real data, `LocalBusiness`, `BreadcrumbList`, `FAQPage` only for visible FAQ. Validate with the Rich
   Results Test and schema.org validator. Google: must match visible content [OFFICIAL]. Expected
   benefit: rich results and entity clarity; not claimed for AI citation.
6. **`robots.txt`** with an explicit per-bot block (training vs search):
   ```
   User-agent: OAI-SearchBot
   Allow: /
   User-agent: GPTBot
   Disallow: /          # training opt-out, decision is the client's
   User-agent: PerplexityBot
   Allow: /
   User-agent: Claude-SearchBot
   Allow: /
   User-agent: ClaudeBot
   Disallow: /
   User-agent: Google-Extended
   Disallow: /
   ```
   Sample only; each Allow/Disallow is a business decision. Verify bot IPs against the published JSON
   lists (OpenAI, Perplexity, Anthropic) rather than trusting the User-Agent alone.
7. **`llms.txt`** (optional): Markdown with an H1 title, blockquote summary, and sections of links with
   one-line descriptions, per the proposal. Low value; keep it automated from the sitemap/CMS so it does
   not rot.
8. **Sitemaps with accurate `lastmod`** (only update lastmod on real content change).
9. **Markdown or plain-text alternates** (e.g. `/page.md`) are sometimes recommended by vendors; Google
   says unnecessary [OFFICIAL] and there is no evidence of effect elsewhere. Skip unless the site is
   documentation consumed by coding agents.
10. **Feeds for commerce**: Merchant Center product feeds and accurate Business Profile data, which Google
    says to keep current [OFFICIAL]; these feed AI Mode shopping surfaces (inference; not verified).
11. **Referral tracking**: tag/watch `utm_source=chatgpt.com` and referrers from perplexity.ai,
    copilot.microsoft.com, gemini.google.com, claude.ai in analytics; server-log bot analytics by UA +
    verified IP.
12. **`nosnippet`/`data-nosnippet`/`max-snippet`** for content the owner wants out of AI features
    (Google). Note the trade-off: restricting snippets reduces the text AI can reuse.
13. **hreflang and language-correct content** for Italian vs English versions (Section 9).

---

## 9. Italian-language specifics

Evidence base is thin. Facts are mostly secondary or inferential; label accordingly.

1. **Availability**: Google AI Overviews launched in Italian for Italy (Italian press: 26 March, with
   Austria, Belgium, Germany, Ireland, Poland, Portugal, Spain, Switzerland) and AI Mode launched in Italy
   in Italian (Italian press dated 2025-10-08) [SECONDARY: italiaoggi.it, smartworld.it,
   tg24.sky.it/tecnologia/2025/10/08/ai-mode-google-italia]. The AI Overviews press snippet did not state
   the year; verify before quoting. AI Overviews on Italian queries are shown to logged-in adults per one
   article [SECONDARY, WEAK for current conditions].
2. **Query language selects the market**: arXiv 2608.30052 (ChatGPT, 234 tests): with queries in the local
   language, local brands dominate; English queries suppress local recommendations; language and location
   are separable. Implication: an Italian business must be tracked with Italian prompts, plus a small
   English control set. [C-B; single engine]
3. **English preference under RAG**: arXiv 2509.13930 finds engines prefer English sources for English
   queries and more so for lower-resource languages. Italian is mid-resource; evidence on the exact
   effect size for Italian is absent. Practical inference: Italian pages compete mostly with Italian pages
   for Italian prompts, but cross-language English sources can leak into Italian answers when Italian
   coverage is thin. Test, do not assume.
4. **Cross-language citation stability** is engine-dependent (Chen et al. 2025 tested zh, ja, de, fr, es vs
   English; Italian was not in that list per the secondary summary). Expect Italian citation sets to differ
   from English ones for the same intent.
5. **Italian source ecosystem**: earned media in Italy means national press (Corriere, Repubblica, Il Sole
   24 Ore, ANSA), vertical publishers, Wikipedia IT, forum/UGC (Reddit has smaller Italian footprint than
   English; Italian-specific forums and Facebook groups are less retrievable) [analyst inference; no
   study found]. Seocli should surface actual top cited Italian domains per vertical rather than guess.
6. **Query formulation**: Italian prompts are often longer and conversational, with local/regional
   references, formal vs informal register ("tu/Lei"), and dialect/regional product names. Prompt sets
   should include variants (e.g. "migliore", "consigliato", "quale scegliere", "prezzo", "recensioni",
   "vicino a me") [inference].
7. **Entity aliases**: Italian brands often have multiple surface forms (with/without "S.r.l.", accents,
   English/Italian descriptors). The `entity.aliases` field must support it; mention extraction needs
   Italian morphology (plural, articles, apostrophes such as "dell'azienda").
8. **Measurement hygiene**: set `Accept-Language: it-IT`, Italian exit IP/region where the interface
   allows, and record both; the paper shows language and exit location are separable factors.
9. **Local/maps intents** in Italian AI answers (restaurants, tourism, services) lean on Business Profile
   and review platforms; keep NAP data consistent [inference; Google says keep Business Profile current,
   OFFICIAL].
10. **Regulatory**: GDPR and the EU AI Act context favor consent-aware measurement; prompts and answers
    tied to a client's private data must stay within the EU (Constitution VII).

---

## 10. Measurement protocol for a GEO analyst

Goal: produce defensible, repeatable estimates of AI visibility with uncertainty, tied to hypotheses that
can be falsified.

### 10.1 Define the question (VALIDATE-ready)

State a falsifiable hypothesis before collecting data. Example: "After adding sourced statistics and a
definition-first paragraph to page P by date D, the appearance rate of P's domain for prompt cluster C
on engine E will rise by at least 10 percentage points within 6 weeks; if the CI of the after period
overlaps the before period or the rate falls, reject."
Include a leading indicator (e.g. page indexed in Bing, appears in Bing grounding-query report, passage
retrieved for sub-query) because citation lag is long.

### 10.2 Build the prompt set

1. Sources: Search Console queries (long-tail, question-like), People Also Ask, sales-call transcripts,
   support tickets, competitor comparisons, customer interviews. Never only the client's own language.
2. Strata (set quotas): intent (informational, comparison, transactional, local), funnel stage,
   branded vs unbranded (target at least 70% unbranded), head vs long-tail, question vs imperative form,
   locale/language.
3. Size: SparkToro used 12 prompts per category in its volunteer test; Sielinski's work indicates that
   differences must be judged with bootstrap CIs. A pragmatic floor for a client report: 30-50 prompts
   per cluster for tracking and at least 10-20 clusters-by-engine cells that matter; fewer prompts =
   report as exploratory. [practice; no source gives a universal number; Sielinski provides sample-size
   guidance I did not read]
4. Paraphrases: include 2-3 paraphrases of the most important prompts to estimate phrasing sensitivity
   (Chen et al. report engines differ in sensitivity to phrasing).
5. Freeze and version the set. Add a holdout set never used to guide optimization to avoid overfitting.
6. Keep a small "canary" set that is run every day, to detect engine changes.

### 10.3 Sampling design

1. **Repeat each prompt K times per engine per period.** Because identical prompts return different lists
   (SparkToro: less than 1 in 100 same list across runs; arXiv 2608.30052: top recommendation changed in
   4 of 6 prompts over six identical runs), K=1 is not a measurement. Use K>=10 for tracked prompts as a
   starting point; raise K until the CI width is acceptable (Sielinski approach). Treat K>=30 per cell as
   the target for before/after decisions. [practice, derived from variance evidence]
2. **Randomize order and time** of runs to avoid time-of-day artifacts; sample across multiple days
   (Sielinski: nine days of daily collection plus 10-minute interval).
3. **Control for personalization**: fresh session, logged-out where possible, memory off, same locale and
   exit region, same interface. Record every control in the sample metadata.
4. **Separate interfaces**: consumer UI vs API; do not mix in one series.
5. **Detect search activation**: record whether the engine searched. Prompts with no search produce
   mentions from parametric memory; analyze them separately.
6. **Record the raw payload**, not only extracted citations, so extraction can be corrected later.
7. **Cost control**: sampling is expensive; allocate more K to prompts near the decision boundary (adaptive
   sampling) and fewer to stable extremes.

### 10.4 Metrics and statistics

- **Primary metric: appearance rate** = samples with the entity mentioned / valid samples (separately for
  citation and mention). SparkToro recommends frequency of appearance as the usable visibility metric
  (secondary summary).
- **Confidence intervals**: Wilson interval for proportions per cell; bootstrap over prompts and runs for
  share-of-voice metrics (cluster bootstrap by prompt, since runs within a prompt are not independent).
- **Share of voice (SOV)**: brand citations / all citations in the cluster. Report per engine and for
  the pooled set with weights stated.
- **Significance**: do not claim a change unless CIs for before/after do not overlap, or a permutation test
  or difference-in-differences against control prompts/pages agrees. Use control pages (untreated) to
  separate your effect from engine-wide drift (the Ahrefs schema study used matched controls with
  difference-in-differences; follow that design).
- **Rank**: do not report it. If a client demands it, show distributions, not point values.
- **Citation vs mention table** per prompt cluster:

| Case | Interpretation | Action |
|---|---|---|
| Cited and mentioned | Strong | Maintain; check accuracy |
| Cited, not mentioned | Used as source but not credited in text | Strengthen brand association in content/PR |
| Mentioned, not cited | Parametric memory or third-party coverage | Make owned pages retrievable; get the answer to link you |
| Neither | Not in candidate set or not chosen | Diagnose stage 1-4 (Section 1.2) |

- **Absorption and accuracy**: sample answers where the brand appears; check the facts against the page and
  record errors.
- **Noise floor**: estimate by running the same prompts on the same day for two independent batches; the
  disagreement is the noise floor. Report it alongside every delta.

### 10.5 Diagnostic workflow when not cited

1. Check access (robots/WAF/logs) for the engine's search bot.
2. Check index status in the engine's underlying index (Google/Bing).
3. Capture sub-queries (fan-out) and check whether the page ranks organically for them.
4. Compare with cited pages: type, length, evidence density, freshness, domain authority, language.
5. Identify whether competitors win via earned media (then the lever is off-page).
6. Edit one thing, log it with date, and re-measure with a control.

### 10.6 Tracking over time

- Cadence: weekly for canary and priority clusters, monthly for the full set; AI answers shift week to
  week (45.5% of AIO sources turned over when content regenerated, per a vendor summary
  [WEAK/SECONDARY: addlly.ai, thrivestack.ai, not verified]).
- Annotate every site change, engine release (model update), and policy change on the time axis.
- Use rolling 4-week windows to smooth noise; display CIs as bands.
- Keep a version of the prompt set per period; compare only across identical versions.
- Cross-check with first-party data: Bing AI Performance (citations, grounding queries) and Search
  Console AI feature impressions; where third-party sampling and first-party counts disagree, trust the
  first-party counts for absolute levels and sampling for relative competitor comparison.
- Referral analytics: AI referrals are small and undercounted (some assistants strip referrers); use as a
  lagging corroboration, not a primary KPI [practice; no study cited].

### 10.7 Reporting rules

1. Every figure carries n and a CI.
2. Never present a blended cross-engine number without breakouts.
3. Separate observation from inference; label correlations.
4. Say what was not measured (engines not sampled, languages not covered).
5. Tie each recommendation to the 4-field structure from the seocli methodology: first-principle
   observation, dependency, falsifiability check, leading indicator (Constitution VIII).

---

## 11. Open questions and gaps

1. **Causality of off-site signals.** All brand-mention / backlink evidence is correlational. A randomized
   or natural-experiment design is missing.
2. **Italian-specific effects.** No study found measuring Italian citation behavior directly; Section 9 is
   mostly inference from multilingual work.
3. **Production-engine ranking criteria.** None of OpenAI, Anthropic, Perplexity publishes citation
   criteria. Everything beyond crawler docs is reverse-engineered.
4. **Persistence of optimization effects.** The survey found no stable longitudinal cross-platform causal
   effect on organic discoverability.
5. **Consumer UI vs API divergence.** Frequently asserted by vendors, not established in the sources I read.
6. **Search Console generative AI report details** and whether an API exists; Bing report API timeline
   "on backlog". Verify before building import tools.
7. **JS rendering by AI fetchers.** Not documented in the pages I read; measure with server logs and test
   pages before advising.
8. **Dependence on dates.** Several 2026 sources (the Google guide, the Bing report, the GSC report,
   arXiv 2026 preprints) postdate model training and were read only through fetch summaries. Re-verify
   anything customer-facing.

---

## 12. Recommendations for the seocli plugin (condensed)

1. Frame GEO as SEO eligibility + passage-level answerability + third-party authority + measurement.
2. Lead any GEO audit with the access and index checks (Tier 1), then the diagnostic stage model.
3. Ship measurement tools that return appearance rate with CI and refuse single-run conclusions.
4. Keep citation, mention and absorption separate in the data model; keep engines separate in reports.
5. Ingest first-party data (Bing AI Performance, Search Console AI report) as ground truth for levels.
6. Treat schema and llms.txt as optional hygiene; never market them as AI ranking levers.
7. Block manipulation tactics in the skill instructions (fabricated stats, hidden text, fake mentions).
8. Test Italian explicitly: Italian prompts, Italian exit region, English control set, Italian aliases.
9. Re-verify the SECONDARY items in this document (Section 0.1) before they enter customer-facing text.

---

## 13. Source list (all accessed 2026-10-02)

Official
- https://developers.google.com/search/docs/appearance/ai-features
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide
- https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
- https://developers.openai.com/api/docs/bots
- https://docs.perplexity.ai/guides/bots
- https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler
- https://preview-about.ads.microsoft.com/en/blog/post/october-2025/optimizing-your-content-for-inclusion-in-ai-search-answers (seen via search snippet only)

Academic (arxiv.org/abs/...)
- 2311.09735 Aggarwal et al., GEO (KDD 2024); html version fetched
- 2509.08919 Chen, Wang, Chen, Koudas, GEO: How to Dominate AI Search
- 2509.13930 Linguistic Nepotism (ICML 2026 Spotlight)
- 2603.08924 Sielinski, Quantifying Uncertainty in AI Visibility
- 2603.29979 Structural Feature Engineering for GEO
- 2604.19113 Think Before Writing (FeatGEO)
- 2604.25707 From Citation Selection to Citation Absorption
- 2605.29107 GEO-Bench: Benchmarking Ranking Manipulation
- 2607.14035 Martinez, Critical Survey of GEO (2023-2026)
- 2608.30052 Zatuchin, The Language of the Question Selects the Market
- 2609.02964 When Optimization Becomes Manipulation (GEO Defender)

Industry (primary fetched)
- https://ahrefs.com/blog/do-ai-assistants-prefer-to-cite-fresh-content/

Industry and press (secondary, via search summaries; not opened)
- https://ahrefs.com/blog/ai-brand-visibility-correlations (75,000 brands) and summaries at
  thenextweb.com/news/ahrefs-youtube-mentions-ai-visibility-brand-search
- https://seranking.com/blog/chatgpt-citation-factors/ ; https://www.searchenginejournal.com/new-data-top-factors-influencing-chatgpt-citations/561954/
- https://www.techwyse.com/news/ai-search/schema-markup-ai-citations-ahrefs-study ; https://analyzify.com/hub/schema-markup-ai-citations-research
- https://somethinginc.com/blog/does-llms-txt-work-2026-data/
- https://www.searchenginejournal.com/ai-recommendations-change-with-nearly-every-query-sparktoro/566242 ; https://searchengineland.com/ai-recommendation-lists-rarely-repeat-study-468076
- https://www.searchinfluence.com/blog/bing-ai-performance-report-copilot-citations/
- https://previsible.io/seo-ai-news/google-launched-ai-performance-reports-in-search-console/
- https://www.pewresearch.org (metered-data AI report, May 2025; short read 2025-10-01)
- https://tg24.sky.it/tecnologia/2025/10/08/ai-mode-google-italia ; https://www.italiaoggi.it/marketing-e-media/media/google-ai-overview-arriva-in-italia-alle-ricerche-risponde-anche-lintelligenza-artificiale-ygfrvsvf
- https://llmpulse.ai/blog/perplexity-publishers-program/ ; https://www.websitebuilderexpert.com/news/perplexity-comet-plus/

Theses (listed, unread)
- unitesi.unipv.it, tesi.univpm.it, webthesis.biblio.polito.it (URLs in 3.7)
