---
description: Generate a design spec for the current feature using the design spec template.
handoffs:
  - label: Evaluate Design Spec
    agent: speckit.designspec.eval
    prompt: Evaluate the design spec
    send: true
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --paths-only
  ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

Goal: Generate a complete design spec document for the current feature using the design spec template.

1. Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse JSON for `FEATURE_DIR`.

2. **Prerequisite check**:
   - Check if `FEATURE_DIR/plan.md` exists
   - If plan.md does NOT exist: warn the user ("plan.md not found. It is recommended to run `/speckit.plan` first.") and ask for confirmation to continue
   - If user confirms, proceed; if not, halt

3. **Check for existing output**:
   - Check if `FEATURE_DIR/design-spec.md` already exists
   - If it exists: ask the user "design-spec.md already exists. Overwrite? (yes/no)"
   - If user says no: halt; if yes or file does not exist: proceed

4. **Load context**:
   - Read `FEATURE_DIR/spec.md` (requirements, user stories, clarifications)
   - Read `FEATURE_DIR/plan.md` (architecture, tech stack, data model, decisions)
   - If `FEATURE_DIR/function-spec.md` exists: read it for behavioral details
   - If `FEATURE_DIR/data-model.md` exists: read it for entity definitions
   - Read `templates/design-spec-template.md` to understand the required structure

5. **Generate design spec**:
   - Follow the structure defined in `templates/design-spec-template.md` exactly
   - Fill ALL sections with AI-generated content based on the loaded context
   - Do NOT leave placeholder text — every section must have substantive content
   - Sections covering architecture, components, interfaces, and data flow should reference actual entities and decisions from plan.md and data-model.md
   - Write in the same language as spec.md

6. **Write output**:
   - Save the generated design spec to `FEATURE_DIR/design-spec.md`

7. **Report completion**:
   - Confirm the file was written and show the path
   - Suggest running `/speckit.designspec.eval` to evaluate quality
