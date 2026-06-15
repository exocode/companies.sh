---
name: resume-detect
description: >
  Pre-flight scan — always the first step. Detects existing Spec-Kit artifacts,
  active branches, in-progress slices, and aborted runs. Returns the earliest
  incomplete phase so the pipeline resumes correctly instead of restarting.
  Prevents double-branch creation after an interrupted run.
---

# Resume Detect — Pre-Flight Scan

Always run this skill first, before any other pipeline phase. It prevents
restarting work that already exists and catches interrupted runs before a
new branch or feature directory is accidentally created.

## What you do

### 0. Validate feature.json against the current issue (MANDATORY — run before step 1)

Before reading `.specify/feature.json`, extract the feature identifier from
the current context — the Paperclip issue title, description, or any explicit
`FEATURE_ID` passed by the CEO.

Compare it to the `feature_directory` value in `.specify/feature.json`:

- If `.specify/feature.json` is absent or empty → proceed normally to step 1.
- If the directory name in `feature.json` **matches** the current issue/feature
  (same NNN prefix or same short name) → trust it and proceed to step 1.
- If the directory name in `feature.json` points to a **different feature**
  (e.g. `specs/001-doc-ingestion-index` when the current issue is Feature 002)
  → **treat feature.json as stale and skip it entirely**.
  Log: `feature.json points to a different feature (<value>); ignoring for
  this run — proceeding with specs/ directory scan.`
  Continue from step 1 using only the specs/ scan (step 1 options 4–6).

This guard prevents a completed feature's feature.json from silently routing a
new feature into the wrong resume phase.

### 1. Resolve FEATURE_DIR

In this order (stop at first hit):

1. Explicit `SPECIFY_FEATURE_DIRECTORY` env or argument.
2. `.specify/feature.json` → `feature_directory` field.
3. If in Paperclip Desktop (no shell): scan conversation history for
   `PAPERCLIP ARTIFACT REPORT` blocks — most recent feature directory wins.
4. Run `.specify/scripts/bash/check-prerequisites.sh --json` if available.
5. Scan `specs/` for `NNN-*` or timestamp-pattern directories — pick the
   highest-numbered one.
6. If ambiguous (multiple candidates), ask the CEO. Never guess.

### 2. Detect in-progress or aborted state

**This step is mandatory. Do not skip it.**

Check for signs of an interrupted run before proceeding:

- **Active git branch**: Does a branch matching `NNN-*` or the feature name
  already exist (locally or on remote)? If yes, this is a resume, not a fresh
  start. Do not create a new branch.
- **Partial implementation**: Does `tasks.md` have some slices marked `[x]`
  (done) and some `[ ]` (open)? The run was interrupted mid-implementation.
  Resume from the first open slice — do not restart from task generation.
- **Uncommitted changes**: Are there uncommitted changes in the feature
  directory? The Implementation Engineer may have been mid-slice when the run
  stopped. Report this to the CEO before continuing.
- **agentmemory check**: If agentmemory is available, query key
  `speckit/<feature-id>/run-state` for a stored ledger from a previous session.
  A stored ledger overrides filesystem inference — it is more precise.

If any in-progress signal is found:
- Report it explicitly in the ledger under `INTERRUPTED_STATE`.
- Set `ENTRY_PHASE` to the earliest incomplete phase, not phase 0.
- The CEO must acknowledge the interrupted state before the pipeline proceeds.

### 3. Scan for existing artifacts

Check for each artifact in `FEATURE_DIR`:

| Artifact | Phase done |
|---|---|
| `.specify/memory/constitution.md` | constitution |
| `spec.md` | specify |
| `spec.md` with no `[NEEDS CLARIFICATION]` markers | clarify |
| `checklists/` directory with files | checklist |
| `plan.md` | plan |
| `tasks.md` | tasks |
| all tasks in `tasks.md` are small vertical slices | refine-slices |
| consistency report with no blocking findings | analyze |
| some slices marked `[x]` in `tasks.md` | implement (partial) |
| all slices marked `[x]` | implement (complete) |

### 4. Stale artifact detection

Flag an artifact as potentially stale if:

- `plan.md` is older than `spec.md`
- `tasks.md` is older than `plan.md`
- Implementation is ahead of the analyze pass
- A branch exists but `tasks.md` has no checked-off slices (branch created,
  nothing implemented — possible abandoned run)

Do not discard stale artifacts automatically — report them and let the CEO
decide whether to rerun the affected phase.

### 5. Store run state in agentmemory (if available)

After completing the scan, store the ledger under
`speckit/<feature-id>/run-state` so a future session can resume without
re-scanning. Include: FEATURE_DIR, ENTRY_PHASE, last completed slice ID,
stale warnings, and interrupted state if any.

### 6. Return the resume ledger

```
FEATURE_DIR:       <resolved path>
ENTRY_PHASE:       <0-7a>
EXISTING_ARTIFACTS: <list>
STALE_WARNINGS:    <list, or none>
INTERRUPTED_STATE: <description of in-progress signals, or none>
ACTIVE_BRANCH:     <branch name if found, or none>
PARTIAL_SLICES:    <list of done/open slices if mid-implementation, or none>
NEXT_ACTION:       <what the CEO should dispatch next>
```
