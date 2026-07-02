---
name: the-ideator
description: Generate, stress-test, and rank new AI method ideas aimed at CCF A or top-tier main technical tracks. Use when the user gives an AI subfield, task, paper, benchmark, venue, or broad direction and wants publishable SOTA-oriented ideas, novelty checks, local prior project idea deduplication, strictly verified recent CCF A metric comparison with paper/table/page evidence, cross-field method transfer with theoretical justification, survey-based defect mining, role-specific multi-agent brainstorming, reviewer objections, minimum experiments, AutoSOTA-style signal recovery, or a decision on whether baseline reproduction is needed. Designed with a decoupled core engine plus Codex/Claude environment adapters.
---

# The Ideator

## Purpose

Help a researcher move from a target AI subfield to a small set of method ideas that are plausible for a CCF A or top-tier main technical track.

Optimize for early rejection of weak ideas, fast metric-based sanity checks, local-prior deduplication, and clear next experiments. Do not imply that acceptance or SOTA is guaranteed.

## Core Defaults

- Follow the user's language; use Chinese if the user asks in Chinese.
- Verify recent literature and metrics with browsing or primary sources. Do not rely on memory for SOTA, venue status, or recent numbers. If browsing is unavailable, ask the user for papers, links, PDFs, or metric tables and mark unsupported values `[UNVERIFIED]`.
- Treat metric verification as fail-closed: a metric is not usable for SOTA comparison unless it has a source URL plus table/page/section locator or an explicit `[UNVERIFIED]` label.
- Evidence window: the latest 3 complete years plus the current year if accepted papers or proceedings are available.
- Venue scope: the target CCF A venue plus 2-4 adjacent top venues in the same AI subfield.
- Experiment posture: harvest reported metrics first, reproduce baselines later.
- Weak-result posture: do not kill a mechanism-level idea after one weak run if settings, recipe, evaluation protocol, or implementation details could plausibly explain the result.
- Prior-work posture: before ideation, ask whether the user has local projects containing already-tried ideas; if yes, read all Markdown files and build an avoid/revisit map.
- Idea-source posture: use target-field recent A-venue problems first, cross-field recent method transfer second, and recent survey taxonomies third when the direct field is sparse or homogeneous.
- Brainstorming posture: use role-specific parallel subagents when supported: Agent A Cross-Domain Explorer, Agent B Defect Sniper, and Agent C Devil's Advocate/Reviewer.
- Architecture posture: keep the core thinking and idea generation engine separate from platform-specific environment interaction; read `references/dual-platform-architecture.md` before implementing wrappers, scripts, or platform-specific behavior.
- Artifact directory: `idea-forge/`.

## Intake Gate

If the request is too broad, ask for narrowing before generating ideas. "NLP", "CV", "LLM", or "multimodal" alone is too broad.

Collect or infer:

1. Target subfield and task, such as long-context RAG, text-to-SQL, offline RL, graph anomaly detection, 3D detection, diffusion acceleration, or multimodal hallucination.
2. Target venue or venue family, such as ACL/EMNLP, CVPR/ICCV/ECCV, KDD/SIGIR, AAAI/IJCAI, ICML/NeurIPS/ICLR if the user treats them as target top AI venues.
3. Main benchmark, dataset, or application setting.
4. Available compute, data, codebase, timeline, and implementation constraints.
5. Local project directories containing already-tried ideas, notes, drafts, or failed attempts.
6. Adjacent fields that are allowed or disallowed as inspiration sources.
7. Ideas already tried, known dead ends, and directions the user wants to avoid.
8. Whether the user wants quick ideation, rigorous ideation, or implementation-ready planning.

If some details are missing but the topic is specific enough, proceed with explicit assumptions. Always ask for local prior project paths before final ideation.

## Workflow

### Architecture Boundary

Before changing code, adding wrappers, or describing implementation, read `references/dual-platform-architecture.md`.

Use the same core workflow for Codex and Claude Code:

- core engine: target contract, prior idea map, metric verification, source ladder, multi-agent idea generation, scoring, and final report schema;
- environment adapters: file reads, Markdown scanning, browser/search/PDF access, CLI commands, UI prompts, subagent execution, and artifact writing.

Do not put Codex-only or Claude-only behavior inside the core reasoning contract. Put platform differences in the environment interaction layer.

### 0. Ingest Prior Local Projects

Immediately after the user gives the research field or target task, ask:

```text
Do you have local project directories that contain ideas you have already tried or discussed? If yes, give me the paths. I will read the Markdown files and build an avoid/revisit map before generating new ideas.
```

If the user provides directories, read `references/prior-project-intake.md` and create:

- `idea-forge/prior_project_inventory.md`: every provided directory and discovered Markdown file.
- `idea-forge/prior_ideas.md`: summarized ideas, methods, experiments, conclusions, and status.
- `idea-forge/avoid_list.md`: ideas to avoid, ideas that can be revisited only with a material change, and unresolved directions.

If the user has no local project directories, still create `prior_project_inventory.md`, `prior_ideas.md`, and `avoid_list.md`, each stating that no user-local prior corpus was provided.

Every candidate idea must include a local-prior overlap verdict:

- `NO_OVERLAP`: materially different from recorded local ideas.
- `RELATED_BUT_DIFFERENT`: related area, but different mechanism, setting, or claim.
- `OVERLAPS_AVOID`: too close to an already-tried idea; discard or reframe.
- `REVIVE_WITH_CHANGE`: previously weak idea that may deserve AutoSOTA-style recovery or a different claim.

### 1. Freeze The Target Contract

Create `idea-forge/target_contract.md` with:

- target task and exact problem anchor;
- target venue and main technical track fit;
- expected contribution type: new method, benchmark insight, diagnostic, theory, system, or data-centric method;
- minimum publishable claim;
- available resources and non-goals;
- local prior idea constraints from `idea-forge/avoid_list.md`;
- success metrics and acceptable compute budget.

Use a FINER-style screen adapted for AI methods:

| Criterion | Question |
|---|---|
| Feasible | Can the idea be tested with accessible code, data, and compute? |
| Interesting | Would a top-venue reviewer care if the result were true or false? |
| Novel | Is the mechanism or finding clearly different from recent public work and local prior ideas? |
| Evaluable | Are datasets, metrics, and ablations available? |
| Relevant | Does it match the target venue's technical taste and current problems? |

Reject or narrow topics with a fatal score on Feasible or Evaluable.

### 2. Build The Recent Metric Board

Before generating final ideas, read `references/metric-harvesting.md` and build `idea-forge/metric_board.md`.

Extract reported metrics from recent CCF A or adjacent top-venue papers instead of starting with baseline reproduction. For each relevant paper, capture:

- venue, year, title, official link, code link if available;
- retrieval source used, such as official proceedings, arXiv API, Semantic Scholar API, OpenReview, ACL Anthology, CVF, ACM, IEEE, PMLR, or web search;
- PDF/proceedings link and exact table/page/section locator for the metric;
- exact task, dataset, split, metric, backbone/base model, training budget, inference setting;
- main reported score and strongest baseline score;
- whether the number is directly comparable to the user's intended setting;
- implementation availability and reproduction risk.

Do not rely on model memory for numeric metric values. If the source cannot be retrieved or the table/page cannot be located, mark the value `[UNVERIFIED]` and do not use it as a direct SOTA target.

Use the board to set a minimum visible improvement for each candidate idea, such as absolute gain, relative gain, cost reduction, robustness gain, or new capability.

Do not reproduce baselines by default. Ask the user whether to reproduce baselines only after the board shows that reproduction is necessary or strategically useful.

### 3. Map The Red Ocean

Before mapping gaps, read `references/idea-source-ladder.md` and create `idea-forge/source_ladder.md`.

Use three source tiers:

1. Target-field recent A-venue methods and their limitations.
2. Recent methods from other AI subfields that can be transferred or combined with the target field.
3. Recent survey papers in the target field, especially when recent A-venue papers are too sparse, too homogeneous, or too incremental.

Create `idea-forge/gap_map.md`:

- cluster recent methods by mechanism, not by paper title;
- identify saturated axes where reviewers will say "incremental";
- list recurring limitations from introductions, limitations sections, appendices, and benchmark failures;
- mark false gaps where a tempting idea has already been done publicly or locally;
- record cross-field mechanisms that might transfer, including what must change for the target field;
- use survey taxonomies to force method diversity when the direct literature is narrow;
- write 3-5 real problem statements in the form: "Current methods can X, but fail under Y because Z."

Keep examples concrete. A gap is not valid unless it names a task condition, failure mode, dataset regime, or mechanism-level limitation.

### 4. Generate Candidates With Parallel Lenses

If subagents are available and the task is non-trivial, read `references/multi-agent-brainstorming.md` and launch the required role-specific agents. If subagents are unavailable, simulate the same roles sequentially and say so.

Required roles:

- Agent A, Cross-Domain Explorer: seek methods from other domains and provide a theoretical justification for every transfer.
- Agent B, Defect Sniper: mine recent surveys and target-field literature for widely acknowledged but unresolved defects or bottlenecks.
- Agent C, Devil's Advocate/Reviewer: critique and refine Agent A and Agent B outputs as a strict CCF A reviewer.

Generate 8-15 candidates using independent lenses:

- mechanism transfer: a method works in one AI subarea but is not yet adapted to this failure mode;
- cross-field combination: combine a recent method from another AI domain with the target field only when the mechanism-level analogy is explicit;
- assumption break: the field relies on an assumption nobody has stress-tested;
- metric mismatch: current SOTA optimizes the wrong proxy;
- data/regime shift: results may change under scale, distribution, long-tail, low-resource, or deployment constraints;
- diagnostic-to-method: a failure analysis suggests a trainable fix;
- efficiency-for-accuracy: same quality at lower cost, latency, memory, or labels;
- controllability/robustness: add guarantees, calibration, safety, or reliability;
- theory-to-practice: convert an explanatory principle into an algorithmic component.

For each candidate, state:

- method in 2-4 concrete steps;
- core hypothesis;
- local-prior overlap verdict;
- closest public prior work;
- expected metric movement;
- minimum experiment;
- likely reviewer objection;
- why the answer matters even if the idea fails.

### 5. Novelty And Reviewer Triage

Do not keep ideas because they sound clever. For every candidate:

1. Run targeted novelty searches using the idea's mechanism, not only the task name.
2. Search recent arXiv and accepted papers from the last 3-6 months for concurrent work.
3. Identify the closest public prior work and write a differentiation statement.
4. Compare against `idea-forge/prior_ideas.md` and `idea-forge/avoid_list.md`; discard or reframe candidates that repeat the user's already-tried ideas.
5. Apply a hostile reviewer pass:
   - "This is just X plus Y."
   - "The gain could come from tuning, scale, or data leakage."
   - "The benchmark is saturated or unconvincing."
   - "The method is too complex for the gain."
   - "The claimed novelty is a missing citation."
6. Keep only candidates with a defensible novelty path, no fatal local-prior overlap, and a credible first experiment.

When available, use a different model or independent reviewer for verdicts. Same-model generation is allowed for breadth; same-model acquittal is not enough for novelty or quality.

### 6. Decide Whether To Reproduce Baselines

After metric harvesting and triage, present a reproduction decision to the user:

- **Skip reproduction for now** when recent papers report directly comparable metrics, code is available, and the first goal is to test whether the new mechanism has signal.
- **Reproduce a small reference baseline** when the metric board has incompatible settings, missing implementation details, suspicious gains, or the user's intended setting changes data/model/training substantially.
- **Reproduce a baseline suite** only when preparing a serious submission-stage experiment plan or when the idea's claim depends on fair reimplementation.

Ask explicitly: "Do you want me to reproduce baselines now, or first run the minimum experiment for the top idea against reported numbers?"

### 7. Recover Signal With AutoSOTA-Style Optimization

When a candidate has a credible mechanism but the first result is weak, do not immediately discard it. Read `references/autosota-lite.md` and run or propose a bounded optimization loop before final judgment.

Use this stage when:

- a runnable repo, evaluation command, and metric exist;
- the idea is plausible but underperforms reported numbers or a local baseline;
- the likely failure may be hyperparameters, training recipe, inference settings, seed variance, data preprocessing, implementation bugs, or small architecture choices;
- the user asks for implementation-ready ideation or wants to know whether a low score is a real idea failure.

Keep this stage bounded. It is not a default full baseline reproduction suite. It is a signal recovery loop inspired by AutoSOTA: set a structured multi-metric objective, freeze evaluation red lines, generate a hypothesis library, iterate over code/config changes, log scores, roll back bad trials, and use an independent read-only audit before accepting gains.

Ask before running expensive optimization:

```text
The idea is still plausible, but the first result may be recipe-limited. I recommend an AutoSOTA-style bounded optimization pass: [N] iterations / [budget] / [metric guardrails]. Do you want me to run this, keep only the plan, or discard the idea?
```

### 8. Rank And Output

Read `references/idea-scoring.md` for the rubric. Produce `idea-forge/IDEA_FORGE_REPORT.md` with:

1. Executive recommendation: top 1-3 ideas and why.
2. Target contract.
3. Local prior idea summary and avoid/revisit map.
4. Recent metric board summary with links.
5. Idea source ladder and cross-field/survey inspiration map.
6. Strict metric verification audit with source/table/page coverage and unverified values.
7. Gap map.
8. Role-specific multi-agent brainstorming summary, including Agent A/B/C outputs and Agent C review verdicts.
9. Ranked idea cards.
10. Eliminated ideas and exact kill reasons, including local-prior overlap when relevant.
11. Reproduction decision and user choice point.
12. AutoSOTA-style signal recovery decision:
   - skipped, planned, or executed;
   - search budget and guardrail metrics;
   - best trial and whether gain survived audit;
   - reason if the idea is killed despite optimization.
13. Minimum experiment plan:
   - command-level or module-level implementation sketch when code exists;
   - datasets and splits;
   - metrics and expected threshold;
   - ablations;
   - compute estimate;
   - failure criteria.
14. CCF A framing:
   - one-sentence paper thesis;
   - contribution list;
   - reviewer risk register;
   - what must be true for this to become a submission.

## Hard Rules

- Do not fabricate papers, arXiv IDs, DOIs, or metric values. If a metric cannot be verified, mark it `[UNVERIFIED]`.
- Do not use an unverified metric as a direct comparison target. Every direct metric target needs a retrieval source, PDF/proceedings link, and table/page/section locator.
- Do not call something SOTA unless the comparison scope and date are explicit. Prefer "reported best among sampled papers" when coverage is incomplete.
- Do not ignore the user's local prior projects. If directories are provided, record their Markdown-derived ideas before generating final candidates.
- Do not bury negative evidence. Dead ends and already-done ideas are part of the deliverable.
- Do not default to expensive reproduction. The skill's default value is fast idea discrimination using recent reported metrics.
- Do not kill a good mechanism solely because the first pilot is weak. Check whether a bounded optimization pass is warranted.
- Do not use `LOW_SIGNAL` as a kill reason unless the AutoSOTA-style recovery decision is recorded as executed, planned, or skipped with an explicit inapplicability reason.
- Do not recommend an "apply X to Y" idea unless it changes the mechanism, benchmark interpretation, or reviewer-facing claim.
- Do not use cross-field transfer as a superficial analogy. Agent A must state the source-domain mechanism, target-domain failure mode, adaptation required, theoretical justification, and why the target benchmark can reveal the effect.
- Do not treat a survey as a citation dump. Use surveys to identify diverse method families, missing combinations, and underexplored failure modes.
- Do not let parallel brainstorming agents decide final quality. Agent C reviews Agent A/B outputs, and the main agent must merge, deduplicate, verify, and rank.
- Do not couple core idea logic to a specific client. Codex and Claude Code wrappers may differ, but the core engine schema and decisions must remain portable.
- Do not leave the user with only prose. Every recommended idea needs a minimum experiment and a metric threshold.

## Reference Files

- `references/prior-project-intake.md`: mandatory when the user provides local directories containing already-tried ideas.
- `references/metric-harvesting.md`: mandatory when building the recent CCF A metric board.
- `references/idea-source-ladder.md`: mandatory before candidate generation; defines target-field, cross-field, and survey-based inspiration sources.
- `references/multi-agent-brainstorming.md`: mandatory when using parallel brainstorming agents or simulating role-based brainstorming.
- `references/dual-platform-architecture.md`: mandatory when implementing or modifying Codex/Claude compatibility, wrappers, scripts, or environment-specific behavior.
- `references/autosota-lite.md`: mandatory when a plausible idea underperforms and settings or implementation details may be responsible.
- `references/idea-scoring.md`: mandatory when ranking candidates or writing the final report.
