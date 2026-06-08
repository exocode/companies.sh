---
name: speckit-paperclip-mode
description: >
  Paperclip Desktop compatibility adapter. When SpecKit native commands
  (specify, clarify, etc.) are not accessible via shell in this session,
  this skill defines how agents degrade gracefully: produce artifacts as
  structured text output and report paths for persistence.
---

# Spec-Kit in Paperclip Desktop — Shell-less Mode

Paperclip Desktop runs agents as LLM sessions with no shell sandbox and no
direct filesystem write access. Native SpecKit commands like `/speckit.specify`
cannot be executed as shell processes.

## The core rule

When a speckit-* skill says "invoke the native /speckit.X command" and no
shell is available in this session:

1. Do not halt or report as blocked.
2. Produce the artifact that the command would have produced — as complete,
   structured markdown text in your response — following the output format
   SpecKit's templates define (spec.md, plan.md, tasks.md, etc.).
3. End your response with a PAPERCLIP ARTIFACT REPORT block (see below) so
   the CEO knows what was produced and where it should be persisted.

## How to know SpecKit's output format without running it

SpecKit's templates are in the initialized project under:
- `.specify/templates/spec-template.md`
- `.specify/templates/plan-template.md`
- `.specify/templates/tasks-template.md`
- `.specify/templates/checklist-template.md`

If the project is not initialized (no `.specify/` directory), tell the CEO
and suggest `uvx --from git+https://github.com/github/spec-kit.git specify init .`

## Path resolution without shell

Resolve FEATURE_DIR in this order (no shell needed):
1. Explicit path the CEO passed in the task message.
2. The most recent `PAPERCLIP ARTIFACT REPORT` in the conversation.
3. Default: `specs/001-<short-feature-name>`.

## PAPERCLIP ARTIFACT REPORT format

End every phase-completing response with this block:

```
--- PAPERCLIP ARTIFACT REPORT ---
Phase:        <e.g. specify>
Agent:        <your agent name>
FEATURE_DIR:  <e.g. specs/001-user-auth>
Artifacts:
  - <relative path>: <one-line description>
Shell status: native command not available — artifact produced as text output
Next phase:   <what the CEO should dispatch next>
--- END REPORT ---
```

## Resume detection without shell

Scan the conversation history for PAPERCLIP ARTIFACT REPORT blocks. The latest
report per artifact type determines what exists. Treat any artifact listed in
a report as done for that phase.

## What this skill does NOT change

Pipeline order, artifact structure, QA review loop, independence mandate,
delegation rules — none of that changes. This skill only replaces shell
execution with text production. Quality standards are identical.
