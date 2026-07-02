# Idea Scoring And Report Template

Use this file after candidate generation and metric harvesting.

## Candidate Card

Each candidate must fit this template:

```markdown
### Idea [N]: [Short title]

**Method in concrete steps**
1. [Build/train/modify step 1]
2. [Step 2]
3. [Step 3]

**Problem anchor**: Current methods can [X], but fail under [Y] because [Z].
**Hypothesis**: If we [mechanism], then [metric/failure mode] should improve because [reason].
**Idea source**: target-field gap / cross-field transfer / survey-derived combination / hybrid.
**Source-ladder evidence**: [Tier 1/Tier 2/Tier 3 evidence].
**Theoretical justification**: [required for cross-domain transfer ideas; explain mathematical/statistical/algorithmic/structural fit].
**Survey defect evidence**: [required for defect-driven ideas; cite survey/paper evidence].
**Local-prior overlap**: NO_OVERLAP / RELATED_BUT_DIFFERENT / OVERLAPS_AVOID / REVIVE_WITH_CHANGE.
**Overlap evidence**: [prior idea id + source path, or "none found"].
**Closest prior work**: [paper + link], [paper + link].
**Differentiation**: Unlike [prior], this idea changes [mechanism/setting/evidence].
**Metric target**: [dataset/split/metric + minimum visible improvement + verified source locator].
**Minimum experiment**: [smallest test with data, code, compute, expected runtime].
**Ablations**: [component removals or controls].
**Reviewer objection**: [strongest objection].
**Kill condition**: [result that makes us stop].
**AutoSOTA-style recovery**: [not needed / planned / run / recommended before killing].
**Next action**: [metric board only / pilot / signal recovery / reproduce baseline / discard].
```

## Scoring Rubric

Score 1-5. Use half points when needed.

| Dimension | 1 | 3 | 5 |
|---|---|---|---|
| Novelty | Already done | Incremental but defensible | Mechanism-level or evidence-level novelty |
| Source diversity | Same saturated family | One clear source tier | Strong hybrid of target gap, transfer, or survey taxonomy |
| Evidence verification | Relies on memory or unverified metric | Partial locator or source | Verified source URL plus table/page/section locator |
| Local-prior distance | Repeats user-tried idea | Related but materially different | Clearly avoids or upgrades local prior ideas |
| CCF A fit | Workshop-level | Borderline main track | Clear main-track technical contribution |
| Metric leverage | No measurable path | One plausible metric | Multiple strong metrics or a better claim axis |
| Feasibility | Requires unavailable assets | Needs moderate engineering | Testable quickly with accessible code/data |
| Reviewer robustness | Fatal obvious objection | Fixable objections | Strong answer to likely objections |
| Insight value | Only positive result matters | Some diagnostic value | Useful even if result is negative |
| Scalability | Toy-only | Can scale with effort | Natural path to full benchmark/submission |

Compute:

```text
Total = Novelty + Source diversity + Evidence verification + Local-prior distance + CCF A fit + Metric leverage + Feasibility + Reviewer robustness + Insight value + Scalability
```

Recommendation:

- 43-50: pursue first.
- 37-42.5: backup or refine.
- 30-36.5: keep only if cheap and strategically useful.
- below 30: discard unless the user has special constraints.

## Elimination Reasons

Use precise kill labels:

- `ALREADY_DONE`: closest public prior work has the same mechanism and claim.
- `LOCAL_DUPLICATE`: repeats an idea already found in the user's local project notes.
- `NO_METRIC_PATH`: no credible benchmark or measurable claim.
- `WEAK_VENUE_FIT`: unlikely to interest target CCF A main track.
- `TOO_EXPENSIVE`: compute/data/time out of scope.
- `TUNING_ONLY`: likely viewed as engineering/tuning without insight.
- `UNFAIR_COMPARISON`: would require extensive baseline reproduction before any claim.
- `LOW_SIGNAL`: pilot or metric board suggests gains are below noise. This label is invalid unless the AutoSOTA-style recovery decision is recorded as executed, planned, or skipped with an explicit inapplicability reason.
- `UNVERIFIED_METRIC`: metric lacks retrieval source, PDF/proceedings link, or table/page/section locator.
- `NAIVE_TRANSFER`: cross-domain idea lacks theoretical justification or is just module stacking.

## Final Report Skeleton

```markdown
# The Ideator Report

**Topic**:
**Target venue/track**:
**Date**:
**Mode**: quick / rigorous / implementation-ready

## 1. Executive Recommendation

| Rank | Idea | Verdict | Why now | First experiment | Reproduction decision |
|---:|---|---|---|---|---|

## 2. Target Contract

[problem anchor, venue fit, contribution type, constraints]

## 3. Local Prior Ideas

[summary of prior_ideas.md and avoid_list.md]

## 4. Recent Metric Board Summary

[3-8 key papers and numbers; link to full board if separate]

## 5. Strict Metric Verification Audit

| Metric claim | Verification | Source URL | Locator | Usable as direct target? |
|---|---|---|---|---|

## 6. Idea Source Ladder

[target-field gaps, cross-field transfer candidates, survey-derived taxonomy]

## 7. Red Ocean Map

[method clusters, saturated axes, real gaps, false gaps]

## 8. Role-Specific Multi-Agent Brainstorming Summary

Subagent availability: [YES/NO]
Mechanism used:
Agents used: Agent A Cross-Domain Explorer; Agent B Defect Sniper; Agent C Devil's Advocate/Reviewer; or sequential simulation.
Fallback reason if any:
Candidate count:
Duplicate clusters:
Final survivors:

## 9. Ranked Idea Cards

[candidate cards]

## 10. Eliminated Ideas

| Idea | Kill label | Evidence | Local-prior overlap |
|---|---|---|---|

For any `LOW_SIGNAL` kill label, include the AutoSOTA-style recovery decision or skip reason in the evidence cell.

## 11. Baseline Reproduction Decision

Recommended path: [fast signal / small reference baseline / full suite]
Reason:
User decision needed:

## 12. AutoSOTA-Style Signal Recovery

Status: [skipped / planned / executed]
Budget:
Search space:
Best valid trial:
Audit verdict:
Decision:

## 13. Minimum Experiment Plan

[implementation sketch, metrics, datasets, ablations, compute, timeline]

## 14. Reviewer Risk Register

| Risk | Severity | How to answer | Evidence needed |
|---|---|---|---|

## 15. Platform Adapter Limitations

[Codex/Claude/search/subagent/filesystem/shell limitations encountered]

## 16. Submission Readiness Conditions

- [ ] Novelty survives closest-prior check.
- [ ] Top idea does not repeat the user's local prior ideas unless materially reframed.
- [ ] Idea source is not trapped in a single saturated method family unless justified.
- [ ] Direct metric targets are retrieval-verified with source URL and table/page/section locator.
- [ ] Cross-domain transfer ideas include theoretical justification.
- [ ] Main metric beats threshold or alternative claim is stronger.
- [ ] Ablations isolate the mechanism.
- [ ] Baseline fairness is handled.
- [ ] Paper thesis is clear enough for the target venue.
```

## Quality Bar

The top idea should have:

- one clear mechanism change, not a bag of tricks;
- one benchmark path and one reviewer-facing story path;
- a falsifiable minimum experiment;
- an explicit closest-prior distinction;
- an answer to "why would this be CCF A main-track rather than incremental?"
