---
description: Evaluate the function spec quality and provide a score with improvement suggestions.
handoffs:
  - label: Build Technical Plan
    agent: speckit.plan
    prompt: Create a plan for the spec. I am building with...
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

Goal: Evaluate the quality of the function spec and produce a scored report with improvement suggestions.

1. Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse JSON for `FEATURE_DIR`.

2. **Prerequisite check (hard block)**:
   - Check if `FEATURE_DIR/function-spec.md` exists
   - If it does NOT exist: ERROR — "function-spec.md not found. Please run `/speckit.funcspec` first." and halt immediately

3. **Load context**:
   - Read `FEATURE_DIR/function-spec.md` (the document to evaluate)
   - Read `templates/function-spec-template.md` (the authoritative structure)

4. **Evaluate by template sections**:

   For each section defined in `templates/function-spec-template.md`:

   **Dimension 1 — Section Completeness (weight: 40%)**:
   - Check if the section exists in function-spec.md
   - Score: (sections present / total required sections) × 100

   **Dimension 2 — Content Quality (weight: 60%)**:
   For each present section, assess:
   - Is the content substantive (not placeholder text like "XXX", "TODO", "YYYY-MM-DD")?
   - Is the content specific to this feature (not generic boilerplate)?
   - Is the content complete enough to be actionable?
   Score each section 0–100, then average across all sections.

   **Total Score**: `completeness_score × 0.4 + quality_score × 0.6`

   **Grade**:
   - ≥ 80: Acceptable — ready to proceed
   - 60–79: Needs Revision — address suggestions before proceeding
   - < 60: Major Revision Required — significant rework needed

5. **Generate improvement suggestions**:
   - Produce at least 3 specific, actionable suggestions
   - Each suggestion must reference a specific section and describe exactly what is missing or weak
   - Avoid generic advice like "improve clarity" — be concrete

6. **Write evaluation report**:
   - Save to `FEATURE_DIR/function-spec-eval.md` using this structure:

   ```markdown
   # Function Spec Evaluation Report

   **Feature**: [feature name]
   **Date**: [YYYY-MM-DD]
   **Target**: [path to function-spec.md]

   ## Score Summary

   | Dimension | Score | Weight | Weighted |
   |-----------|-------|--------|---------|
   | Section Completeness | XX/100 | 40% | XX |
   | Content Quality | XX/100 | 60% | XX |
   | **Total** | | | **XX/100** |

   **Grade**: [Acceptable / Needs Revision / Major Revision Required]

   ## Improvement Suggestions

   1. [Section name]: [Specific, actionable suggestion]
   2. [Section name]: [Specific, actionable suggestion]
   3. [Section name]: [Specific, actionable suggestion]

   ## Section Details

   | Section | Present | Quality | Notes |
   |---------|---------|---------|-------|
   | [section] | ✅/❌ | XX/100 | [notes] |
   ```

7. **Print report to console** and confirm the file was saved to `FEATURE_DIR/function-spec-eval.md`.

8. **Next step guidance**:
   - If grade is Acceptable: suggest proceeding to `/speckit.plan`
   - If grade is Needs Revision or Major Revision: suggest editing function-spec.md and re-running `/speckit.funcspec.eval`
