# Data Model: Extend Speckit Workflow Commands

**Feature**: 001-extend-spec-commands | **Date**: 2026-02-26

## Entities

### CommandFile

命令文件，存储在 `templates/commands/` 和 `.claude/commands/`。

| Field | Type | Description |
|-------|------|-------------|
| name | string | 文件名（如 `funcspec.md`，`.claude/` 版本加 `speckit.` 前缀） |
| description | string | YAML frontmatter 中的命令描述 |
| handoffs | list[Handoff] | 命令完成后建议的下一步命令列表 |
| scripts | ScriptPair | 关联的 sh/ps 脚本（可选） |
| content | string | Markdown 格式的命令执行指令 |

### Handoff

| Field | Type | Description |
|-------|------|-------------|
| label | string | 显示给用户的按钮文字 |
| agent | string | 目标命令名（如 `speckit.funcspec.eval`） |
| prompt | string | 传递给目标命令的提示文字 |
| send | bool | 是否自动发送（默认 false） |

### ScriptPair

| Field | Type | Description |
|-------|------|-------------|
| sh | string | Bash 脚本路径和参数 |
| ps | string | PowerShell 脚本路径和参数（可选） |

### SpecFile

生成命令的输出文件，存储在 feature 目录。

| Field | Type | Description |
|-------|------|-------------|
| type | enum | `function-spec` / `design-spec` / `test-spec` |
| path | string | 绝对路径（`FEATURE_DIR/{type}.md`） |
| template_source | string | 对应模板路径（`templates/{type}-template.md`） |
| content | string | AI 根据上下文生成的 Markdown 内容 |

### EvalReport

评估命令的输出文件，存储在 feature 目录。

| Field | Type | Description |
|-------|------|-------------|
| target_spec | SpecFile | 被评估的规格文件 |
| path | string | 绝对路径（`FEATURE_DIR/{type}-eval.md`） |
| total_score | int | 0-100 总分 |
| completeness_score | int | 章节完整性得分（权重 40%） |
| quality_score | int | 内容质量得分（权重 60%） |
| grade | enum | `acceptable`(≥80) / `needs-revision`(60-79) / `major-revision`(<60) |
| suggestions | list[string] | 至少 3 条具体修改建议 |

## State Transitions

```text
[未生成] → funcspec → [function-spec.md 存在]
                    → funcspec.eval → [function-spec-eval.md 存在]

[未生成] → designspec → [design-spec.md 存在]
                      → designspec.eval → [design-spec-eval.md 存在]

[未生成] → testspec → [test-spec.md 存在]
                    → testspec.eval → [test-spec-eval.md 存在]
```

## Validation Rules

- `total_score = completeness_score * 0.4 + quality_score * 0.6`
- `grade` 由 `total_score` 决定：≥80 → acceptable，60-79 → needs-revision，<60 → major-revision
- `suggestions` 长度 ≥ 3
- 生成命令在目标文件已存在时必须提示确认（软警告）
- 前置依赖文件缺失时发出软警告，等待用户确认后继续
