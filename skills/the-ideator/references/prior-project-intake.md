# Prior Local Project Intake

Use this file when the user provides local directories containing research notes, past attempts, drafts, experiment logs, or project documentation. The purpose is to avoid reinventing ideas the user already tried and to identify weak ideas that may only need better settings or AutoSOTA-style recovery.

## User Prompt

After the user states the research field or task, ask for local prior projects:

```text
Do you have local project directories that contain ideas you have already tried or discussed? If yes, give me the paths. I will read all Markdown files in those directories and build an avoid/revisit map before generating new ideas.
```

If the user says no or provides no paths, still create all expected artifacts:

- `idea-forge/prior_project_inventory.md`
- `idea-forge/prior_ideas.md`
- `idea-forge/avoid_list.md`

Use content like:

```markdown
# Prior Ideas From User Local Projects

No local prior project directories were provided. Candidate ideas should still be checked against user-stated dead ends and public prior work.
```

## Directory Scan

For each provided directory:

1. Verify the path exists and is a directory.
2. Recursively enumerate Markdown files:
   - `*.md`
   - `*.markdown`
   - `*.mdown`
   - `*.mdx`
3. Read every discovered Markdown file. Do not stop at README files.
4. If the number of files or total size is too large to inspect within the current budget, first write a full inventory and ask the user whether to continue with all files, prioritize likely research notes, or provide a narrower directory.
5. If files are inaccessible, record the error and continue with accessible files.

Respect the client permission model:

- read only user-confirmed paths;
- do not follow symlinks or shortcuts outside the provided directory unless the user explicitly approves;
- ask before processing directories that appear very large, sensitive, or unrelated;
- do not copy private note contents into external services beyond what the active agent client already sends for the task;
- record inaccessible or skipped files instead of bypassing sandbox or permission boundaries.

Use case-insensitive matching when the filesystem or tool requires explicit handling.

Prefer `rg --files` when available.

PowerShell:

```powershell
rg --files "C:\path\to\project" -g "*.md" -g "*.markdown" -g "*.mdown" -g "*.mdx"
```

POSIX shell:

```bash
rg --files "/path/to/project" -g "*.md" -g "*.markdown" -g "*.mdown" -g "*.mdx"
```

If `rg` is not available, use the platform's recursive file listing.

## Inventory File

Create `idea-forge/prior_project_inventory.md`:

```markdown
# Prior Project Markdown Inventory

| Project directory | Markdown file | Size | Read status | Notes |
|---|---|---:|---|---|
```

Use absolute paths where possible. Mark read status as:

- `READ`
- `PARTIAL`
- `FAILED`
- `SKIPPED_WITH_USER_APPROVAL`

Do not silently skip files.

## Extraction Schema

For each Markdown file, extract:

- project or note title;
- task/subfield;
- proposed method or hypothesis;
- claimed novelty;
- datasets, benchmarks, metrics, or target venues;
- experiment status: untested, failed, partial signal, positive, unknown;
- reasons for failure or uncertainty;
- implementation details that may explain weak performance;
- relation to the current user request;
- exact source file path.

## Prior Ideas File

Create `idea-forge/prior_ideas.md`:

```markdown
# Prior Ideas From User Local Projects

## Idea P[ID]: [short title]

**Source files**:
**Task/subfield**:
**Core method**:
**Hypothesis**:
**Evidence/status**:
**Known failure mode**:
**Could be settings-limited?** YES/PARTIAL/NO/UNKNOWN
**Avoid or revisit?** AVOID / REVISIT_WITH_CHANGE / RECOVER_WITH_AUTOSOTA / UNKNOWN
**Reason**:
```

Merge duplicate or near-duplicate notes into one prior idea, but keep all source file paths.

## Avoid/Revisit Map

Create `idea-forge/avoid_list.md`:

```markdown
# Avoid And Revisit Map

## Avoid

| Prior idea | Why avoid | Closest current-topic overlap |
|---|---|---|

## Revisit Only With Material Change

| Prior idea | Required change | Why it might still matter |
|---|---|---|

## AutoSOTA-Style Recovery Candidates

| Prior idea | Weak-result reason | Suggested recovery search space |
|---|---|---|

## Open Threads

| Thread | Missing evidence | How to use during ideation |
|---|---|---|
```

## Candidate Overlap Check

Every new candidate idea must include:

```markdown
**Local-prior overlap**: NO_OVERLAP / RELATED_BUT_DIFFERENT / OVERLAPS_AVOID / REVIVE_WITH_CHANGE
**Overlap evidence**: [prior idea id + source path, or "none found"]
**Action**: keep / reframe / discard / run signal recovery
```

Rules:

- `OVERLAPS_AVOID`: discard unless the user explicitly wants a new version of that idea.
- `REVIVE_WITH_CHANGE`: keep only if the new version changes the mechanism, setting, claim, or optimization path.
- `RELATED_BUT_DIFFERENT`: explain the exact difference; vague distinction is not enough.
- `NO_OVERLAP`: still check public prior work.

## Quality Checks

Before generating final ideas:

- confirm that all provided directories appear in the inventory;
- confirm that every discovered Markdown file is marked with a read status;
- confirm that `prior_project_inventory.md`, `prior_ideas.md`, and `avoid_list.md` exist even when no local directories are provided;
- confirm that `avoid_list.md` is referenced in the target contract;
- verify that top-ranked candidates do not repeat local prior ideas without a stated material change.
