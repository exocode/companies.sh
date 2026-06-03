# SpecKit Prime

An autonomous [Agent Company](https://agentcompanies.io) built around GitHub's
[Spec-Kit](https://github.com/github/spec-kit) Spec-Driven Development toolkit.
Hand it a feature idea and it runs the entire Spec-Kit pipeline by itself —
constitution, specify, clarify, checklist, plan, tasks, analyze, implement —
adding vertical-slice refinement and a bounded QA/review→refine loop on top.

Designed to be driven by an orchestrator (Paperclip, RunFusion Fusion, or any
similar runner). Talks to a human **only** when it hits an ambiguity it cannot
responsibly resolve on its own — and only through the CEO.

> **Runtime-agnostic.** No agent runtime is pinned. Spec-Kit has many
> implementations — this company runs on Claude Code, KiloCode, Cursor, Copilot,
> Gemini CLI, and anything else that exposes the `/speckit.*` commands.

> **Merged from two independently developed versions** (speckit-workflow-lab by
> Codex, speckit-agency by Claude) — taking the best of each.

## Why it exists

Spec-Kit's command sequence is excellent, but two things hurt when you run it
unattended:

1. **`tasks` is often too coarse** — broad tasks produce large diffs that are
   hard to implement cleanly and hard to review.
2. **There's no real post-implementation review loop** — `analyze` checks
   artifact consistency, but nothing systematically reviews *implemented* code
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
  Validates freshness and traceability before reuse.
- **A disciplined human gate** (`clarify-gate`) — autonomous by default; the
  human is interrupted only on *material* ambiguity, and only by the CEO.
- **Spec-Kit script respect** (`speckit-artifact-script-contract`) — agents
  never hand-create feature folders, invent NNN numbers, or create branches.
  They invoke Spec-Kit's own scripts and let them own structure.

## The pipeline

```
resume-detect → 0 constitution → 1 specify → 2 clarify → 3 checklist
              → 4 plan → 5 tasks → 5a refine-slices → 6 analyze
              → 7 implement → 7a qa-review ──(fail, bounded)──► re-slice / re-implement
```

| # | Phase | Owner | Skill |
|---|-------|-------|-------|
| — | resume detection | CEO | `resume-detect` |
| 0 | constitution | CEO | `speckit-constitution` |
| 1 | specify | Spec Analyst | `speckit-specify` |
| 2 | clarify | Spec Analyst | `speckit-clarify` |
| 3 | checklist | QA Reviewer (via CTO) | `speckit-checklist` |
| 4 | plan | Solution Architect | `speckit-plan` |
| 5 | tasks | Task Slicer | `speckit-tasks` |
| 5a | **refine-slices** | Task Slicer | `refine-slices` |
| 6 | analyze | QA Reviewer (via CTO) | `speckit-analyze` |
| 7 | implement | Implementation Engineer | `speckit-implement` |
| 7a | **qa-review → refine** | QA Reviewer | `qa-review` |

All agents use `speckit-artifact-script-contract` to resolve Spec-Kit's native
paths and scripts before falling back to manual inference.

## Org chart

```
CEO — Chief Spec Officer & Pipeline Orchestrator   (constitution, orchestration, single human gate)
├── Spec Analyst                                    (specify, clarify)
└── CTO — Chief Technology Officer                  (owns the build sub-pipeline)
    ├── Solution Architect                          (plan)
    ├── Task Slicer                                 (tasks + refine-slices)
    ├── Implementation Engineer                     (implement)
    └── QA Reviewer                                 (checklist, analyze, qa-review loop)
```

| Agent | Reports to | Role |
|-------|-----------|------|
| **CEO** | — | Sets/loads the constitution, orchestrates the whole run via `spec-flow`, and is the only agent that talks to the human. |
| **Spec Analyst** | CEO | Turns the idea into a clarified `spec.md`. Routes material ambiguities to the CEO, defaults mechanical ones. |
| **CTO** | CEO | Owns checklist → plan → slice → analyze → implement → QA loop. Enforces slice-refinement policy and loop bounds. |
| **Solution Architect** | CTO | Produces `plan.md` + slice map from the clarified spec. |
| **Task Slicer** | CTO | Generates `tasks.md` then immediately runs `refine-slices`. |
| **Implementation Engineer** | CTO | Builds one vertical slice at a time to its done-condition. |
| **QA Reviewer** | CTO | Runs checklist, analyze, and the qa-review→refine loop. |

## Spec-Kit Artifact Structure

```text
.specify/
|-- feature.json
|-- memory/constitution.md
|-- scripts/
|   |-- bash/
|   |   |-- check-prerequisites.sh
|   |   |-- common.sh
|   |   |-- create-new-feature.sh
|   |   |-- setup-plan.sh
|   |   `-- setup-tasks.sh
|   `-- powershell/
`-- templates/
    |-- checklist-template.md
    |-- constitution-template.md
    |-- plan-template.md
    |-- spec-template.md
    `-- tasks-template.md

specs/
`-- <NNN-feature-name>/
    |-- spec.md
    |-- plan.md
    |-- research.md
    |-- data-model.md
    |-- contracts/
    |-- quickstart.md
    |-- tasks.md
    `-- checklists/
```

## Skills

| Skill | Source | Purpose |
|-------|--------|---------|
| `spec-flow` | speckit-agency (Claude) | Master orchestration playbook |
| `resume-detect` | speckit-agency (Claude) | Artifact scan + entry-point resolution |
| `clarify-gate` | speckit-agency (Claude) | Mechanical vs material ambiguity classifier |
| `refine-slices` | speckit-agency (Claude) | Task → vertical slice decomposition |
| `qa-review` | speckit-agency (Claude) | Bounded post-implementation review loop |
| `speckit-constitution` | speckit-agency (Claude) | Phase 0 skill |
| `speckit-specify` | speckit-agency (Claude) | Phase 1 skill |
| `speckit-clarify` | speckit-agency (Claude) | Phase 2 skill |
| `speckit-checklist` | speckit-agency (Claude) | Phase 3 skill |
| `speckit-plan` | speckit-agency (Claude) | Phase 4 skill |
| `speckit-tasks` | speckit-agency (Claude) | Phase 5 skill |
| `speckit-analyze` | speckit-agency (Claude) | Phase 6 skill |
| `speckit-implement` | speckit-agency (Claude) | Phase 7 skill |
| `speckit-artifact-script-contract` | speckit-workflow-lab (Codex) | Native path/script resolution |

## Presets

The `presets/` directory accepts Spec-Kit community presets that seed the
constitution and plan phases for specific project types. Adding a preset never
changes the pipeline order, the slice-refinement pass, the QA loop, or the
human gate.

---

Generated from [github/spec-kit](https://github.com/github/spec-kit) and
merged from speckit-workflow-lab (Codex) + speckit-agency (Claude).
Conforms to the [Agent Companies specification](https://agentcompanies.io/specification).
