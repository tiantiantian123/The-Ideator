# Dual-Platform Architecture

Use this file when implementing or modifying The Ideator for Codex, Claude Code, or any other Agent Skills client.

The architecture must be decoupled:

- `TheIdeatorCore`: platform-independent thinking and idea generation engine.
- `EnvironmentAdapter`: platform-specific file, search, PDF, shell, UI, and subagent capabilities.

The same core engine must be usable from both Codex and Claude Code. Platform wrappers may differ, but the workflow state, schemas, and decisions must remain the same.

## Layer 1: Core Thinking / Idea Generation Engine

The core owns:

- target contract schema;
- prior idea inventory schema;
- avoid/revisit map schema;
- strict metric verification schema;
- source ladder schema;
- role-specific multi-agent task contracts;
- candidate idea schema;
- reviewer triage and scoring rubric;
- baseline reproduction decision;
- AutoSOTA-style signal recovery decision;
- final report schema.

The core must not directly assume:

- a filesystem path format;
- a specific shell;
- a browser tool;
- a specific subagent API;
- Codex UI features;
- Claude Code CLI behavior.

## Layer 2: Environment Interaction Layer

The adapter owns:

| Capability | Codex Adapter | Claude Code Adapter | Core Interface |
|---|---|---|---|
| File inventory | workspace tools / shell | CLI filesystem tools | `list_markdown_files(paths)` |
| File read/write | workspace tools | CLI file ops | `read_text(path)`, `write_artifact(path, content)` |
| Retrieval/search | web tool if available | web/search MCP or user-provided papers | `retrieve_paper(query)` |
| PDF/table location | PDF tools or browser | CLI PDF tooling or user-provided locator | `locate_metric(source, metric_context)` |
| Subagents | Codex subagent tool | Claude task/subagent equivalent or sequential simulation | `run_role_agents(tasks)` |
| Shell/evaluation | sandboxed commands | CLI commands | `run_command(command, budget, permissions)` |
| User questions | Codex chat/UI | CLI prompt/chat | `ask_user(question)` |

If a platform lacks a capability, the adapter must return a structured limitation, not silently fake the result.

## Core Data Contracts

### Metric Evidence

```json
{
  "paper_title": "",
  "venue_year": "",
  "retrieval_path": "official|arxiv|semantic_scholar|openreview|web|user_provided",
  "source_url": "",
  "pdf_or_proceedings_url": "",
  "locator": "PDF p. X, Table Y",
  "task": "",
  "dataset_split": "",
  "metric": "",
  "reported_score": "",
  "baseline_score": "",
  "model_backbone": "",
  "training_inference_setting": "",
  "comparability": "YES|PARTIAL|NO|UNKNOWN",
  "verification": "VERIFIED|PARTIAL|UNVERIFIED|CONFLICT",
  "notes": ""
}
```

### Candidate Idea

```json
{
  "title": "",
  "source_type": "target_gap|cross_domain_transfer|survey_defect|hybrid",
  "method_steps": [],
  "hypothesis": "",
  "theoretical_justification": "",
  "survey_defect_evidence": "",
  "local_prior_overlap": "NO_OVERLAP|RELATED_BUT_DIFFERENT|OVERLAPS_AVOID|REVIVE_WITH_CHANGE",
  "closest_public_prior": [],
  "metric_target": "",
  "minimum_experiment": "",
  "reviewer_objection": "",
  "kill_condition": "",
  "autosota_recovery": "not_needed|planned|executed|skipped_with_reason"
}
```

### Role Agent Result

```json
{
  "agent": "A_cross_domain_explorer|B_defect_sniper|C_devil_reviewer",
  "inputs_used": [],
  "ideas": [],
  "review_verdicts": [],
  "limitations": []
}
```

## Implementation Plan

1. Keep `SKILL.md` as the portable orchestration contract.
2. Keep all detailed protocols in `references/`.
3. Add scripts only when a repeated deterministic operation is needed, such as metric table extraction or Markdown inventory.
4. Scripts must call adapter-like functions or accept plain files/JSON inputs rather than using Codex-only or Claude-only APIs internally.
5. Codex wrapper may use `agents/openai.yaml`, Codex subagents, and workspace tools.
6. Claude Code wrapper should use CLI-native file/shell capabilities and either Claude task agents or sequential role simulation.
7. Final reports must include adapter limitations, such as missing web search or unavailable subagents.

## Fail-Closed Rules

- If retrieval is unavailable, ask the user for papers/PDFs/links and mark metrics `[UNVERIFIED]` until evidence is provided.
- If table/page location is unavailable, the metric cannot be a direct SOTA target.
- If subagents are unavailable, simulate Agent A/B/C sequentially and record the fallback.
- If local filesystem access is restricted, inventory only accessible user-approved paths and record skipped files.
- If shell execution is unavailable, produce an implementation plan but do not claim experiments were run.
