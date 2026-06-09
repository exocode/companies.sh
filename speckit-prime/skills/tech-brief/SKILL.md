---
name: tech-brief
description: >
  Human gate before /speckit.plan. The CEO presents technology proposals
  for the human to accept, override, or extend. The result becomes the
  $ARGUMENTS passed to /speckit.plan — so the plan is built on decisions
  the human actually made, not defaults the agent invented.
---

# Tech Brief — Human Technology Gate

This skill runs once, immediately after spec-review APPROVED and before
the Solution Architect is dispatched. It is the human's opportunity to
steer all technology decisions before any plan is written.

## What you do (CEO)

### Step 1: read the spec and constitution

Load `spec.md` and `.specify/memory/constitution.md`. Extract:

- What type of system this is (web app, API, CLI, data pipeline, library, etc.)
- Any technology constraints already stated in the constitution or spec
- Integrations and external dependencies the spec requires
- Scale, performance, and operational requirements from the spec

### Step 2: form technology proposals

For each decision category below, propose a default based on the spec and
constitution. Mark each proposal with one of:

- `CONSTITUTION` — already fixed by the constitution, not open for discussion
- `SPEC` — implied or required by the spec
- `PROPOSED` — your recommendation based on context, open for human input
- `OPEN` — no defensible default exists, human must decide

Decision categories to cover (omit categories not relevant to this feature):

**Runtime & language**
- Primary language and version
- Runtime environment

**Framework & libraries**
- Application framework (web, API, CLI, data)
- Key libraries for the main feature concern

**Data storage**
- Primary database (type + product)
- Cache / ephemeral storage if needed
- File / blob storage if needed
- Vector / search storage if needed

**AI / ML components** (if applicable)
- LLM provider or local model
- Embedding model
- Inference runtime

**Infrastructure**
- Hosting / deployment target
- Containerisation approach

**Observability**
- Logging, metrics, tracing approach (keep it proportional to the feature)

### Step 3: present to the human

Format the presentation as a clean table. Do not bury it in prose.

```
TECH BRIEF — <feature name>
Before I dispatch the Solution Architect, I need your input on technology
choices. Constitution-fixed decisions are shown for reference only.

Category              | Decision              | Status       | Notes
----------------------|-----------------------|--------------|------
Language              | <proposal>            | PROPOSED     | <why>
Framework             | <proposal>            | PROPOSED     | <why>
Database              | <proposal>            | PROPOSED     | <why>
...                   | ...                   | OPEN         | needs your decision

Reply with one of:
  "approved"              — accept all PROPOSED defaults, decide all OPEN items
                            with the proposed fallback where one exists
  "approved, <changes>"  — accept defaults except where you specify overrides
  <full replacement>      — specify your own choices for any category

You can also add free-form requirements: "use TypeScript strict mode",
"no external AI APIs — local models only", "must run in Docker on-prem".
```

### Step 4: compile the plan arguments

Once the human responds, compile their decisions into a `PLAN ARGUMENTS`
block that becomes the `$ARGUMENTS` for `/speckit.plan`:

```
PLAN ARGUMENTS
Language:     <decision>
Framework:    <decision>
Database:     <decision>
[...]
Additional constraints: <free-form human input if any>
```

Pass this block verbatim as the argument to the Solution Architect when
dispatching. The Solution Architect must treat every item in PLAN ARGUMENTS
as a fixed decision — not a suggestion to reconsider.

## What the CEO must NOT do

- Skip the tech brief because "the spec implies the tech stack"
- Accept the Solution Architect's self-decided tech choices as the plan
- Present more than one round of questions — batch everything into one message
- Invent a tech stack and present it as "confirmed" without human input
