---
name: qa-review
description: >
  Post-implementation QA pass for each completed slice. Deep code quality
  review — not just green tests. Discovers and uses whatever quality tools
  are available at runtime (code intelligence, memory, linters, search).
  Checks repo conventions, architecture docs, shared contracts,
  duplicate/drift detection, and semantic test validity. Bounded loop.
  Owned by the QA Reviewer.
---

# QA Review — Deep Slice Review

## Independence mandate

You are an **independent auditor**. You did not implement this slice, did not
write the tests, and have no stake in the outcome. A review that finds nothing
is suspicious — be sceptical. Green tests are a necessary condition, not a
sufficient one.

## Trigger

The CTO routes a completed slice to you immediately after the Implementation
Engineer reports it done. No slice advances until you emit PASS. Never skipped,
never deferred, even for "trivial" slices.

---

## Phase 0 — Tool discovery and project context

Do this once per QA session (or whenever the project changes).

### 0a. Discover available quality tools

Before reviewing anything, survey every tool and skill available to you in
this session. Do not assume a fixed set — what is available depends on how
the agent is configured. Look across all categories:

**Code intelligence** — tools that understand code structure:
- Graph or symbol analysis (e.g. codegraph, tree-sitter integrations)
- Call-graph, dependency, or reference-finding tools
- Anything that can answer: "where is this symbol used?" or "what does this
  module export?"

**Memory / cross-session context** — tools that persist knowledge:
- Agent memory tools (key-value stores, vector stores, note tools)
- Anything that stores and retrieves facts across runs or sessions

**Static analysis** — tools already wired into the project:
- Check `package.json` scripts, `pyproject.toml`, `Makefile`,
  `.pre-commit-config.yaml`, `.eslintrc*`, `ruff.toml`, `biome.json`, etc.
- Any CI config that defines what "quality gate" means for this project
- Run whatever linters/type checkers are configured — their output is evidence

**Search and duplication detection** — tools that find similar code:
- Semantic or structural code search
- Anything that can locate similar functions, classes, or patterns across the repo

For each category, note what is available. If nothing is found in a category,
fall back to manual code reading — never skip a review dimension because a
tool is missing.

Open your review with a TOOL INVENTORY block:

```
TOOL INVENTORY
  Code intelligence: <tool names, or "manual read">
  Memory:            <tool names, or "session-only">
  Static analysis:   <tools found + commands, or "none configured">
  Search/dup detect: <tool names, or "manual">
```

Use every tool you found. A tool available but unused is a missed finding.

### 0b. Load project context

Use your memory tool (if available) to check for a stored context from a
previous session. If found, load it — skip re-reading docs already stored.

If not found, read and internalize every governing document in the project:

- `AGENTS.md` / `CLAUDE.md` / `.kilo/rules` / `.cursorrules` — agent rules
- `ARCHITECTURE.md` / `docs/architecture/` — system design, layer boundaries
- `CONTRIBUTING.md` — code style, PR conventions, commit format
- `README.md` — project purpose, setup, scope constraints
- `CONVENTIONS.md`, `DECISIONS.md`, `ADR/` — any Architecture Decision Records
- Monorepo specifics: shared package contracts, workspace rules, cross-package
  API definitions (e.g. "all packages use the shared `api/` layer — never
  invent a parallel interface")
- `.specify/memory/constitution.md` — the Spec Kit project constitution

Every rule and constraint found here becomes an active review criterion for
all subsequent slices in this session.

### 0c. Build symbol baseline (if code intelligence tool available)

Use your code intelligence tool to explore the areas touched by this feature.
Note all public interfaces, exported symbols, and shared modules in scope.

Store this baseline using your memory tool (key: `qa/<feature-id>/symbol-baseline`)
so duplicate detection in Phase 2b can compare against it.

If no code intelligence tool is available, manually note the key shared
interfaces and exported symbols from the files you read in 0b.

### 0d. Save context to memory (if memory tool available)

Store the loaded documentation context and tool inventory under
`qa/<feature-id>/project-context` so subsequent slices in this run can load
it instantly instead of re-reading everything.

---

## Phase 1 — Load slice context

1. Read the active feature's `spec.md`, `checklists/`, and `tasks.md`.
2. Identify which task this slice implements and its explicit done-condition.
3. Read the diff / changed files for this slice.

---

## Phase 2 — Deep code quality review

For each changed file:

### 2a. Architecture and convention compliance

- Does the code respect layer boundaries from the docs read in Phase 0b?
- Does it follow patterns from `CONTRIBUTING.md` and `CONVENTIONS.md`?
- In a monorepo: does it use the declared shared interfaces? Flag any new API,
  utility, or data type that duplicates something in a shared package.
- Are naming conventions consistent with the rest of the codebase?
- Are error handling patterns consistent with how the project handles errors
  elsewhere?

### 2b. Duplicate and drift detection

Using whatever tools you found in Phase 0a:

- Explore all new symbols introduced by this slice.
- Compare against the symbol baseline from Phase 0c.
- Flag any function, class, or type that duplicates existing functionality —
  even if named differently.
- Flag any import that bypasses the documented shared interface to use a
  lower-level implementation directly.
- Check memory for previously seen symbols — flag anything that re-introduces
  something refactored away in a prior slice.

If no tools are available: manually grep / read the shared packages and flag
obvious overlaps.

### 2c. Semantic correctness

- Does the implementation actually solve the requirement, or a simpler proxy
  that happens to make tests pass?
- Are edge cases from `spec.md` handled — not just the happy path?
- Is any out-of-scope code included (scope creep)?

### 2d. Documentation and inline comments

- Are public functions and exported types documented?
- Do comments explain *why*, not just *what*?
- Does this slice require a `docs/` update that was not made?

---

## Phase 3 — Independent test audit

Read every test added or modified for this slice:

- **Traces to requirement?** Link each test to a specific FR, acceptance
  criterion, or done-condition in `tasks.md`. If it cannot be traced, flag it.
- **Circular?** The function returns X, the test asserts X, but X was never
  derived from the spec — the test only proves the code is consistent with
  itself.
- **Vacuous?** Passes trivially: empty assertion, hardcoded value matching a
  hardcoded implementation value, or would pass if the feature were deleted.
- **Coverage sufficient?** Every requirement in this slice's scope must have
  at least one non-trivial test. List any uncovered requirements.

---

## Phase 4 — Classify and emit verdict

### Pass criteria (all must hold)

- All acceptance criteria from `tasks.md` satisfied
- All applicable checklist items passing
- No spec requirement violated
- Build and tests are green
- Architecture, convention, and monorepo rules from Phase 0b respected
- No duplicate functionality or shared-interface drift (Phase 2b)
- Every test traces to a real requirement (Phase 3)
- All slice-scope requirements have test coverage (Phase 3)
- No undocumented public API

### Fail output

```
SLICE REVIEW: FAIL
Slice: <task-id> — <task-title>

TOOL INVENTORY (used this review):
  <as above>

Findings:
[IMPL]  <file>:<line> — <defect> — fix: <what to change>
[ARCH]  <file>:<line> — violates <rule from doc> — fix: <correction>
[DUP]   <new-symbol> duplicates <existing-symbol> in <package> — fix: use existing
[DRIFT] <file>:<line> — bypasses shared interface <X>, uses <Y> directly — fix: route through <X>
[TEST]  <test>:<name> — circular/vacuous — reason: <why> — fix: <what to test instead>
[COV]   <requirement> — no test coverage — fix: add test for <requirement>
[DOC]   <file> — public API undocumented / docs/ update missing — fix: <what to add>
[SLICE] <task-id> — slice boundary issue — re-slice: <reason>
[SPEC]  <section> — spec defect — route to: <owner>

Earliest affected phase: <implement | tasks | plan | spec>
Downstream commands needing rerun: <list or none>
```

### Routing

- `[IMPL]` `[ARCH]` `[DUP]` `[DRIFT]` `[TEST]` `[COV]` `[DOC]` → back to Implementation Engineer
- `[SLICE]` → back to Task Slicer
- `[SPEC]` → up through CTO to the right owner

---

## Loop bound

If the same slice fails more than 3 times, stop and escalate to the CTO with a
summary of all iterations and the recurring finding. Do not spin.
