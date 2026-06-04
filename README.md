# Evaluating Agent-Authored Pull Requests Before Merge

An empirical study of how developers evaluate AI agent-authored pull requests (PRs) before they are merged, using the AIDev dataset. The analysis is descriptive and quantitative, broken down across five AI coding agents.

**Course:** SE 611 — Software Analytics (Spring 2026), Drexel University
**Instructor:** Dr. Preetha Chatterjee

## Research questions

- **RQ1 — Evaluation prevalence.** To what extent do agent-authored PRs receive formal evaluation, and how does this vary across the five agents?
- **RQ2 — Reviewer behavior.** Among PRs that _do_ receive evaluation, what behaviors do reviewers exhibit (verdict mix, comment volume, and time-to-decision)?

## Dataset

The study uses **AIDev-pop**, a popular-repository subset of the AIDev dataset (Li et al., MSR 2026), loaded directly from Hugging Face (`hf://datasets/hao-li/AIDev`).

- ~33,596 pull requests
- 5 AI coding agents: Codex, Devin, Copilot, Cursor, Claude Code
- ~2,807 repositories (curated, 100+ stars)

**Note on composition:** Codex accounts for roughly 65% of AIDev-pop, so any dataset-wide average largely reflects Codex behavior. For this reason, all results are reported per agent in addition to any aggregate figure.

Four tables are joined for the analysis: `pull_request`, `pr_reviews`, `pr_comments`, and `pr_review_comments_v2` (inline, code-line comments). The key joins are `pull_request.id → pr_reviews.pr_id` and `pull_request.id → pr_comments.pr_id`; inline comments link indirectly through `pull_request_review_id → pr_reviews.id → pr_id`.

## Repository contents

| File                  | Description                                                                        |
| --------------------- | ---------------------------------------------------------------------------------- |
| `aidev_project.ipynb` | The full analysis: data loading, enrichment, and both RQ tracks, with all figures. |
| `.gitignore`          | Excludes generated artifacts (chart PNGs, downloaded `.parquet` data).             |
| `README.md`           | This file.                                                                         |

Generated charts and the cached dataset are intentionally not committed; the notebook regenerates them on each run.

## How to run

The notebook loads the dataset over the network from Hugging Face, so an internet connection is required.

1. Clone the repository:
   ```bash
   git clone https://github.com/Software-Analytics-Drexel-University/se611-rrv-project.git
   cd se611-rrv-project
   ```
2. Install the dependencies (Python 3):
   ```bash
   pip install pandas numpy matplotlib seaborn pyarrow jupyter
   ```
3. Launch Jupyter and open the notebook:
   ```bash
   jupyter notebook aidev_project.ipynb
   ```
4. Run all cells (Run → Run All Cells). The notebook downloads the AIDev-pop tables, computes all statistics, and saves each chart as a PNG in the working directory.

## Key findings

### RQ1 — Most agent PRs merge without formal review

- **53.8%** of all PRs are merged with zero formal review — a "fast lane" into the main branch.
- The rate varies sharply by agent: **74.2%** of Codex PRs take the fast lane versus **6.9%** of Copilot PRs, indicating two distinct collaboration regimes.
- Stress-testing the claim with a stricter definition of engagement (no formal review _and_ no thread comment _and_ no inline comment): **84.2%** of fast-lane PRs are _truly silent_ — no human engagement of any kind before merge.
- "No engagement" means different things per agent. Share of an agent's PRs merged with no engagement at all: Codex **67.9%** (merged anyway — a rubber-stamp regime), Copilot **2.9%** (disengaged PRs are mostly abandoned, so review acts as a gate), Devin **0.1%** (PRs almost always draw at least informal comments).

### RQ2 — When review happens, it is substantive but rarely a gate

- **24.2%** of PRs (8,140) received at least one formal review — the RQ2 subset.
- Reviewed PRs are iterative, not rubber-stamped: median 2 reviews / 2 thread comments / 1 inline comment, with long tails (90th percentile of 8+ reviews and 9+ inline comments; one PR reached 159 inline comments).
- Verdict mix is dominated by `COMMENTED` (62–81% of reviews per agent), while `CHANGES_REQUESTED` is rare everywhere (2–7%) — explicit rejection is unusual. Devin draws the most formal approvals (31%); Claude Code is almost pure discussion (81% commented, 16% approved).
- Review correlates with deliberation time, not rejection: median time-to-merge is ~0 hours for fast-lane PRs versus 6.2 hours for reviewed PRs — roughly a 100× gap.

## Limitations

- Codex is ~65% of the data; aggregate figures are reported alongside per-agent breakdowns to avoid masking variation.
- The verdict mix is computed per review, so heavily-reviewed PRs weigh more heavily.
- Inline comments attach to a review by construction, so a no-review PR cannot have them — thread comments are the meaningful informal-engagement channel for fast-lane PRs.
- The analysis is descriptive only; it makes no causal or post-merge defect claims.
- AIDev-pop is a curated popular-repository subset and may not generalize to all projects.

## Related work

This study is complementary to "Understanding Dominant Themes in Reviewing Agentic AI-authored Code" (arXiv:2601.19287), which examines the _themes_ of review content on the same dataset. This work instead quantifies evaluation _prevalence_ and reviewer _behavior_; the two do not overlap.

## Acknowledgments

Per course policy, AI assistance was used during this project for code scaffolding, debugging, and support with analysis and visualization. All research questions, analytical decisions, interpretations, and conclusions are the author's own.
