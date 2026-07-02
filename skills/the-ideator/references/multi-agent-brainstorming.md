# Role-Specific Multi-Agent Brainstorming Protocol

Use this file when the agent environment supports subagents, background threads, or parallel workers. The goal is not just breadth; it is role separation that reduces homogeneous ideas.

If subagents are unavailable, simulate the same roles sequentially and state that no true parallel subagents were available.

## Availability Record

Before brainstorming, record:

```markdown
**Subagent availability**: YES/NO
**Mechanism used**: subagent tool / background threads / sequential simulation / other
**Roles launched**: Agent A / Agent B / Agent C
**Fallback reason if NO**:
```

## Required Roles

Launch or simulate these three roles in order. Agent A and Agent B may run in parallel. Agent C must review their outputs after they return.

| Agent | Role | Mandate | Required Output |
|---|---|---|---|
| Agent A | Cross-Domain Explorer | Seek transferable methods from other AI domains, such as adapting NLP paradigms to CV, graph methods to retrieval, RL/search to reasoning, or diffusion/flow mechanisms to generation/control. | 3-5 transfer ideas, each with theoretical justification. |
| Agent B | Defect Sniper | Analyze recent surveys and target-domain papers to identify widely acknowledged but unsolved defects, bottlenecks, or evaluation blind spots. | 3-5 defect-driven ideas grounded in survey/open-problem evidence. |
| Agent C | Devil's Advocate/Reviewer | Act as a strict CCF A reviewer. Critique, merge, refine, and filter Agent A/B outputs. | accept/revise/reject verdicts, fatal objections, and improved versions. |

## Agent A Prompt

```text
You are Agent A, Cross-Domain Explorer for The Ideator.

Target contract:
[brief target]

Local prior avoid/revisit map:
[brief avoid list]

Metric board summary:
[verified metrics and comparability notes]

Idea source ladder:
[target-field gaps, cross-field candidates, survey taxonomy]

Generate 3-5 cross-domain transfer ideas. For each:
- source domain and source method;
- target-domain failure mode;
- mechanism-level adaptation;
- Theoretical Justification explaining why the migration is mathematically, statistically, algorithmically, or structurally sound;
- why this is not naive A+B module stacking;
- closest prior work to check;
- expected metric movement;
- minimum experiment;
- local-prior overlap;
- kill condition.
```

Agent A must not propose a transfer without a theoretical justification. Reject analogies based only on surface similarity.

## Agent B Prompt

```text
You are Agent B, Defect Sniper for The Ideator.

Target contract:
[brief target]

Recent surveys and target-field papers:
[survey/taxonomy/open-problem summary]

Metric board summary:
[verified metrics and comparability notes]

Local prior avoid/revisit map:
[brief avoid list]

Identify 3-5 universally acknowledged but unsolved defects or bottlenecks in the specified domain. For each:
- survey or paper evidence;
- defect/bottleneck statement;
- why current methods have not solved it;
- method idea to attack it;
- dataset/metric where the defect appears;
- minimum experiment;
- local-prior overlap;
- likely reviewer objection;
- kill condition.
```

Agent B should prefer defects supported by multiple surveys/papers or repeated benchmark failures.

## Agent C Prompt

```text
You are Agent C, Devil's Advocate/Reviewer for The Ideator.

Inputs:
- Agent A transfer ideas;
- Agent B defect-driven ideas;
- target contract;
- avoid/revisit map;
- verified metric board;
- source ladder.

Act as a strict CCF A reviewer. For each idea:
- verdict: ACCEPT / REVISE / REJECT;
- strongest novelty objection;
- strongest experimental fairness objection;
- local-prior duplication risk;
- metric verification risk;
- whether Agent A's theoretical justification is sufficient;
- whether Agent B's defect evidence is strong enough;
- concrete refinement if REVISE;
- final kill reason if REJECT.

Then produce a merged shortlist of 3-6 ideas with no near-duplicates.
```

Agent C does not have final authority. The main agent still verifies sources, deduplicates, scores, and ranks.

## Main Agent Responsibilities

The main agent must:

1. Run or simulate Agent A and Agent B independently before Agent C sees their outputs.
2. Give Agent C the raw Agent A/B outputs.
3. Merge Agent C's reviewed shortlist into the candidate pool.
4. Remove duplicates and near-duplicates.
5. Discard candidates that violate local-prior constraints.
6. Run public novelty checks on the survivors.
7. Score the survivors using `references/idea-scoring.md`.
8. Preserve rejected ideas in an eliminated-ideas table with kill reasons.

Subagents can propose and critique. They cannot decide final novelty, SOTA status, or readiness.

## Deduplication

Treat ideas as duplicates if they share the same:

- core mechanism;
- target failure mode;
- metric claim;
- closest prior distinction.

Keep the sharper version and merge useful details from the weaker version.

## Diversity Requirement

The final top 3 should not be minor variants of the same mechanism. At least one top idea should be defect-driven and at least one should be transfer-driven when both survive Agent C review. If all strong ideas cluster around one mechanism, say so and provide either:

- one main idea plus two ablation/extension paths; or
- a request for a narrower or different target.

## Output Snippet

Add this section to the final report:

```markdown
## Role-Specific Multi-Agent Brainstorming Summary

Subagent availability: [YES/NO]
Mechanism used: [tool or fallback]
Roles launched: Agent A Cross-Domain Explorer; Agent B Defect Sniper; Agent C Devil's Advocate/Reviewer
Fallback reason if any:

| Agent | Useful ideas contributed | Rejected/refined because |
|---|---|---|

Agent C review summary:
Merged candidate count:
Duplicate clusters:
Final survivors:
```
