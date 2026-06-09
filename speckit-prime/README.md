# SpecKit Prime

An autonomous [Agent Company](https://agentcompanies.io) built around GitHub's
[Spec-Kit](https://github.com/github/spec-kit) Spec-Driven Development toolkit.
Hand it a feature idea and it runs the full pipeline by itself — with formal
review gates, vertical-slice refinement, and a bounded QA loop.

> **Runtime-agnostic.** Runs on Claude Code, KiloCode, Cursor, Copilot,
> Gemini CLI, or any agent runtime that exposes `/speckit.*` commands.

---

## Pipeline

Every run starts with `resume-detect`. The CEO then drives each phase in order,
dispatching to the owning specialist and verifying the artifact before advancing.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SPECKIT PRIME PIPELINE                              │
└─────────────────────────────────────────────────────────────────────────────┘

  START
    │
    ▼
┌──────────────┐
│ resume-detect│  ← always first: scans artifacts, active branch,
│   (CEO)      │    mid-run state, agentmemory ledger
└──────┬───────┘
       │ resolves FEATURE_DIR + ENTRY_PHASE
       ▼
┌──────────────┐
│ Phase 0      │  constitution  (CEO)
│ constitution │  └─ spec-critic on constitution.md
└──────┬───────┘  └─ [FAIL blocks pipeline until PASS]
       │ artifact: .specify/memory/constitution.md
       │ git checkpoint ①
       ▼
┌──────────────┐
│ Phase 1      │  specify  (Spec Analyst)
│ specify      │  └─ /speckit.specify  →  spec.md
├──────────────┤
│ Phase 1a     │  clarify  (Spec Analyst, automatic)
│ clarify      │  └─ /speckit.clarify  →  spec.md updated
├──────────────┤
│ Phase 1b     │  spec-critic  (Spec Analyst, autonomous)
│ spec-critic  │  └─ adversarial self-review
│              │  └─ FAIL: fix and re-run (max 3 iterations)
└──────┬───────┘
       │ artifact: spec.md, no [NEEDS CLARIFICATION] markers
       ▼
┌──────────────┐
│ Phase 1c     │  ★ HUMAN GATE ★
│ human gate   │  CEO presents SPEC ANALYST HANDBACK:
│              │    • what was specified (1 sentence)
│              │    • auto-resolved clarifications (for review)
│              │    • material questions + recommended defaults
│              │    • critic advisory notes
│              │  → waits for "confirmed" or corrections
└──────┬───────┘
       │ human confirmed
       ▼
┌──────────────┐
│ Phase 1d     │  spec-review  (Spec Reviewer — independent)
│ spec-review  │  └─ loads: spec.md + constitution + originating brief
│  HARD GATE   │  └─ reviews 7 dimensions:
│              │       1. Brief fidelity
│              │       2. Constitution alignment
│              │       3. Weasel words ("where feasible", "as appropriate"…)
│              │       4. Testability
│              │       5. Security & ops gaps
│              │       6. Later-feature boundary clarity
│              │       7. Drift against prior specs
│              │  └─ verdict: BLOCKED / APPROVED WITH FIXES / APPROVED
└──────┬───────┘
       │         ┌─ BLOCKED ──→ Spec Analyst fixes blockers only
       │         │              (no new human gate, max 2 rounds)
       │         │              Round 2 still BLOCKED → CEO escalates to human
       │ APPROVED or APPROVED WITH FIXES
       │ git checkpoint ②
       ▼
┌──────────────┐
│ Phase 2      │  checklist  (QA Reviewer via CTO)
│ checklist    │  └─ /speckit.checklist  →  checklists/ files
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Phase 3      │  plan  (Solution Architect via CTO)
│ plan         │  └─ /speckit.plan  →  plan.md
└──────┬───────┘
       │ artifact: plan.md
       ▼
┌──────────────┐
│ Phase 4      │  tasks  (Task Slicer via CTO)
│ tasks        │  └─ /speckit.tasks  →  tasks.md
├──────────────┤
│ Phase 4a     │  refine-slices  (Task Slicer, immediate)
│ refine-slices│  └─ every task → smallest vertical slice
└──────┬───────┘
       │ artifact: tasks.md (all slices small + independent)
       ▼
┌──────────────┐
│ Phase 5      │  analyze  (QA Reviewer via CTO)
│ analyze      │  └─ /speckit.analyze  →  consistency report
└──────┬───────┘
       │ artifact: no blocking findings
       │ git checkpoint ③
       ▼
┌──────────────┐
│ Phase 6      │  implement  (Implementation Engineer via CTO)
│ implement    │  └─ /speckit.implement
│  [per slice] │  └─ one vertical slice at a time
│              │
│   for each   │─────────────────────────────────┐
│    slice:    │                                  ▼
│              │                    ┌─────────────────────────┐
│              │                    │ Phase 6a  qa-review      │
│              │                    │ (QA Reviewer, mandatory) │
│              │                    │                          │
│              │                    │ Tool discovery first:    │
│              │                    │  • code intelligence     │
│              │                    │  • memory tools          │
│              │                    │  • static analysis       │
│              │                    │  • dup/drift detection   │
│              │                    │                          │
│              │                    │ Reviews:                 │
│              │                    │  • architecture docs     │
│              │                    │  • convention compliance │
│              │                    │  • duplicate detection   │
│              │                    │  • test independence     │
│              │                    │  • requirement coverage  │
│              │                    │                          │
│              │                    │ verdict: PASS or FAIL    │
│              │                    └────────┬────────────────-┘
│              │◄── fix & resubmit ──── FAIL │  (max 3 rounds)
│              │                        PASS │
│              │◄───────────────────────────-┘
│              │  git checkpoint ④ (per slice)
└──────┬───────┘
       │ all slices PASS
       ▼
  DONE — artifacts + implementation reviewed and committed
```

---

## Git Checkpoints

The pipeline defines four natural commit points. The CEO signals each one
explicitly — nothing is committed without an explicit checkpoint signal:

| # | When | What to commit |
|---|------|----------------|
| ① | After constitution passes spec-critic | `.specify/memory/constitution.md` |
| ② | After spec-review APPROVED | `specs/<id>/spec.md`, `checklists/` |
| ③ | After analyze passes (before any code) | `specs/<id>/plan.md`, `tasks.md` |
| ④ | After each slice passes qa-review | The slice's code changes only |

**Why this cadence:**
- Constitution is a project-level governance decision — commit it alone.
- Spec is reviewed and human-confirmed — commit it before any planning begins.
- Plan + tasks are the implementation contract — commit them before code.
- Each passing slice is independently shippable — commit it before the next.

You never end up with a giant undifferentiated diff. Each commit is traceable
to a single pipeline phase and a single human or agent decision.

---

## Org Chart

```
CEO — Chief Spec Officer & Pipeline Orchestrator
├── Spec Analyst      (specify → clarify → spec-critic → handback)
├── Spec Reviewer     (independent spec gate, blocks planning)
└── CTO — Chief Technology Officer
    ├── Solution Architect      (plan)
    ├── Task Slicer             (tasks + refine-slices)
    ├── Implementation Engineer (implement, slice by slice)
    └── QA Reviewer             (checklist, analyze, qa-review loop)
```

| Agent | Reports to | Role |
|-------|-----------|------|
| **CEO** | — | Orchestrates the full run. Only agent that talks to the human. Runs resume-detect and spec-critic on constitution. |
| **Spec Analyst** | CEO | specify → clarify (automatic) → spec-critic (autonomous) → HANDBACK to CEO. |
| **Spec Reviewer** | CEO | Independent formal review of spec.md. 7 dimensions. Hard gate before planning. Max 2 rounds. |
| **CTO** | CEO | Owns checklist → plan → tasks → analyze → implement → QA loop. Enforces slice and loop bounds. |
| **Solution Architect** | CTO | Produces `plan.md` and slice map. |
| **Task Slicer** | CTO | `tasks.md` then immediately `refine-slices`. |
| **Implementation Engineer** | CTO | One vertical slice at a time to its done-condition. |
| **QA Reviewer** | CTO | checklist, analyze, qa-review after every slice. Discovers available tools first. |

---

## Resume Safety

`resume-detect` runs **before every pipeline start**. It detects:

- An existing branch matching the feature name → resume, no new branch
- `tasks.md` with mixed `[x]`/`[ ]` slices → resume at first open slice
- Uncommitted changes in the feature directory → report before continuing
- A stored run ledger in agentmemory → takes precedence over filesystem scan

The CEO acknowledges any `INTERRUPTED_STATE` before the pipeline proceeds.
Nothing is restarted from scratch when valid artifacts already exist.

---

## Spec-Kit Artifact Structure

```
.specify/
├── feature.json
├── memory/constitution.md
├── scripts/bash/   (check-prerequisites.sh, setup-plan.sh, …)
└── templates/      (spec, plan, tasks, checklist templates)

specs/<NNN-feature-name>/
├── spec.md
├── plan.md
├── tasks.md
├── checklists/
├── research.md        (if needed)
├── data-model.md      (if needed)
└── contracts/         (if needed)
```

Agents never hand-create feature folders or invent `NNN` numbers.
They invoke Spec-Kit's own scripts and let them own the structure.

---

## Skills Reference

| Skill | Type | Purpose |
|-------|------|---------|
| `spec-flow` | company | Master pipeline map for the CEO |
| `resume-detect` | company | Artifact scan + mid-run state detection |
| `clarify-gate` | company | Mechanical vs material ambiguity classifier |
| `spec-critic` | company | Autonomous adversarial self-review of spec/constitution |
| `spec-review` | company | Formal 7-dimension independent spec review protocol |
| `refine-slices` | company | Task → vertical slice decomposition |
| `qa-review` | company | Deep bounded post-implementation review (tool-discovery driven) |
| `speckit-paperclip-mode` | company | Shell-less fallback for Paperclip Desktop |
| `speckit-artifact-script-contract` | company | Native Spec-Kit path/script resolution |
| `speckit-constitution` | native | Delegates to `/speckit.constitution` |
| `speckit-specify` | native | Delegates to `/speckit.specify` |
| `speckit-clarify` | native | Delegates to `/speckit.clarify` |
| `speckit-checklist` | native | Delegates to `/speckit.checklist` |
| `speckit-plan` | native | Delegates to `/speckit.plan` |
| `speckit-tasks` | native | Delegates to `/speckit.tasks` |
| `speckit-analyze` | native | Delegates to `/speckit.analyze` |
| `speckit-implement` | native | Delegates to `/speckit.implement` |

**Native skills** are thin wrappers — they delegate to the `/speckit.*` commands
that Spec-Kit installs in your project. They never copy or re-implement Spec-Kit
logic, so they stay up to date automatically when Spec-Kit releases new versions.

---

Generated from [github/spec-kit](https://github.com/github/spec-kit).
Conforms to the [Agent Companies specification](https://agentcompanies.io/specification).
