---
name: speckit-artifact-script-contract
description: Respect Spec Kit's native feature directory structure, templates, feature-state files, and helper scripts during workflow execution and resume
metadata:
  sources:
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/tasks.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
    - kind: github-file
      repo: github/spec-kit
      path: templates/commands/plan.md
      commit: ed10b32014431a15c4e54e4ed7c92452230dd193
      attribution: GitHub
      license: MIT
      usage: referenced
---

# Spec Kit Artifact and Script Contract

Use this skill whenever locating, creating, validating, or resuming Spec Kit artifacts.

## Native Structure

Respect the initialized project structure:

```text
.specify/
|-- feature.json
|-- memory/constitution.md
|-- scripts/
|   |-- bash/
|   `-- powershell/
`-- templates/

specs/
`-- <feature-id>/
    |-- spec.md
    |-- plan.md
    |-- research.md
    |-- data-model.md
    |-- contracts/
    |-- quickstart.md
    |-- tasks.md
    `-- checklists/
```

Feature ids are commonly `NNN-short-name`, but Spec Kit may also use timestamp-based numbering or an explicit `SPECIFY_FEATURE_DIRECTORY`.

## Feature Directory Resolution

Resolve the active feature directory in this order:

1. Explicit user or environment value for `SPECIFY_FEATURE_DIRECTORY`.
2. `.specify/feature.json` containing `feature_directory`.
3. JSON output from the relevant local Spec Kit helper script.
4. Current branch or matching `specs/<feature-id>/` directory.
5. Ask the user if multiple plausible feature directories remain.

Do not assume that the git branch name and spec directory name are identical.

## Helper Scripts

When initialized scripts exist, use them or honor command-provided paths before guessing:

- `create-new-feature.{sh,ps1}` for feature branch/spec setup when applicable
- `setup-plan.{sh,ps1} --json` for `FEATURE_SPEC`, `IMPL_PLAN`, `SPECS_DIR`, and branch context
- `setup-tasks.{sh,ps1} --json` for `FEATURE_DIR`, `TASKS_TEMPLATE`, and `AVAILABLE_DOCS`
- `check-prerequisites.{sh,ps1} --json` for prerequisite validation and feature path discovery
- `common.{sh,ps1}` as shared path-resolution logic used by the scripts

Prefer the script variant appropriate to the importing environment. If no script can be executed, document the fallback and keep path resolution conservative.

## Artifact Expectations

Use these phase outputs:

- `/speckit.constitution`: `.specify/memory/constitution.md`
- `/speckit.specify`: `specs/<feature-id>/spec.md` and `.specify/feature.json`
- `/speckit.clarify`: updated `spec.md`, optionally updated `checklists/requirements.md`
- `/speckit.checklist`: files under `specs/<feature-id>/checklists/`
- `/speckit.plan`: `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md` as applicable
- `/speckit.tasks`: `tasks.md`
- `/speckit.analyze`: read-only consistency findings across `spec.md`, `plan.md`, and `tasks.md`
- `/speckit.implement`: source changes plus verification evidence driven by `tasks.md`

## Contract

Scripts and artifacts are part of the workflow contract. A workflow may refine plans or tasks, but it must preserve Spec Kit's expected paths and command inputs so later commands can resume correctly.
