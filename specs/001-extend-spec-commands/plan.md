# Implementation Plan: Extend Speckit Workflow with Spec Generation and Evaluation Commands

**Branch**: `001-extend-spec-commands` | **Date**: 2026-02-26 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-extend-spec-commands/spec.md`

## Summary

为 speckit 工作流新增 6 个命令：3 个 AI 生成命令（funcspec、designspec、testspec）和 3 个评估命令（funcspec.eval、designspec.eval、testspec.eval）。实现方式为在 `templates/commands/` 添加 Markdown 命令文件，并同步到 `.claude/commands/`，无需修改 Python CLI 或 Bash 脚本。

## Technical Context

**Language/Version**: Markdown (命令文件), Python 3.11 (specify-cli), Bash (现有脚本)
**Primary Dependencies**: 无新依赖，复用现有 `check-prerequisites.sh` 脚本
**Storage**: 文件系统（Markdown 文件，保存在 feature 目录和 `.claude/commands/`）
**Testing**: 手动验证命令输出（生成文件结构 + 评估报告格式）
**Target Platform**: Linux/macOS（bash），任何支持 Claude Code 的平台
**Project Type**: CLI tool / AI agent workflow toolkit
**Performance Goals**: 每个命令 30 秒内完成（SC-001）
**Constraints**: 命令文件结构必须与现有命令保持一致（frontmatter + handoffs）
**Scale/Scope**: 6 个新 Markdown 命令文件，2 个目录（templates/commands/ + .claude/commands/）

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Spec-First | ✅ Pass | spec.md 已完成，包含澄清记录 |
| II. AI-Agent Agnosticism | ✅ Pass | 主要产出物在 `templates/commands/`（agent-agnostic），`.claude/commands/` 为 Claude 专属副本 |
| III. Incremental Delivery | ✅ Pass | 6 个用户故事各自独立可测试，可按优先级逐步交付 |
| IV. Simplicity | ✅ Pass | 纯 Markdown 文件，无新依赖，无新脚本，最小复杂度 |
| Gate 1 (Pre-Plan) | ✅ Pass | spec.md 包含 SC-001~SC-005 可量化成功标准和 P1~P6 优先级用户故事 |

无 Constitution 违规，无需填写 Complexity Tracking 表。

## Project Structure

### Documentation (this feature)

```text
specs/001-extend-spec-commands/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output
└── tasks.md             # Phase 2 output (/speckit.tasks)
```

### Source Code (repository root)

```text
templates/commands/          # Canonical agent-agnostic source
├── funcspec.md              # NEW: generate function spec
├── funcspec.eval.md         # NEW: evaluate function spec
├── designspec.md            # NEW: generate design spec
├── designspec.eval.md       # NEW: evaluate design spec
├── testspec.md              # NEW: generate test spec
└── testspec.eval.md         # NEW: evaluate test spec

.claude/commands/            # Claude-specific installed copies
├── speckit.funcspec.md      # NEW
├── speckit.funcspec.eval.md # NEW
├── speckit.designspec.md    # NEW
├── speckit.designspec.eval.md # NEW
├── speckit.testspec.md      # NEW
└── speckit.testspec.eval.md # NEW
```

**Structure Decision**: 纯文件添加，无新目录结构。`templates/commands/` 为权威来源，`.claude/commands/` 为 Claude 运行时副本，两者内容相同（仅命令名前缀不同）。

## Phase 0: Research

### Research Findings

#### Decision 1: 命令文件结构

**Decision**: 复用现有命令的 frontmatter 结构（description + handoffs + scripts）

**Rationale**: 所有现有命令（clarify、plan、analyze 等）均使用相同的 YAML frontmatter 格式。新命令遵循相同模式确保一致性，且 specify-cli 的安装逻辑依赖此格式。

**Alternatives considered**: 自定义格式 — 被拒绝，会破坏 specify-cli 的命令解析逻辑。

#### Decision 2: 前置条件检查方式

**Decision**: 使用现有 `check-prerequisites.sh --json --paths-only` 脚本，在命令内容中通过 AI 逻辑检查依赖文件是否存在，不新增脚本标志

**Rationale**: 现有脚本已返回 FEATURE_DIR 和所有关键路径。依赖文件的存在性检查（如 plan.md 是否存在）可在命令 Markdown 内容中由 AI 执行，无需修改 Bash 脚本。

**Alternatives considered**: 新增 `--require-funcspec` 等脚本标志 — 被拒绝，过度工程化，现有模式已足够。

#### Decision 3: 评估维度权重

**Decision**: 章节完整性（40%）+ 内容质量（60%），内容质量按章节数量平均分配

**Rationale**: 内容质量比章节存在性更重要（空章节无价值），但章节完整性是基础门槛。60/40 权重平衡了两者。

**Alternatives considered**: 等权重（50/50）— 被拒绝，空章节不应与充实章节等价。

#### Decision 4: templates/commands/ 与 .claude/commands/ 的关系

**Decision**: 两个目录均需添加新文件。`templates/commands/` 使用无前缀命名（`funcspec.md`），`.claude/commands/` 使用 `speckit.` 前缀（`speckit.funcspec.md`）

**Rationale**: 查看现有文件确认此模式：`templates/commands/clarify.md` 对应 `.claude/commands/speckit.clarify.md`，内容相同。

**Alternatives considered**: 仅修改 `.claude/commands/` — 被拒绝，违反 Constitution Principle II（AI-Agent Agnosticism），templates/ 是权威来源。

#### Decision 5: Handoffs 链设计

**Decision**: 生成命令 handoff 到对应的 eval 命令；eval 命令 handoff 到工作流下一步

| 命令 | Handoff To |
|------|-----------|
| speckit.funcspec | speckit.funcspec.eval |
| speckit.funcspec.eval | speckit.plan |
| speckit.designspec | speckit.designspec.eval |
| speckit.designspec.eval | speckit.tasks |
| speckit.testspec | speckit.testspec.eval |
| speckit.testspec.eval | (无，工作流结束) |

**Rationale**: 与现有命令的 handoff 模式一致（每个命令指向逻辑下一步）。


## Constitution Check (Post-Design)

*Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Spec-First | ✅ Pass | 所有设计决策均基于 spec.md 中的需求 |
| II. AI-Agent Agnosticism | ✅ Pass | 主要产出物在 `templates/commands/`，`.claude/commands/` 为派生副本；设计未假设特定 AI 能力 |
| III. Incremental Delivery | ✅ Pass | 6 个命令可按 P1→P6 顺序独立交付，每个命令独立可测试 |
| IV. Simplicity | ✅ Pass | 纯 Markdown 文件，无新脚本，无新依赖，无抽象层 |
| Gate 2 (Pre-Tasks) | ✅ Pass | plan.md 通过 Constitution Check，research.md 已完成，所有 NEEDS CLARIFICATION 已解决 |

无违规，无需 Complexity Tracking。
