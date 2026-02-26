---
description: Generate a test spec for the current feature using the test spec template.
handoffs:
  - label: Evaluate Test Spec
    agent: speckit.testspec.eval
    prompt: Evaluate the test spec
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

Goal: Generate a complete test spec document for the current feature using the test spec template.

1. Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse JSON for `FEATURE_DIR`.

2. **Prerequisite check**:
   - Check if `FEATURE_DIR/tasks.md` exists
   - If tasks.md does NOT exist: warn the user ("tasks.md not found. It is recommended to run `/speckit.tasks` first.") and ask for confirmation to continue
   - If user confirms, proceed; if not, halt

3. **Check for existing output**:
   - Check if `FEATURE_DIR/test-spec.md` already exists
   - If it exists: ask the user "test-spec.md already exists. Overwrite? (yes/no)"
   - If user says no: halt; if yes or file does not exist: proceed

4. **Load context**:
   - Read `FEATURE_DIR/spec.md` (user stories, acceptance scenarios, edge cases, success criteria)
   - Read `FEATURE_DIR/tasks.md` (implemented tasks and feature scope)
   - If `FEATURE_DIR/function-spec.md` exists: read it for behavioral details and use cases
   - If `FEATURE_DIR/plan.md` exists: read it for technical context
   - Read `templates/test-spec-template.md` to understand the required structure

5. **Generate test spec**:
   - Follow the structure defined in `templates/test-spec-template.md` exactly
   - Fill ALL sections with AI-generated content based on the loaded context
   - The functional test section MUST include a test case table for each major feature/command
   - Test cases should be derived from the acceptance scenarios in spec.md and the tasks in tasks.md
   - Do NOT leave placeholder text — every section must have substantive content
   - Write in the same language as spec.md

6. **Write output**:
   - Save the generated test spec to `FEATURE_DIR/test-spec.md`

7. **Report completion**:
   - Confirm the file was written and show the path
   - Suggest running `/speckit.testspec.eval` to evaluate quality
