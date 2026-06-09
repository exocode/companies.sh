---
name: spec-review
description: >
  Formal independent review of a spec.md against the constitution, the
  originating brief/issue context, and prior specs. Produces classified
  findings as Blocker / Should Fix / Nice to Have. Planning MUST NOT proceed
  while Blocker findings remain. The Spec Reviewer never rewrites — only
  reviews and reports. This is the release gate between specify and plan.
---

# Spec Review — Formal Release Gate

This skill defines the full review protocol for the Spec Reviewer agent.
It is a formal gate, not an advisory pass. The spec cannot advance to
`/speckit.plan` while Blocker findings are open.

## Independence rule

The Spec Reviewer did not write the spec, did not run clarify, and has no
stake in marking it green. Read everything fresh, with no prior context
from the producing agent.

## What to load before reviewing

1. `.specify/memory/constitution.md` — the governing baseline
2. The spec file: `specs/<feature-id>/spec.md`
3. The **originating brief** — whichever of these exists:
   - The original Paperclip issue / task description that triggered this run
   - The user prompt or feature request handed to the CEO
   - Any prior design handoff document in the project
   - Related prior specs in `specs/` that this feature builds on or interfaces with
4. Any `ARCHITECTURE.md`, `CONTRIBUTING.md`, or `CONVENTIONS.md` in the project root

If the originating brief cannot be found, flag it as a Blocker — a spec
without a traceable brief cannot be verified for drift.

---

## Review protocol — work through every dimension

### 1. Brief fidelity — does the spec match what was asked?

Compare the spec against the originating brief line by line:
- Is everything requested in the brief covered in the spec?
- Has any requirement from the brief been silently dropped or narrowed?
- Has anything been added that was not in the brief and not justified by the
  constitution or project context?
- Does the feature scope match what the brief intended, or has it grown/shrunk?

### 2. Constitution alignment

Read the constitution. For every principle, check whether the spec respects it:
- Technology constraints: does the spec stay within allowed tech choices?
- Scope boundaries: does the spec stay within the boundaries the constitution
  sets for this type of feature?
- Quality standards: are the spec's success criteria consistent with what the
  constitution defines as "done"?
- Team norms: does the spec introduce process or architecture decisions the
  constitution reserves for later phases?

### 3. Weasel words — untestable hedges

Scan the full spec for language that makes requirements unverifiable. Flag
every occurrence:

- "where feasible" / "where possible" / "if possible"
- "as appropriate" / "as needed" / "as applicable"
- "technically available" / "when available"
- "should consider" / "may optionally"
- "reasonable" / "adequate" / "sufficient" (without a metric)
- "robust" / "reliable" / "scalable" / "performant" (without a threshold)
- "intuitive" / "user-friendly" / "easy to use" (without an observable test)
- "etc." / "and more" / "among others" (open-ended lists in requirements)

Every weasel word is a Should Fix at minimum, a Blocker if it appears in a
success criterion or acceptance scenario.

### 4. Testability audit

For each functional requirement and each acceptance scenario:
- Can an independent tester verify this without asking the author?
- Does the scenario have concrete Given/When/Then structure with specific values?
- Is the success criterion measurable — does it name a metric, threshold, count,
  time, or rate? "Fast" is not measurable. "Returns results in under 2 seconds
  for 95% of queries" is measurable.
- Would a test written against this requirement still pass if the feature were
  deleted or broken? If yes, the criterion is vacuous.

### 5. Security and operations gaps

Check whether the spec has addressed:
- **Authentication / authorization**: who can do what, and what happens to
  unauthorized access attempts?
- **Data protection**: what data is sensitive, how is it stored, who can see it?
- **Error states**: what happens when the system fails, times out, or receives
  bad input?
- **Operational visibility**: is there a requirement for logging, monitoring,
  or alerting relevant to this feature?
- **Rate limiting / abuse**: if the feature is user-facing or exposed via API,
  is there a requirement for throttling or abuse prevention?

Missing security/ops requirements for a feature that touches data, auth, or
external systems are Blockers. Missing requirements for purely internal features
are Should Fix.

### 6. Later-feature boundary clarity

Check whether features that are explicitly out of scope are cleanly delineated:
- Does the spec say "X is out of scope for this feature" and mean it — or does
  the implementation path clearly require X to work?
- Are there implicit dependencies on unbuilt features that are not acknowledged?
- Does the spec define the data contracts or API boundaries that later features
  will need to consume? If the spec description says "this feature prepares data
  for later use", are those interfaces specified?
- Are any "future feature" placeholders in the spec vague enough that two
  developers would design incompatible interfaces?

### 7. Drift against prior specs

If related prior specs exist in `specs/`:
- Does this spec use the same terminology for shared concepts?
- Does it reuse the same data entities, or invent parallel ones?
- Does it contradict decisions made in prior specs?
- Does it assume a capability that a prior spec explicitly deferred?

---

## Finding format

Classify every finding into exactly one severity:

- **Blocker** — spec cannot advance to planning until resolved. The error is
  fundamental: the spec cannot be implemented correctly, safely, or in
  accordance with the brief or constitution.
- **Should Fix** — significant gap that will cause downstream problems but does
  not make the spec unimplementable. Should be fixed before planning ideally,
  and must be resolved before implementation.
- **Nice to Have** — minor clarity improvement. Can be addressed in the next
  iteration without blocking progress.

---

## SPEC REVIEW REPORT format

```
SPEC REVIEW REPORT
Feature:   <feature-id> — <feature title>
Spec:      <path to spec.md>
Brief:     <source of originating brief, or "NOT FOUND — Blocker">
Reviewer:  Spec Reviewer (independent)
Verdict:   <BLOCKED | APPROVED WITH FIXES | APPROVED>

Findings: (<N> total — <B> Blockers, <S> Should Fix, <N> Nice to Have)

[BLOCKER]       #<n> <dimension> — <what is wrong> — required change: <specific fix>
[SHOULD FIX]    #<n> <dimension> — <what is weak> — suggested change: <specific improvement>
[NICE TO HAVE]  #<n> <dimension> — <observation> — suggestion: <optional improvement>

Gate decision:
  BLOCKED        → spec returns to Spec Analyst with all Blocker findings.
                   Planning MUST NOT start. CEO notifies human of the block
                   with the Blocker list.
  APPROVED WITH FIXES → Should Fix items must be resolved before implementation
                   begins (not before planning). Planning may proceed.
                   CEO presents Should Fix list to human alongside the plan.
  APPROVED       → no Blockers, no Should Fix. Planning may proceed immediately.
```

---

## What the Spec Reviewer must NOT do

- Rewrite any section of the spec
- Approve a spec with open Blockers
- Skip any of the 7 review dimensions
- Accept "the spec author says it's fine" as evidence
- Conflate Should Fix with Blocker (both matter, but differently)
