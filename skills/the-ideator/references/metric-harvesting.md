# Metric Harvesting Protocol

Use this file before ranking ideas. The goal is to replace slow default baseline reproduction with a fast, citation-backed comparison board from recent top papers without hallucinating metrics.

The protocol is fail-closed: model memory is never a valid metric source. A numeric result is usable only when the agent retrieves or is given a verifiable source and records the locator.

## Source Priority

Prefer primary sources:

1. Official conference proceedings and paper pages.
2. OpenReview, ACL Anthology, CVF, ACM Digital Library, IEEE, USENIX, PMLR, NeurIPS/ICML/ICLR proceedings, AAAI/IJCAI pages.
3. Authors' arXiv versions only when official pages are unavailable or lack full metrics.
4. Authors' code repositories and leaderboards for setup details.
5. Secondary blogs only for navigation, never as metric authority.

Always cite the paper source used for the metric. Prefer sources that expose PDF, HTML, or proceedings pages where the table/section can be located.

## Retrieval Verification Step

For every candidate metric row:

1. Retrieve candidate papers using at least one supported retrieval path:
   - official proceedings search;
   - arXiv API or arXiv page;
   - Semantic Scholar API or page;
   - OpenReview;
   - ACL Anthology, CVF, PMLR, ACM, IEEE, AAAI, IJCAI, NeurIPS, ICML, or ICLR pages;
   - web search restricted to primary sources when APIs are unavailable.
2. Open the paper PDF, HTML paper, or official proceedings page.
3. Locate the exact table, figure, appendix table, page number, or section containing the metric.
4. Cross-check that the task, dataset, split, metric definition, model scale, and training/inference setting match the intended comparison.
5. Record the source URL and locator. If the source is a PDF, record the page number and table/figure number when available.
6. If no locator can be found, write `[UNVERIFIED]` in the score field and do not use the number as a direct SOTA target.

Valid locator examples:

- `PDF p. 7, Table 2`
- `Appendix C, Table 9`
- `HTML section 4.2, Results`
- `OpenReview official PDF p. 5, Table 1`
- `Leaderboard snapshot, accessed YYYY-MM-DD, entry URL`

Invalid metric sources:

- the model's memory;
- uncited claims in the agent's own prose;
- social media summaries;
- a paper title without a PDF/proceedings link;
- a metric without dataset/split/setting context.

## Search Recipe

For each target topic, run searches covering:

- `[task] [metric] [dataset] [target venue] [year]`
- `[task] SOTA [dataset] [year]`
- `[method family] [failure mode] [benchmark]`
- `[target venue] [subfield] [dataset]`
- `[paper title or authors] code`
- current-year accepted papers or proceedings if available

Search at least 5 query formulations. Include naming variants, old dataset names, abbreviations, and neighboring tasks.

## Inclusion Rules

Include a paper when it satisfies at least one:

- published in the target CCF A venue or an adjacent top venue in the last 3 years;
- widely treated as SOTA for the exact benchmark;
- closest mechanism-level prior work for a candidate idea;
- recent negative or diagnostic paper explaining why a popular direction fails.

Exclude or quarantine:

- papers without verifiable metrics;
- incomparable private data unless the target idea uses the same setting;
- leaderboard-only entries without enough methodological detail;
- arXiv-only claims when there are enough peer-reviewed alternatives, unless the work is very recent and central.

## Extraction Table

Create a table with these columns:

| Paper | Venue/Year | Retrieval Path | Source URL | PDF/Proceedings Link | Locator | Task | Dataset/Split | Metric | Reported Score | Baseline In Paper | Model/Backbone | Training/Inference Setting | Code | Comparable? | Verification | Notes |
|---|---|---|---|---|---|---|---|---|---:|---:|---|---|---|---|---|---|

For "Verification", use:

- `VERIFIED`: source URL plus locator found; setting is clear.
- `PARTIAL`: source and locator found, but one setting detail is incomplete.
- `UNVERIFIED`: source or locator missing; do not use as direct target.
- `CONFLICT`: sources disagree; preserve both and explain.

For "Comparable?", use:

- `YES`: same task, dataset, split, metric, and similar model scale.
- `PARTIAL`: same task but different backbone, data, split, prompt policy, training budget, or inference setting.
- `NO`: useful context but not a valid numeric target.
- `UNKNOWN`: key setup information missing.

## Minimum Visible Improvement

For each benchmark, set a target threshold before proposing experiments:

- saturated accuracy/F1: require a gain larger than typical seed variance, or claim efficiency/robustness instead;
- generative metrics: require human/LLM-judge, win-rate, or error-type evidence if automatic metrics are weak;
- efficiency claims: report quality at fixed cost and cost at fixed quality;
- robustness/safety claims: require stress sets or targeted failure categories, not only average score;
- new dataset or setting: require strong baseline coverage or diagnostic value.

Write thresholds as concrete statements:

- "Need at least +1.0 F1 on Dataset A under the same backbone, or +30% latency reduction at no quality loss."
- "A pilot signal is positive if the new loss improves long-tail recall by 2 points on the same split with one seed."

## Reproduction Decision

Default: do not reproduce baselines yet.

Ask the user about reproduction when any condition is true:

- no directly comparable recent number exists;
- the candidate changes dataset, split, model scale, prompt protocol, or training budget;
- reported gains are smaller than likely variance;
- official code is missing or incompatible;
- a reviewer would likely challenge fairness;
- the user is moving from ideation to submission-stage experiments.

Suggested user question:

```text
I can proceed in two ways:
1. Fast signal: run the top idea against reported recent metrics first.
2. Fair baseline: reproduce one or more baselines before testing the idea.

Given the metric board, I recommend [1/2] because [reason]. Which path do you want?
```

## Audit Checks

Before finalizing the metric board:

- every numeric value used as direct comparison has a source link and locator;
- every comparison says whether it is direct, partial, or not comparable;
- every direct SOTA target is `VERIFIED`, not `UNVERIFIED`;
- no paper is counted as SOTA outside its exact setup;
- current-year and last-6-month concurrent work were checked;
- the board states what is still unknown.
