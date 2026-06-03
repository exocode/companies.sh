# Presets (future)

This directory is a placeholder for adopting or deriving [Spec-Kit community presets](https://github.github.com/spec-kit/community/presets.html).

Nothing here is required to run SpecKit Agency today. It exists so presets can be layered later **without changing the orchestration**.

## How a preset would slot in

A Spec-Kit preset is essentially a bundled set of defaults — typically a starting **constitution** and **plan** conventions for a given kind of project (web app, CLI, library, etc.). Because `spec-flow` keys off Spec-Kit's standard artifacts and never hardcodes their content, a preset only needs to seed two phases:

| Preset supplies | Seeds phase | Effect |
|-----------------|-------------|--------|
| Constitution defaults | 0 `speckit-constitution` | The CEO loads the preset's principles instead of authoring from blank |
| Plan/stack conventions | 4 `speckit-plan` | The Solution Architect starts from the preset's architecture defaults |

The pipeline order, the slice-refinement pass, the QA/review→refine loop, and the human-escalation gate all stay exactly as they are.

## Suggested layout (when you add one)

```
presets/
└── <preset-slug>/
    ├── PRESET.md          # name, description, which Spec-Kit preset it derives from
    ├── constitution.md    # seed for phase 0
    └── plan-defaults.md   # seed for phase 4
```

Keep each preset's provenance (source URL + commit) in `PRESET.md`, the same way external skills cite their `sources`.
