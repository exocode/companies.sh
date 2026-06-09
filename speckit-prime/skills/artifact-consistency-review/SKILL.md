---
name: artifact-consistency-review
description: >
  Phase 4b — deep cross-artifact consistency review after all planning
  artifacts exist (plan.md, research.md, data-model.md, contracts/,
  quickstart.md, tasks.md) and before any implementation starts.
  Catches contradictions that /speckit.analyze misses because it only
  covers spec+plan+tasks. Hard gate: BLOCKED stops implementation.
  Owned by the QA Reviewer via CTO.
---

# Artifact Consistency Review — Phase 4b

This review runs **after** `/speckit.tasks` and `refine-slices`, and
**before** `/speckit.implement`. It is the last gate before code is written.

`/speckit.analyze` checks spec ↔ plan ↔ tasks at a structural level.
This review goes deeper: it cross-checks every planning artifact against
every other, looking for the specific contradictions that survive analyze
and then explode during implementation or QA.

## What to load

Read all of these before starting. Do not skip any that exist:

- `.specify/memory/constitution.md`
- `specs/<feature-id>/spec.md`
- `specs/<feature-id>/plan.md`
- `specs/<feature-id>/research.md` (if present)
- `specs/<feature-id>/data-model.md` (if present)
- `specs/<feature-id>/contracts/` — all files (if present)
- `specs/<feature-id>/quickstart.md` (if present)
- `specs/<feature-id>/tasks.md`
- `specs/<feature-id>/checklists/` — all files

## The 10 consistency checks

Work through each check. For every contradiction found, record it with
the two conflicting artifact locations, the exact contradiction, and a
concrete fix. Do not summarise — quote the conflicting text.

### Check 1 — Commands vs API contracts

For every shell command or API call in `quickstart.md` and `tasks.md`:
- Does the endpoint, method, or parameter match the contract in `contracts/`?
- Does the request shape match `data-model.md`?
- Flag any command that uses an endpoint, field, or parameter not defined
  in the contracts.

### Check 2 — Auth requirements vs examples

Find every authentication requirement in `spec.md` and `contracts/`:
- Does every example command in `quickstart.md` include the required auth
  header, token, or credential?
- Does every task that calls an authenticated endpoint document how to
  authenticate in its done-condition or verification step?
- Flag any example that silently skips auth that the spec requires.

### Check 3 — Local-only / no-egress claims vs setup steps

If `spec.md`, `constitution.md`, or `plan.md` declares local-only operation,
no external API calls, or no-egress:
- Scan every setup step in `quickstart.md` and every task in `tasks.md`
  for `curl`, `wget`, `docker pull`, `pip install` (from PyPI), `npm install`
  (from registry), model download commands, or any network fetch.
- Flag any step that contradicts the local-only/no-egress claim.
- Flag any step that pulls from an external registry without a local mirror
  being documented.

### Check 4 — Tasks vs plan structure

For every component, service, or module defined in `plan.md`:
- Is there at least one task in `tasks.md` that implements it?
- Is there at least one task that verifies it?
- Flag any plan component with no corresponding task.
- Flag any task that implements something not in the plan (scope creep).

### Check 5 — Tasks vs success criteria

For every success criterion in `spec.md`:
- Is there at least one task whose done-condition verifiably satisfies it?
- Is there at least one task that tests it (not just implements it)?
- Flag any success criterion with no traceable task.

### Check 6 — Data model vs downstream contracts

For every entity in `data-model.md`:
- Does the entity's field set match what `contracts/` exposes in API
  responses, event schemas, or data exports?
- Are field names, types, and nullability consistent between the two?
- Flag any field present in the data model but absent from contracts,
  and vice versa — unless the discrepancy is explicitly documented.

### Check 7 — Quickstart versions vs success criteria

If `quickstart.md` defines multiple scenarios (V1, V2, …) or progressive
examples:
- Does each scenario correspond to a specific success criterion (SC-xxx)?
- Does the final scenario demonstrate all success criteria in scope?
- Flag any success criterion not covered by any quickstart scenario.
- Flag any quickstart scenario that demonstrates behaviour not in the spec.

### Check 8 — Task done-conditions are future, not past tense

Scan every task's done-condition in `tasks.md`:
- Flag any done-condition written in past tense that implies the work
  is already complete ("X has been implemented", "Y is configured").
- Done-conditions must describe a verifiable future state: "X returns
  200 for valid input", "Y config file exists with field Z set".
- Flag any done-condition so vague it cannot be verified independently.

### Check 9 — Validation commands are executable

For every verification command in `tasks.md` and `quickstart.md`:
- Can the command be run as written in the project's expected environment?
- Does it reference files, variables, or services that exist at the point
  it runs in the task sequence?
- Flag any command referencing a file produced by a later task.
- Flag any command using a placeholder (e.g. `<YOUR_TOKEN>`) without
  documenting where to obtain that value.
- Mark illustrative examples explicitly — they must not appear as
  runnable verification steps.

### Check 10 — Constitution compliance in planning artifacts

Re-check every planning artifact (not just spec.md) against the constitution:
- Does `plan.md` introduce a technology, pattern, or dependency the
  constitution prohibits?
- Does `research.md` recommend an approach the constitution rules out?
- Does `quickstart.md` describe a deployment or operational pattern that
  contradicts the constitution?
- Flag any constitution violation in any artifact, not just the spec.

---

## Verdict and report format

```
ARTIFACT CONSISTENCY REVIEW
Feature:  <feature-id> — <title>
Reviewer: QA Reviewer (independent)
Verdict:  <BLOCKED | APPROVED WITH FIXES | APPROVED>

Findings: (<N> total — <B> Blockers, <S> Should Fix, <NTH> Nice to Have)

[BLOCKER]      Check <N> — <artifact-a>:<location> contradicts
               <artifact-b>:<location>
               <artifact-a> says: "<exact quote>"
               <artifact-b> says: "<exact quote>"
               Fix: <specific change to one or both artifacts>

[SHOULD FIX]   Check <N> — <description>
               Fix: <specific change>

[NICE TO HAVE] Check <N> — <observation>
               Suggestion: <improvement>

Gate decision:
  BLOCKED            → implementation MUST NOT start.
                       Route findings to the owner of the earliest
                       affected artifact. CEO notifies human of block.
  APPROVED WITH FIXES → implementation may start. Should Fix items
                         must be resolved before the affected slice
                         is marked done.
  APPROVED           → implementation proceeds immediately.
```

## Routing of findings

- Contradiction in `spec.md` → Spec Analyst (via CTO → CEO)
- Contradiction in `plan.md` or `research.md` → Solution Architect (via CTO)
- Contradiction in `tasks.md` → Task Slicer (via CTO)
- Contradiction in `quickstart.md` or `contracts/` → Solution Architect (via CTO)
- Constitution violation → CEO (who decides whether to update constitution
  or the artifact)

After the earliest artifact is fixed, the downstream artifacts must be
re-checked for cascading effects before implementation resumes.

## Loop bound

Maximum 2 rounds of fix → re-review before escalating to the CTO with a
summary of persistent contradictions. Do not spin past round 2.
