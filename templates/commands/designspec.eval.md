---
description: Evaluate the design spec quality and provide a score with improvement suggestions.
handoffs:
  - label: Implement Feature
    agent: speckit.implement
    prompt: Implement the feature
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

Goal: Evaluate the quality of the design spec and produce a scored report with improvement suggestions.

1. Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse JSON for `FEATURE_DIR`.

2. **Prerequisite check (hard block)**:
   - Check if `FEATURE_DIR/design-spec.md` exists
   - If it does NOT exist: ERROR — "design-spec.md not found. Please run `/speckit.designspec` first." and halt immediately

3. **Load context**:
   - Read `FEATURE_DIR/design-spec.md` (the document to evaluate)
   - Read `templates/design-spec-template.md` (the authoritative structure)

4. **Evaluate by template sections**:

   **Dimension 1 — Section Completeness (weight: 40%)**:
   - Check if each required section from the template exists in design-spec.md
   - Score: (sections present / total required sections) × 100

   **Dimension 2 — Content Quality (weight: 60%)**:
   For each present section, assess:
   - Is the content substantive (not placeholder text)?
   - Does it describe actual architecture/design decisions (not generic descriptions)?
   - Are diagrams, data structures, or interface specs present where required?
   Score each section 0–100, then average.

   **Total Score**: `completeness_score × 0.4 + quality_score × 0.6`

   **Grade**:
   - ≥ 80: Acceptable
   - 60–79: Needs Revision
   - < 60: Major Revision Required

5. **Generate improvement suggestions** (at least 3, specific and actionable).

6. **Write evaluation report** to `FEATURE_DIR/design-spec-eval.md`:

   ```markdown
   # Design Spec Evaluation Report

   **Feature**: [feature name]
   **Date**: [YYYY-MM-DD]
   **Target**: [path to design-spec.md]

   ## Score Summary

   | Dimension | Score | Weight | Weighted |
   |-----------|-------|--------|---------|
   | Section Completeness | XX/100 | 40% | XX |
   | Content Quality | XX/100 | 60% | XX |
   | **Total** | | | **XX/100** |

   **Grade**: [Acceptable / Needs Revision / Major Revision Required]

   ## Improvement Suggestions

   1. [Section name]: [Specific suggestion]
   2. [Section name]: [Specific suggestion]
   3. [Section name]: [Specific suggestion]

   ## Section Details

   | Section | Present | Quality | Notes |
   |---------|---------|---------|-------|
   ```

7. **Print report to console** and confirm file saved to `FEATURE_DIR/design-spec-eval.md`.

8. **Next step guidance**:
   - If Acceptable: suggest proceeding to `/speckit.implement`
   - Otherwise: suggest editing design-spec.md and re-running `/speckit.designspec.eval`
