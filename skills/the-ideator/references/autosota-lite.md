# AutoSOTA-Style Signal Recovery

Use this file when a method idea is conceptually plausible but an initial experiment is weak. The goal is to decide whether the method is truly bad or merely under-optimized.

This protocol adapts useful AutoSOTA-style behavior into a lightweight research-skill workflow: paper/code grounding, structured objectives, iterative idea execution, score logging, rollback, and independent validity checks. It does not require the AutoSOTA CLI unless the user wants to run it and has a runnable local repository.

## When To Use

Use this stage when all are true:

- there is a concrete candidate method and metric;
- a local or public codebase can run at least a small evaluation;
- a first result is weak, unstable, or below recent reported numbers;
- failure may come from settings rather than the core idea.

Skip this stage when:

- the idea is already killed by novelty or venue fit;
- no runnable evaluation exists;
- the metric board shows the required gain is far beyond plausible variance;
- the user only requested paper-level ideation.

## Resource And Objective Setup

Create `idea-forge/autosota_plan.md`:

```markdown
# AutoSOTA-Style Optimization Plan

**Candidate idea**:
**Repo / code path**:
**Eval command**:
**Primary metric**:
**Direction**: higher/lower is better
**Reference value**: reported metric or local baseline
**Guardrail metrics**: latency, memory, robustness, fairness, cost, secondary accuracy, etc.
**Budget**: iterations, wall time, GPU hours, max debug attempts
**Forbidden changes**: eval script, test labels, dataset split, leakage-prone preprocessing, manual test-set tuning
**Stop rules**:
```

Make objectives multi-metric when needed. A gain is not valid if it improves the headline metric by sacrificing a guardrail the paper claim depends on.

## Red Lines

Never count an improvement if it depends on:

- changing the evaluation script, metric definition, test split, or label files without explicitly reframing the claim;
- tuning directly on the hidden or official test set;
- adding data not allowed by the comparison protocol;
- silently changing model scale, pretraining data, or inference budget while comparing to fixed-budget numbers;
- cherry-picking the best seed without reporting variance;
- fixing a bug in a way that invalidates comparison with the original method;
- moving a method from fair training to transductive or test-time adaptation without saying so.

If a potentially valid change modifies evaluation hygiene, separate it as a diagnostic finding rather than a SOTA claim.

## Hypothesis Library

Generate 10-25 optimization hypotheses before editing code. Group them:

| Group | Examples |
|---|---|
| Training recipe | learning rate, warmup, scheduler, weight decay, batch size, gradient clipping, EMA, label smoothing |
| Data pipeline | augmentation strength, sampling, class balance, context length, normalization, missing-value handling |
| Inference recipe | threshold, decoding, self-consistency, ensembling, TTA, calibration, post-processing |
| Objective/loss | auxiliary loss, contrastive term, robust loss, margin, temperature, distillation |
| Architecture small moves | pooling, residual gate, adapter rank, attention mask, normalization, feature fusion |
| Efficiency knobs | precision, cache, pruning, early exit, candidate pruning, vectorization |
| Implementation hygiene | device placement, seed control, checkpoint loading, train/eval mode, metric aggregation |

For each hypothesis, record:

- expected direction and affected metric;
- why it might rescue the idea;
- risk to comparability;
- estimated runtime;
- rollback condition.

## Iteration Loop

Run a bounded loop:

1. Freeze a clean baseline commit or copy.
2. Run or verify one reference score if feasible.
3. Pick one or a small compatible bundle of hypotheses.
4. Make the smallest code/config change.
5. Run the evaluation.
6. Append to `idea-forge/scores.jsonl`:
   - iteration id;
   - change summary;
   - metric values;
   - guardrail values;
   - runtime;
   - commit/diff path;
   - validity notes.
7. Keep the best valid change; roll back invalid or regressing changes.
8. Reflect using the score trajectory before choosing the next hypothesis.

Suggested budgets:

- Scout: 3-5 iterations, cheap subset, used during early ideation.
- Recovery: 8-12 iterations, one main benchmark, used before killing a plausible top idea.
- Submission prep: 16-24+ iterations, multi-seed and full benchmark, requires explicit user approval.

## Independent Audit

Before accepting a recovered gain:

- rerun the best configuration at least once, or state why not;
- compare against the frozen baseline under the same command;
- inspect the diff for red-line violations;
- check whether the gain exceeds likely seed/noise variance;
- verify that secondary metrics did not collapse;
- write a short read-only verdict:

```markdown
## AutoSOTA-Style Audit Verdict

**Best valid score**:
**Delta vs reference**:
**Comparable?** YES/PARTIAL/NO
**Red-line issues**:
**Variance risk**:
**Guardrail impact**:
**Decision**: accept signal / need baseline reproduction / continue optimization / kill idea
```

## If AutoSOTA CLI Is Available

If the user explicitly wants automated optimization and has a runnable local repository, the workflow may call AutoSOTA or a similar tool instead of manually orchestrating the loop.

Required inputs:

- local cloned repo that already has runnable dependencies or can be repaired;
- `target.md` with paper name, task, primary metric, baseline value, direction, and guardrails;
- optional paper PDF and priors;
- explicit budget: max iterations, max wall time, devices.

Preferred mode:

- use idea review or priors to seed the candidate method;
- set budget by `max_iterations` and wall time, not by "stop when target percent is reached";
- keep a separate research model or verified web search for literature grounding if available;
- require a final independent read-only evaluation verdict.

Do not install or run AutoSOTA without user approval. Treat its output as experimental evidence that still needs scientific review.

## Final Decision Rules

- **Accept signal**: gain beats threshold, guardrails hold, and audit finds no red-line issue.
- **Needs tuning**: trajectory improves but has not crossed the threshold; recommend more budget or better search space.
- **Needs fair baseline**: result depends on a changed setup or reported numbers are not comparable.
- **Kill idea**: multiple reasonable settings fail, metric stays below noise, or the only gains are invalid.
