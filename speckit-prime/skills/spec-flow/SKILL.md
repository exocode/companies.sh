---
name: spec-flow
description: >
  Autonomous orchestration of the full Spec-Kit pipeline in canonical order
  (constitution → specify → clarify → checklist → plan → tasks → analyze →
  implement), augmented with a vertical-slice refinement pass and a bounded
  QA/review→refine loop. Resumes from existing artifacts instead of restarting.
  Runs without human intervention; escalates to a human only on material
  ambiguity, and only through the orchestrator. Runtime-agnostic — works with
  any Spec-Kit-compatible agent runtime.
---

# spec-flow — autonomous Spec-Kit pipeline

This is the orchestrator's playbook. It runs the Spec-Kit sequence end-to-end for an external runner (Paperclip, RunFusion Fusion, or similar) and only stops to ask a human when it genuinely must.

## Non-negotiables

1. **Canonical order is sacred.** The eight Spec-Kit phases run in this order and are never reordered or skipped:
   `constitution → specify → clarify → checklist → plan → tasks → analyze → implement`.
   The agency *inserts* two augmentations (slice refinement, QA loop) but never *removes* or *reorders* a Spec-Kit phase.
2. **Autonomy is the default.** A question to the human is the exception, raised only by `clarify-gate` and only by the orchestrator (CEO).
3. **Never restart needlessly.** Always begin with `resume-detect` and resume from the earliest incomplete phase.

## The augmented pipeline

```
            resume-detect            ← decide where to start
                  │
  ┌───────────────┴───────────────────────────────────────────────┐
  │  0. constitution   (CEO · speckit-constitution)                │
  │  1. specify        (Spec Analyst · speckit-specify)            │
  │  2. clarify        (Spec Analyst · speckit-clarify)           │
  │  3. checklist      (QA Reviewer · speckit-checklist)          │
  │  4. plan           (Solution Architect · speckit-plan)        │
  │  5. tasks          (Task Slicer · speckit-tasks)              │
  │  5a. refine-slices (Task Slicer · refine-slices)      ← NEW   │
  │  6. analyze        (QA Reviewer · speckit-analyze)           │
  │  7. implement      (Impl. Engineer · speckit-implement)      │
  │  7a. qa-review     (QA Reviewer · qa-review)          ← NEW   │
  │        └─ on failure → re-slice (5a) or re-implement (7), bounded │
  └───────────────────────────────────────────────────────────────┘
```

`5a` and `7a` are the agency's additions. They slot *between* canonical phases — they do not replace any.

## Phase ownership

| # | Phase | Owner agent | Skill | Output artifact |
|---|-------|-------------|-------|-----------------|
| 0 | constitution | CEO | `speckit-constitution` | constitution (project memory) |
| 1 | specify | Spec Analyst | `speckit-specify` | `spec.md` |
| 2 | clarify | Spec Analyst | `speckit-clarify` | clarified `spec.md` |
| 3 | checklist | QA Reviewer | `speckit-checklist` | `checklists/*` |
| 4 | plan | Solution Architect | `speckit-plan` | `plan.md` (+ design artifacts) |
| 5 | tasks | Task Slicer | `speckit-tasks` | `tasks.md` |
| 5a | refine-slices | Task Slicer | `refine-slices` | refined `tasks.md` (small slices) |
| 6 | analyze | QA Reviewer | `speckit-analyze` | consistency report |
| 7 | implement | Implementation Engineer | `speckit-implement` | code |
| 7a | qa-review | QA Reviewer | `qa-review` | per-slice verdicts |

## Spec-Kit mechanics — don't bypass them

The eight phases are invoked as the Spec-Kit slash commands your runtime exposes (`/speckit.specify`, …). Those commands run Spec-Kit's own helper **scripts** (`create-new-feature.sh`, `setup-plan.sh`, `setup-tasks.sh`, `check-prerequisites.sh`, `common.sh`) and own the project structure. The agency rides on top of this — it never reimplements it:

- **Feature directories are script-owned.** Spec-Kit creates `specs/NNN-shortname/` with a sequential 3-digit number and (optionally, via a `before_specify` hook) a git branch. Agents never hand-create folders, pick the number, or branch manually — `specify` does it.
- **Locate the feature via `.specify/feature.json`.** Its `feature_directory` is the resolved path that every downstream phase (`plan`, `tasks`, `implement`, …) reads — *not* the git branch name. `resume-detect` resolves it; pass that `FEATURE_DIR` to each owner.
- **Commands return JSON; trust it.** e.g. `plan` runs `setup-plan.sh --json` and yields `FEATURE_SPEC`, `IMPL_PLAN`, `SPECS_DIR`, `BRANCH`. Parse the command's output to find artifact paths rather than guessing.
- **Templates and config are Spec-Kit's.** Specs/plans/tasks are seeded from `.specify/templates/*`; numbering from `.specify/init-options.json`; extension hooks from `.specify/extensions.yml`. Respect all three; don't override them.
- **Precondition:** if `.specify/` is missing, the project isn't initialized — `resume-detect` escalates that as material (init must run first).

The augmentations (`refine-slices`, `qa-review`) operate on the artifacts these scripts produce — they edit `tasks.md` in place and review the implemented slices; they do not replace any script or command.

## How the orchestrator runs it

For each phase, in order:

1. **Skip if already satisfied.** Use `resume-detect`'s verdict — if the phase's artifact exists, is non-empty, and is not stale relative to its upstream, skip to the next phase.
2. **Dispatch to the owner.** Hand the owner the explicit inputs (the upstream artifact paths) and the constitution. Do not have one agent do another's phase.
3. **Verify the artifact landed** before advancing. A missing or empty artifact is a failed phase, not a reason to silently continue.
4. **Collect routed uncertainties.** Specialists route material ambiguities up to you, never to the user. Hold them; apply `clarify-gate`.
5. **Advance.**

## The QA/review→refine loop (7a)

After `implement`, every slice goes through `qa-review`. On failure, the QA Reviewer classifies and the orchestrator routes back:

- implementation defect → re-implement (phase 7) for that slice
- oversized/wrong slice → re-slice (phase 5a) for that slice
- spec/plan defect → back to the owning phase (1/2/4), then forward again

**The loop is bounded.** Default budget: **3 iterations per slice**. If a slice still fails after the budget is spent, stop looping and escalate that slice to the human via the CEO with the failure history. This is what keeps the loop from spinning forever (anti-pattern: an unbounded refine loop).

## The human-escalation gate

You ask the human only when `clarify-gate` returns **material**. When you do:

- Batch all open material questions into one round — never drip-feed.
- For each, state your recommended default and what changes based on the answer.
- Everything mechanical is decided silently with a documented assumption.

If nothing is material, the run completes with **zero** human interaction. That is the success case for an autonomous runner.

## Done

The run is done when phase 7a reports every slice passing review against its checklist, with no open material questions. Report the artifact set and the implemented, reviewed code back to the runner.

## Notes for adaptation

- **Presets.** This pipeline keys off Spec-Kit's standard artifacts, so a future Spec-Kit *preset* (a bundled constitution + plan defaults) can be layered by seeding the constitution and plan phases without changing the orchestration. See `presets/README.md`.
- **Runtime-agnostic.** Phases are invoked as the Spec-Kit slash commands your runtime exposes (`/speckit.specify`, …). Nothing here assumes a specific agent runtime; swap Claude Code, KiloCode, Cursor, Copilot, or Gemini CLI freely.
