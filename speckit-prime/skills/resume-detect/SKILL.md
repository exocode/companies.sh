---
name: resume-detect
description: >
  Detect the correct entry point into the Spec-Kit pipeline by scanning the
  working directory for existing artifacts (constitution, spec.md, plan.md,
  tasks.md, checklists, analyze report) and unresolved [NEEDS CLARIFICATION]
  markers. Returns the earliest incomplete phase so a run resumes instead of
  restarting from scratch. Use at the start of every spec-flow run.
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: docs/reference/workflows.md
      url: https://github.com/github/spec-kit/blob/main/docs/reference/workflows.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
    - kind: github-file
      repo: github/spec-kit
      path: README.md
      url: https://github.com/github/spec-kit/blob/main/README.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# resume-detect — find where to start

An autonomous run must never blow away good work. Before phase 0, scan the working tree and decide the earliest phase whose output is missing or stale. Everything before that point is skipped.

## Spec-Kit owns the structure — don't reinvent it

Spec-Kit manages features in **numbered directories** under `specs/` (e.g. `specs/003-user-auth/`), and its slash commands run helper **scripts** (`create-new-feature.sh`, `setup-plan.sh`, `setup-tasks.sh`, `check-prerequisites.sh`, `common.sh`) that own folder creation, the `NNN` numbering, and optional git branches. Agents in this company **never** hand-create feature folders, invent the number, or create branches themselves — they invoke the commands and let the scripts do it. resume-detect only *reads* this structure.

### How to locate the active feature directory

1. **Read `.specify/feature.json`** → its `feature_directory` value is the resolved path (e.g. `specs/003-user-auth`). This is the source of truth; downstream phases rely on it, **not** on the git branch name. Use it.
2. If `feature.json` is absent (no feature created yet) but the runner is targeting an existing feature, fall back to the highest-numbered `specs/NNN-*` directory, or resolve from the runner's task context.
3. If `specs/` has several features and the target is ambiguous, that is a **material** question → route through `clarify-gate` to the human.

### Precondition: Spec-Kit must be initialized

If `.specify/` does not exist (no `scripts/`, `templates/`, `memory/`), the project is not Spec-Kit-initialized. Do not fake it — running `specify init …` (or confirming which runtime/agent flavor to init for) is a **material** prerequisite; surface it via `clarify-gate`. Numbering config lives in `.specify/init-options.json`; extension hooks in `.specify/extensions.yml` — respect both, don't override them.

## What to scan

Match by filename within the resolved feature directory (paths vary slightly across Spec-Kit versions — `FEATURE_DIR` below = the `feature_directory` from `.specify/feature.json`):

| Phase | Artifact | Present-and-valid means… |
|-------|----------|--------------------------|
| — init | `.specify/` (scripts, templates, memory) | absent → Spec-Kit not initialized; escalate (see above) |
| 0 constitution | `.specify/memory/constitution.md` | exists and non-empty → skip phase 0, but **load** it |
| 1 specify | `FEATURE_DIR/spec.md` | exists and non-empty → skip phase 1 |
| 2 clarify | `FEATURE_DIR/spec.md` has **no** `[NEEDS CLARIFICATION]` markers | no open markers → skip phase 2 |
| 3 checklist | `FEATURE_DIR/checklists/` populated | gates exist → skip phase 3 |
| 4 plan | `FEATURE_DIR/plan.md` (+ `research.md`, `data-model.md`, `contracts/`) | plan exists and non-empty → skip phase 4 |
| 5 tasks | `FEATURE_DIR/tasks.md` | tasks exist → skip phase 5 |
| 5a refine | `tasks.md` slices already small (no coarse/`and`-joined tasks) | already refined → skip phase 5a |
| 6 analyze | a consistency report exists and is newer than spec/plan/tasks | up-to-date → skip phase 6 |
| 7 implement | tasks marked done + code present for them | all slices done → run is complete |

## Decision rules

1. **Resume from the earliest incomplete phase**, not the latest complete one. If `plan.md` exists but `spec.md` has open `[NEEDS CLARIFICATION]` markers, resume at clarify (2) — a stale downstream artifact does not let you skip a broken upstream one.
2. **Staleness beats existence.** If an artifact is older than the upstream it depends on (e.g. `tasks.md` predates the latest `plan.md` edit), treat it as missing and re-run that phase. Note the re-run reason.
3. **Always load the constitution** even when skipping phase 0 — every later phase is held to it.
4. **Unresolved markers force clarify.** Any `[NEEDS CLARIFICATION]` (or equivalent Spec-Kit marker) anywhere in the spec means phase 2 is not done, regardless of what exists downstream.
5. **Multiple features.** Resolve the target feature from `.specify/feature.json` first (rule above); only fall back to task context, and escalate if still ambiguous.

## Output

A single verdict consumed by `spec-flow`: the resolved `FEATURE_DIR`, the entry phase (e.g. "resume at 4 (plan); constitution + spec + clarify + checklist present and current"), and any re-run reasons.
