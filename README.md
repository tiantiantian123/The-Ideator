# The Ideator

The Ideator is an Agent Skill for fast, evidence-grounded AI research idea generation aimed at CCF A / top-tier main technical tracks.

It is built for the painful part of research: finding a method idea that is novel enough, measurable enough, and worth implementing before spending weeks reproducing baselines.

## What It Does

The skill helps an agent:

- ask for the target AI subfield, task, venue, benchmark, and constraints;
- ask whether the user has local prior project directories;
- recursively read Markdown files from those projects and extract ideas already tried;
- build an avoid/revisit map so new ideas do not repeat the user's old attempts;
- harvest reported metrics from recent CCF A or adjacent top-venue papers before reproducing baselines, with source links plus table/page evidence;
- use a source ladder that combines target-field recent A-venue gaps, cross-field method transfer, and recent survey taxonomies when the immediate field is too narrow;
- generate candidate method ideas through role-specific parallel agents: Cross-Domain Explorer, Defect Sniper, and Devil's Advocate/Reviewer;
- stress-test novelty, reviewer objections, local-prior overlap, and metric comparability;
- decide whether to skip baseline reproduction, reproduce one reference baseline, or reproduce a suite;
- use an AutoSOTA-style bounded optimization pass before killing a plausible idea after weak first results;
- produce an implementation-ready idea report with minimum experiments, thresholds, and risks.

## Repository Layout

```text
The-Ideator/
├── README.md
├── LICENSE
├── .gitignore
└── skills/
    └── the-ideator/
        ├── SKILL.md
        ├── LICENSE
        ├── agents/
        │   └── openai.yaml
        └── references/
            ├── autosota-lite.md
            ├── dual-platform-architecture.md
            ├── idea-scoring.md
            ├── idea-source-ladder.md
            ├── metric-harvesting.md
            ├── multi-agent-brainstorming.md
            └── prior-project-intake.md
```

## Install

### Codex

Copy `skills/the-ideator` into your Codex skills directory, usually:

```text
~/.codex/skills/the-ideator
```

The `agents/openai.yaml` file provides Codex UI metadata.

### Claude Code Or Other Agent Skills Clients

Use the same `skills/the-ideator` directory as a standard Agent Skill. The skill follows the common `SKILL.md` + optional `references/` structure.

If your client expects skills in a specific directory, copy the `the-ideator` folder there. If your client expects uploadable skills, zip the `the-ideator` folder itself.

Do not zip the repository root if the client expects `SKILL.md` at the archive root. In that case, publish or upload an archive whose top-level folder is `the-ideator/` and contains:

```text
the-ideator/
├── SKILL.md
├── agents/
└── references/
```

## Example Prompts

```text
Use The Ideator to find CCF A main-track method ideas for long-context RAG hallucination mitigation. Target ACL/EMNLP. I have 2 A100 GPUs and one month.
```

```text
Use The Ideator for graph anomaly detection in dynamic graphs. I have prior idea notes in D:\research\graph-notes and D:\projects\failed-gad.
```

```text
Use The Ideator to generate SOTA-oriented ideas for efficient diffusion sampling. First compare recent CVPR/ICCV/NeurIPS metrics; do not reproduce baselines unless needed.
```

## Design Notes

The skill intentionally puts several checks before expensive engineering:

- Local prior intake comes before ideation, so the agent avoids ideas the user already tried.
- Recent metric harvesting comes before baseline reproduction, but every metric must be retrieval-verified with a paper/PDF/proceedings source and table/page or section locator.
- If the agent environment has no web search, the skill asks the user for papers, links, PDFs, or metric tables and marks unsupported values as `[UNVERIFIED]`.
- Idea sources are deliberately diversified: target-field recent papers first, then cross-field mechanisms, then recent surveys when the direct field is sparse or homogeneous.
- Parallel brainstorming is role-specific: Agent A transfers methods with theoretical justification, Agent B mines survey-recognized defects, and Agent C aggressively reviews/refines both.
- AutoSOTA-style signal recovery is used only after a plausible idea underperforms, and only within explicit budget and evaluation guardrails.
- The architecture is decoupled: `TheIdeatorCore` owns thinking and idea generation, while Codex/Claude wrappers provide file, search, shell, UI, and subagent capabilities.

## Source Inspirations

This project is designed around the user's requested research-skill references:

- Auto-claude-code-research-in-sleep: https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep
- academic-research-skills: https://github.com/Imbad0202/academic-research-skills
- PaperSpine: https://github.com/WUBING2023/PaperSpine
- AutoSOTA: https://github.com/tsinghua-fib-lab/AutoSOTA
- Agent Skills specification: https://agentskills.io/specification

## Status

Initial skill project. Treat generated research ideas as hypotheses requiring literature verification and experiments.

## License

MIT License. See [LICENSE](LICENSE).
