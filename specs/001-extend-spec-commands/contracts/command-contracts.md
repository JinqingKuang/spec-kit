# Command Contracts: Spec Generation & Evaluation Commands

**Feature**: 001-extend-spec-commands | **Date**: 2026-02-26

## Contract Format

每个命令文件遵循 speckit 命令契约：YAML frontmatter + Markdown 执行指令。

---

## speckit.funcspec

**File**: `.claude/commands/speckit.funcspec.md` / `templates/commands/funcspec.md`

```yaml
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
```

**Input**: FEATURE_DIR (via check-prerequisites.sh), `spec.md`, `templates/function-spec-template.md`
**Output**: `FEATURE_DIR/function-spec.md`
**Precondition check**: 软警告（spec.md 缺失时提示，等待确认）
**Overwrite behavior**: 目标文件已存在时提示确认

---

## speckit.funcspec.eval

**File**: `.claude/commands/speckit.funcspec.eval.md` / `templates/commands/funcspec.eval.md`

```yaml
---
description: Evaluate the function spec quality and provide a score with improvement suggestions.
handoffs:
  - label: Build Technical Plan
    agent: speckit.plan
    prompt: Create a plan for the spec
---
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --paths-only
  ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
---
```

**Input**: `FEATURE_DIR/function-spec.md`, `templates/function-spec-template.md`
**Output**: Console + `FEATURE_DIR/function-spec-eval.md`
**Score formula**: `total = completeness * 0.4 + quality * 0.6`
**Precondition check**: 硬阻断（function-spec.md 不存在时报错退出）

---

## speckit.designspec

**File**: `.claude/commands/speckit.designspec.md` / `templates/commands/designspec.md`

```yaml
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
```

**Input**: FEATURE_DIR, `spec.md`, `plan.md`, `function-spec.md`（可选）, `templates/design-spec-template.md`
**Output**: `FEATURE_DIR/design-spec.md`
**Precondition check**: 软警告（plan.md 缺失时提示，等待确认）

---

## speckit.designspec.eval

**File**: `.claude/commands/speckit.designspec.eval.md` / `templates/commands/designspec.eval.md`

```yaml
---
description: Evaluate the design spec quality and provide a score with improvement suggestions.
handoffs:
  - label: Create Tasks
    agent: speckit.tasks
    prompt: Break the plan into tasks
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --paths-only
  ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
---
```

**Input**: `FEATURE_DIR/design-spec.md`, `templates/design-spec-template.md`
**Output**: Console + `FEATURE_DIR/design-spec-eval.md`
**Precondition check**: 硬阻断（design-spec.md 不存在时报错退出）

---

## speckit.testspec

**File**: `.claude/commands/speckit.testspec.md` / `templates/commands/testspec.md`

```yaml
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
```

**Input**: FEATURE_DIR, `spec.md`, `tasks.md`, `function-spec.md`（可选）, `templates/test-spec-template.md`
**Output**: `FEATURE_DIR/test-spec.md`
**Precondition check**: 软警告（tasks.md 缺失时提示，等待确认）

---

## speckit.testspec.eval

**File**: `.claude/commands/speckit.testspec.eval.md` / `templates/commands/testspec.eval.md`

```yaml
---
description: Evaluate the test spec quality and provide a score with improvement suggestions.
handoffs: []
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --paths-only
  ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
---
```

**Input**: `FEATURE_DIR/test-spec.md`, `templates/test-spec-template.md`
**Output**: Console + `FEATURE_DIR/test-spec-eval.md`
**Precondition check**: 硬阻断（test-spec.md 不存在时报错退出）

---

## Eval Report Format Contract

所有 eval 命令输出的报告文件遵循统一格式：

```markdown
# [Spec Type] Evaluation Report

**Feature**: [feature name]
**Date**: [YYYY-MM-DD]
**Target**: [spec file path]

## Score Summary

| Dimension | Score | Weight | Weighted |
|-----------|-------|--------|---------|
| Section Completeness | XX/100 | 40% | XX |
| Content Quality | XX/100 | 60% | XX |
| **Total** | | | **XX/100** |

**Grade**: [Acceptable / Needs Revision / Major Revision]

## Improvement Suggestions

1. [Specific, actionable suggestion]
2. [Specific, actionable suggestion]
3. [Specific, actionable suggestion]
...

## Section Details

[Per-section breakdown of completeness and quality assessment]
```
