# Quickstart: Spec Generation & Evaluation Commands

**Feature**: 001-extend-spec-commands | **Date**: 2026-02-26

## 扩展后的完整工作流

```text
/speckit.specify      → 创建 spec.md
/speckit.clarify      → 澄清需求
/speckit.funcspec     → 生成 function-spec.md        ← NEW
/speckit.funcspec.eval → 评估 function spec           ← NEW
/speckit.plan         → 创建 plan.md
/speckit.designspec   → 生成 design-spec.md           ← NEW
/speckit.designspec.eval → 评估 design spec           ← NEW
/speckit.tasks        → 生成 tasks.md
/speckit.analyze      → 分析一致性
/speckit.implement    → 执行实现
/speckit.testspec     → 生成 test-spec.md             ← NEW
/speckit.testspec.eval → 评估 test spec               ← NEW
```

## 各命令说明

### /speckit.funcspec

在 `/speckit.clarify` 完成后运行。AI 读取 `spec.md` 和 `templates/function-spec-template.md`，生成完整的 `function-spec.md`。

**输出文件**: `specs/{feature}/function-spec.md`

### /speckit.funcspec.eval

在 `/speckit.funcspec` 完成后运行。AI 按模板章节结构评估 `function-spec.md`，输出评分报告。

**输出文件**: `specs/{feature}/function-spec-eval.md`（同时打印到控制台）

**评分等级**:
- ≥80：可接受，可继续下一步
- 60-79：需要修订，建议修改后重新评估
- <60：需要重大修订，必须修改后重新运行 `/speckit.funcspec`

### /speckit.designspec

在 `/speckit.plan` 完成后运行。AI 读取 `spec.md`、`plan.md` 和 `templates/design-spec-template.md`，生成完整的 `design-spec.md`。

**输出文件**: `specs/{feature}/design-spec.md`

### /speckit.designspec.eval

在 `/speckit.designspec` 完成后运行。评估 `design-spec.md` 质量。

**输出文件**: `specs/{feature}/design-spec-eval.md`

### /speckit.testspec

在 `/speckit.implement` 完成后运行。AI 读取 `spec.md`、`tasks.md` 和 `templates/test-spec-template.md`，生成完整的 `test-spec.md`。

**输出文件**: `specs/{feature}/test-spec.md`

### /speckit.testspec.eval

在 `/speckit.testspec` 完成后运行。评估 `test-spec.md` 质量。

**输出文件**: `specs/{feature}/test-spec-eval.md`

## 前置条件行为

| 情况 | 行为 |
|------|------|
| feature 目录不存在 | 硬阻断，提示运行 `/speckit.specify` |
| 依赖文件缺失（如 plan.md） | 软警告，等待用户确认后继续 |
| 目标文件已存在 | 提示确认是否覆盖 |
| 被评估的 spec 文件不存在 | 硬阻断，提示先运行对应生成命令 |
