---
description: Generate a function spec for the current feature using the function spec template.
handoffs:
  - label: Evaluate Function Spec
    agent: speckit.funcspec.eval
    prompt: Evaluate the function spec
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

Goal: Generate a complete function spec document for the current feature using the function spec template.

1. Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse JSON for `FEATURE_DIR` and `FEATURE_SPEC`. For single quotes in args, use escape syntax.

2. **Prerequisite check**:
   - Check if `FEATURE_SPEC` (spec.md) exists in FEATURE_DIR
   - If spec.md does NOT exist: warn the user ("spec.md not found. It is recommended to run `/speckit.specify` first.") and ask for confirmation to continue
   - If user confirms, proceed; if not, halt

3. **Check for existing output**:
   - Check if `FEATURE_DIR/function-spec.md` already exists
   - If it exists: ask the user "function-spec.md already exists. Overwrite? (yes/no)"
   - If user says no: halt
   - If user says yes or file does not exist: proceed

4. **Load context**:
   - Read `FEATURE_DIR/spec.md` (functional requirements, user stories, clarifications, assumptions)
   - Read `templates/function-spec-template.md` to understand the required structure and all sections
   - If `FEATURE_DIR/plan.md` exists: read it for additional technical context

5. **Generate function spec**:
   - Follow the structure defined in `templates/function-spec-template.md` exactly
   - Fill ALL sections with AI-generated content based on the feature context from spec.md
   - Do NOT leave placeholder text or empty sections — every section must have substantive content
   - Use the feature name, requirements, user stories, and clarifications from spec.md as the primary source
   - Write in the same language as spec.md

6. **Write output**:
   - Save the generated function spec to `FEATURE_DIR/function-spec.md`

7. **Report completion**:
   - Confirm the file was written
   - Show the path to the generated file
   - Suggest running `/speckit.funcspec.eval` to evaluate the quality of the generated spec
