last_verified: 2026-10-02
volatile: true

# Kill list: do not recommend these

Each line is something a recommendation must not rely on. The lint allows these terms only here and in
`## Do not recommend` sections.

- HowTo rich results and FAQ rich results as a goal: Google no longer shows them for ordinary sites; do not sell the markup for rich results.
- FID as a metric: replaced by INP.
- A keyword density target.
- llms.txt as a ranking lever.
- A Domain Authority KPI (a third-party score, not a Google signal).
- rel=next and rel=prev as an indexing signal.
- The Indexing API for ordinary pages (limited to job postings and livestream pages).
- Flesch readability for Italian text: use Gulpease.
- An optimisation score from an ad platform as a KPI.

## Sources

- Google Search Central documentation on rich result changes, Core Web Vitals and the Indexing API (to be re-verified with URL and access date in the M2 verification pass, DESIGN section 9.3).
