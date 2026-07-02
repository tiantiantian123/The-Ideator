# Idea Source Ladder

Use this file before candidate generation. The goal is to avoid shallow "apply X to Y" ideation while still escaping a saturated target field.

## Output File

Create `idea-forge/source_ladder.md` with:

```markdown
# Idea Source Ladder

## Tier 1: Target-Field Recent A-Venue Gaps

| Paper | Venue/Year | Method family | Reported strength | Stated or observed limitation | Improvement direction |
|---|---|---|---|---|---|

## Tier 2: Cross-Field Transfer Candidates

| Source field | Source method | Venue/Year | Mechanism | Target-field failure mode | Required adaptation | Risk |
|---|---|---|---|---|---|---|

## Tier 3: Survey-Derived Diversity Map

| Survey | Year | Taxonomy branch | Representative methods | Underexplored combination | Target-field opportunity |
|---|---|---|---|---|---|
```

## Tier 1: Target-Field Recent A-Venue Gaps

Start here. Search recent target-field papers from the target CCF A venue and adjacent top venues.

Extract:

- what each method optimizes;
- what failure mode or setting remains weak;
- what limitation the authors admit;
- which benchmark metric is saturated or misleading;
- what change would be non-incremental.

Generate improvement directions only when the limitation is concrete: data regime, failure type, metric mismatch, deployment constraint, theory gap, or system bottleneck.

## Tier 2: Cross-Field Method Transfer

Run a separate search over adjacent or distant AI subfields from the last 3 years. Look for method mechanisms, not task names.

Examples of transferable mechanisms:

- retrieval, memory, routing, sparsity, calibration, uncertainty, causal intervention, contrastive objectives, diffusion/flow matching, test-time adaptation, active data selection, program synthesis, graph propagation, self-refinement, curriculum, distillation, preference optimization, constrained decoding, verifier-guided search.

For each transfer, write an analogy proof:

```markdown
**Source mechanism**:
**Why it worked in source field**:
**Target failure mode**:
**Required adaptation**:
**What would make it nontrivial**:
**What metric should move**:
**Closest public prior to check**:
```

Reject a transfer if:

- it only changes vocabulary and not mechanism;
- the target field already has an equivalent method;
- no benchmark can isolate the claimed effect;
- the required adaptation is mostly engineering without a research claim.

## Tier 3: Survey-Derived Diversity

If Tier 1 is sparse, saturated, or too homogeneous, search for recent survey papers in the target field from the last 3 years. Use surveys to broaden the method taxonomy.

Extract:

- major method families and subfamilies;
- neglected branches with few strong recent papers;
- method combinations the survey implies but does not resolve;
- recurring open challenges;
- benchmarks or settings the survey says are underused;
- disagreements between surveys and recent A-venue papers.

Use survey taxonomies to force diversity. The final candidate pool should not all come from the same method family if the survey shows several viable families.

## Combining Tiers

Strong candidates often combine:

- a Tier 1 target-field failure;
- a Tier 2 mechanism that has succeeded elsewhere;
- a Tier 3 taxonomy branch showing why the combination is underexplored.

Candidate template:

```markdown
**Target-field failure from Tier 1**:
**Transferred mechanism from Tier 2**:
**Survey taxonomy support from Tier 3**:
**New method claim**:
**Why this is not just apply-X-to-Y**:
**Metric threshold**:
```

## Quality Checks

Before brainstorming:

- at least 3 target-field recent papers were checked, unless the field is too new or narrow;
- at least 2 cross-field source areas were considered;
- at least 1 recent survey was checked when the direct field lacks diversity;
- every transfer idea has an explicit mechanism analogy;
- the source ladder is cross-checked against `idea-forge/avoid_list.md`.
