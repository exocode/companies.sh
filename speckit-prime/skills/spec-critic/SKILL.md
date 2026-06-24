---
name: spec-critic
description: >
  Autonomous critical review of a spec.md or constitution.md before it reaches
  the human. Adversarial read: finds gaps, contradictions, untestable
  requirements, scope ambiguity, and constitution violations. Produces a
  structured CRITIC REPORT. Owned by the Spec Analyst (spec.md) and CEO
  (constitution.md). Always runs before the human gate.
---

# Spec Critic — Autonomous Pre-Human Review

This skill runs an adversarial pass over a freshly produced `spec.md` or
`constitution.md`. Its job is to catch everything the producing agent
normalised away or missed — before a human spends time on it.

## When to run

- **After every `/speckit.specify` run** — the Spec Analyst runs this on
  `spec.md` before handing back to the CEO for the human clarify gate.
- **After every `/speckit.constitution` run** — the CEO runs this on
  `constitution.md` before the pipeline advances to specify.

Do not skip this pass even if the producing agent reports "complete" or
"no clarification needed." The critic is independent and does not trust
the producer's self-assessment.

## What you do

Read the artifact as a hostile reviewer. Work through each dimension below.
For every issue found, record it with a severity tag. At the end, emit
the CRITIC REPORT.

### Dimension 1 — Completeness

- Are all mandatory sections present and non-empty?
- Are there any placeholder phrases like "TBD", "to be defined", "N/A",
  "see later", or template text that was never replaced?
- Does every functional requirement have at least one acceptance scenario?
- Are success criteria measurable — do they name a metric, threshold, or
  observable outcome? "Fast", "easy", "robust" without a number are not
  measurable.
- For a constitution: does it cover the key governance areas — technology
  constraints, quality standards, scope boundaries, team norms?

### Dimension 2 — Testability

- Can each requirement be verified by an independent tester without talking
  to the author?
- Are acceptance scenarios written in Given/When/Then form with concrete
  values, or are they vague narratives?
- Are there requirements that describe internal system behaviour rather than
  observable outcomes? (These belong in the plan, not the spec.)

### Dimension 3 — Internal consistency

- Do any two requirements contradict each other?
- Does the scope section ("this feature does not include X") conflict with
  a functional requirement that implicitly requires X?
- Are the same concepts named differently in different sections? (Terminology
  drift is a defect.)
- For a constitution: do the stated principles conflict with each other?

### Dimension 4 — Constitution alignment (spec.md only)

- Does the spec respect every constraint in `.specify/memory/constitution.md`?
  Read the constitution first if not already loaded.
- Does the spec introduce technology choices, architecture decisions, or team
  norms that the constitution prohibits or reserves for the planning phase?
- Does the spec hardcode a tech-stack decision (framework, database, language,
  library, hosting target, AI/ML component) that bypasses the tech-brief
  human gate? If yes, flag as [BLOCK] — tech-stack decisions belong in the
  tech-brief gate, not in the spec. The spec must describe WHAT, not HOW.
- Does the scope match the boundaries the constitution sets for this type of
  feature?

### Dimension 5 — Scope clarity

- Is it clear what is IN scope and what is OUT of scope?
- Are there implicit requirements hiding in user scenarios that are not
  listed in the functional requirements?
- Could a developer read this spec and build the wrong thing while following
  it literally? If yes, the ambiguity is a defect.

### Dimension 6 — Human-readiness

- Is the spec written for a non-technical stakeholder, or does it leak
  implementation detail (framework names, database schemas, API endpoints)?
  Implementation detail belongs in the plan.
- Are there more than 3 unresolved `[NEEDS CLARIFICATION]` markers?
  More than 3 means the spec is not ready for human review — it needs
  another clarify pass first.

---

## CRITIC REPORT format

Always end with this block, even if the verdict is PASS:

```
CRITIC REPORT
Artifact: <spec.md | constitution.md> at <path>
Verdict:  <PASS | PASS-WITH-NOTES | FAIL>

Findings: (<N> total — <blocking> blocking, <advisory> advisory)

[BLOCK] <dimension> — <description of the defect> — fix: <what needs to change>
[ADVISE] <dimension> — <observation> — suggestion: <what would strengthen it>

Summary:
  Blocking findings must be resolved before this artifact goes to the human.
  Advisory findings are improvements that can be addressed now or deferred.

Recommended next action:
  <PASS>             → route to human gate as planned
  <PASS-WITH-NOTES>  → route to human gate, include advisory findings in the
                       summary presented to the human
  <FAIL>             → return to the producing agent with the blocking findings;
                       do not route to the human until a PASS is issued
```

### Severity rules

- `[BLOCK]` — missing mandatory section, direct contradiction, constitution
  violation, untestable success criteria, placeholder text, more than 3 open
  clarification markers
- `[ADVISE]` — terminology inconsistency, weak but not absent acceptance
  scenario, scope that could be tighter, implementation detail that slipped in

A FAIL verdict with zero `[BLOCK]` findings is a logic error — recheck.
A PASS verdict with `[BLOCK]` findings is a logic error — recheck.
