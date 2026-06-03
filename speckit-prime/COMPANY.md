---
name: SpecKit Prime
description: >
  Autonomous Spec-Driven Development company that runs the full Spec-Kit
  pipeline — constitution, specify, clarify, checklist, plan, tasks, analyze,
  implement — with vertical-slice refinement and a bounded QA/review→refine
  loop. Respects Spec-Kit's native scripts and artifact structure. Resumes from
  existing artifacts. Runtime-agnostic; escalates to a human only on material
  ambiguity, and only through the CEO.
slug: speckit-prime
schema: agentcompanies/v1
version: 1.0.0
license: MIT
authors:
  - name: Dotta
  - name: exocode
goals:
  - Run the full Spec-Kit lifecycle in the required order from constitution through implementation
  - Respect Spec-Kit's native feature directory, template, script, and feature-state structure
  - Resume safely from partially completed Spec-Kit artifacts such as an existing plan, tasks file, analysis output, or implementation branch
  - Stay model-, provider-, router-, adapter-, CLI-, and agent-runtime-agnostic
  - Keep user interruptions limited to genuine material ambiguities, missing product decisions, or approval gates — all routed through the CEO
  - Refine over-large plans and tasks into smaller, verifiable vertical slices
  - Add QA and review feedback loops without breaking Spec-Kit artifact dependencies
tags:
  - speckit
  - spec-driven-development
  - orchestration
  - model-agnostic
  - qa
---

SpecKit Prime is an autonomous delivery company for GitHub Spec Kit projects.
It is designed for orchestrators (Paperclip, RunFusion Fusion, or any compatible
runner) that need a durable, reliable workflow instead of a loose prompt chain.
It is deliberately agnostic about model provider, router, adapter, CLI, agent
runtime, and local tool stack.

The company preserves this Spec Kit command order exactly:

1. `resume-detect` (pre-flight scan — always first)
2. `/speckit.constitution`
3. `/speckit.specify`
4. `/speckit.clarify`
5. `/speckit.checklist`
6. `/speckit.plan`
7. `/speckit.tasks`
8. `refine-slices` (inserted pass — immediately after tasks)
9. `/speckit.analyze`
10. `/speckit.implement`
11. `qa-review → refine loop` (inserted pass — after each implemented slice)

The workflow adds control around the commands, not inside them.

## Org Chart

```
CEO — Chief Spec Officer & Pipeline Orchestrator   (constitution, orchestration, single human gate)
├── Spec Analyst                                    (specify, clarify)
└── CTO — Chief Technology Officer                  (owns the build sub-pipeline)
    ├── Solution Architect                          (plan)
    ├── Task Slicer                                 (tasks + refine-slices)
    ├── Implementation Engineer                     (implement)
    └── QA Reviewer                                 (checklist, analyze, qa-review loop)
```

## What Makes This Company Different From a Raw Spec-Kit Run

Two things hurt an unattended Spec-Kit run:

1. **`tasks` is often too coarse** — broad tasks produce large diffs that are
   hard to implement cleanly and hard to review.
2. **There is no real post-implementation review loop** — `analyze` checks
   artifact consistency, but nothing systematically reviews implemented code
   back against the spec and gates.

SpecKit Prime keeps the canonical Spec-Kit order **exactly** intact and adds the
missing connective tissue:

- **Slice refinement** (`refine-slices`) after `tasks` — cut every task into the
  smallest independently shippable vertical slice.
- **A bounded QA/review→refine loop** (`qa-review`) after `implement` — review
  each slice against the spec and checklists, route defects back to the right
  owner, converge in a fixed number of iterations or escalate.
- **Resumability** (`resume-detect`) — start from whatever artifacts already
  exist (constitution, spec, plan, tasks) instead of re-running from scratch.
  Validates the freshness and traceability of existing artifacts before reuse.
- **A disciplined human gate** (`clarify-gate`) — autonomous by default; the
  CEO is the only agent that talks to the human, and only on material ambiguity
  batched into a single, decision-ready message.

## Spec Kit Artifact Contract

Spec Kit's own project structure is a core contract. Feature work lives under
`specs/<feature-id>/`, with artifacts such as `spec.md`, `plan.md`,
`research.md`, `data-model.md`, `contracts/`, `quickstart.md`, `tasks.md`,
and `checklists/`. The workflow uses `.specify/feature.json`,
`SPECIFY_FEATURE_DIRECTORY`, and the local `.specify/scripts/{bash,powershell}/`
helpers when available instead of guessing paths. Agents never hand-create
feature folders, invent the `NNN` number, or create branches themselves —
they invoke the Spec-Kit commands and let the scripts do it.

## Review Loop Bounds

The review loop is intentionally bounded:

- If requirements are unclear, return to `/speckit.clarify`.
- If acceptance criteria are weak, return to `/speckit.checklist`.
- If architecture or scope is too broad, refine after `/speckit.plan` and rerun `/speckit.tasks`.
- If task chunks are too large, refine tasks (re-run `refine-slices`) before `/speckit.analyze`.
- If implementation fails QA, return to the smallest affected task or slice before continuing.
- If a slice cannot converge within the loop bound, the CTO escalates to the CEO, who escalates to the human.

## Presets

The `presets/` directory accepts Spec-Kit community presets that seed the
constitution and plan phases for specific project types. Adding a preset never
changes the pipeline order, the slice-refinement pass, the QA/review→refine
loop, or the human-escalation gate.

---

Generated from [github/spec-kit](https://github.com/github/spec-kit) with the
company-creator skill. Merges the best of speckit-workflow-lab (Codex) and
speckit-agency (Claude).
