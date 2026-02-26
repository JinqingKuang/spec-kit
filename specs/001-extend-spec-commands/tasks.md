# Tasks: Extend Speckit Workflow with Spec Generation and Evaluation Commands

**Input**: Design documents from `/specs/001-extend-spec-commands/`
**Prerequisites**: plan.md ✅, spec.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (US1–US6)
- Include exact file paths in descriptions

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: 确认模板文件存在，建立新命令文件的基础结构

- [X] T001 验证 `templates/function-spec-template.md`、`templates/design-spec-template.md`、`templates/test-spec-template.md` 均存在且结构完整
- [X] T002 [P] 确认 `templates/commands/` 目录结构，了解现有命令文件的 frontmatter 格式（参考 `templates/commands/clarify.md`）
- [X] T003 [P] 确认 `.claude/commands/` 目录结构，了解现有命令文件的命名规范（`speckit.` 前缀）

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: 无共享基础设施需要建立。本特性为纯文件添加，无代码依赖。

**⚠️ CRITICAL**: Phase 1 完成后即可开始所有用户故事，各故事可并行实现。

**Checkpoint**: 基础确认完成，可开始实现各命令文件

---

## Phase 3: User Story 1 - Generate Function Spec (Priority: P1) 🎯 MVP

**Goal**: 提供 `/speckit.funcspec` 命令，AI 根据 spec.md 生成完整的 function-spec.md

**Independent Test**: 运行 `/speckit.funcspec`，检查生成的 `function-spec.md` 是否覆盖 `templates/function-spec-template.md` 的所有章节，内容非占位符

### Implementation for User Story 1

- [X] T004 [US1] 创建 `templates/commands/funcspec.md`，包含正确的 YAML frontmatter（description、handoffs 到 funcspec.eval、scripts 使用 check-prerequisites.sh）和完整的 AI 执行指令（读取 spec.md + 模板、生成完整内容、软警告逻辑、覆盖确认逻辑）
- [X] T005 [US1] 创建 `.claude/commands/speckit.funcspec.md`，内容与 `templates/commands/funcspec.md` 相同（命令名使用 `speckit.funcspec.eval` 作为 handoff agent）

**Checkpoint**: `/speckit.funcspec` 可独立运行并生成完整的 function-spec.md

---

## Phase 4: User Story 2 - Evaluate Function Spec (Priority: P2)

**Goal**: 提供 `/speckit.funcspec.eval` 命令，输出 0-100 评分、各维度得分和至少 3 条修改建议，同时保存到 function-spec-eval.md

**Independent Test**: 运行 `/speckit.funcspec.eval`，检查控制台输出和生成的 `function-spec-eval.md` 是否包含总分、章节完整性分、内容质量分和 ≥3 条建议

### Implementation for User Story 2

- [X] T006 [US2] 创建 `templates/commands/funcspec.eval.md`，包含 YAML frontmatter（description、handoffs 到 speckit.plan、scripts）和完整的评估执行指令（读取 function-spec.md + 模板、按章节评分、计算 completeness*0.4 + quality*0.6、输出等级、保存到 function-spec-eval.md、硬阻断逻辑）
- [X] T007 [US2] 创建 `.claude/commands/speckit.funcspec.eval.md`，内容与 `templates/commands/funcspec.eval.md` 相同

**Checkpoint**: `/speckit.funcspec.eval` 可独立运行并输出完整评估报告

---

## Phase 5: User Story 3 - Generate Design Spec (Priority: P3)

**Goal**: 提供 `/speckit.designspec` 命令，AI 根据 spec.md 和 plan.md 生成完整的 design-spec.md

**Independent Test**: 运行 `/speckit.designspec`，检查生成的 `design-spec.md` 是否覆盖 `templates/design-spec-template.md` 的所有章节

### Implementation for User Story 3

- [X] T008 [US3] 创建 `templates/commands/designspec.md`，包含 YAML frontmatter（description、handoffs 到 designspec.eval、scripts）和完整的 AI 执行指令（读取 spec.md + plan.md + function-spec.md（可选）+ 模板、生成完整内容、软警告逻辑）
- [X] T009 [US3] 创建 `.claude/commands/speckit.designspec.md`，内容与 `templates/commands/designspec.md` 相同

**Checkpoint**: `/speckit.designspec` 可独立运行并生成完整的 design-spec.md

---

## Phase 6: User Story 4 - Evaluate Design Spec (Priority: P4)

**Goal**: 提供 `/speckit.designspec.eval` 命令，输出评分报告并保存到 design-spec-eval.md

**Independent Test**: 运行 `/speckit.designspec.eval`，检查控制台输出和 `design-spec-eval.md` 是否包含总分和具体建议

### Implementation for User Story 4

- [X] T010 [US4] 创建 `templates/commands/designspec.eval.md`，包含 YAML frontmatter（description、handoffs 到 speckit.tasks、scripts）和完整的评估执行指令（读取 design-spec.md + 模板、按章节评分、保存到 design-spec-eval.md、硬阻断逻辑）
- [X] T011 [US4] 创建 `.claude/commands/speckit.designspec.eval.md`，内容与 `templates/commands/designspec.eval.md` 相同

**Checkpoint**: `/speckit.designspec.eval` 可独立运行并输出完整评估报告

---

## Phase 7: User Story 5 - Generate Test Spec (Priority: P5)

**Goal**: 提供 `/speckit.testspec` 命令，AI 根据 spec.md 和 tasks.md 生成完整的 test-spec.md

**Independent Test**: 运行 `/speckit.testspec`，检查生成的 `test-spec.md` 是否覆盖 `templates/test-spec-template.md` 的所有章节，包含功能测试用例列表

### Implementation for User Story 5

- [X] T012 [US5] 创建 `templates/commands/testspec.md`，包含 YAML frontmatter（description、handoffs 到 testspec.eval、scripts）和完整的 AI 执行指令（读取 spec.md + tasks.md + function-spec.md（可选）+ 模板、生成完整内容、软警告逻辑）
- [X] T013 [US5] 创建 `.claude/commands/speckit.testspec.md`，内容与 `templates/commands/testspec.md` 相同

**Checkpoint**: `/speckit.testspec` 可独立运行并生成完整的 test-spec.md

---

## Phase 8: User Story 6 - Evaluate Test Spec (Priority: P6)

**Goal**: 提供 `/speckit.testspec.eval` 命令，输出评分报告并保存到 test-spec-eval.md

**Independent Test**: 运行 `/speckit.testspec.eval`，检查控制台输出和 `test-spec-eval.md` 是否包含总分、测试覆盖率评估和具体建议

### Implementation for User Story 6

- [X] T014 [US6] 创建 `templates/commands/testspec.eval.md`，包含 YAML frontmatter（description、无 handoffs、scripts）和完整的评估执行指令（读取 test-spec.md + 模板、按章节评分、保存到 test-spec-eval.md、硬阻断逻辑）
- [X] T015 [US6] 创建 `.claude/commands/speckit.testspec.eval.md`，内容与 `templates/commands/testspec.eval.md` 相同

**Checkpoint**: `/speckit.testspec.eval` 可独立运行并输出完整评估报告

---

## Phase 9: Polish & Cross-Cutting Concerns

**Purpose**: 确保所有命令一致性和工作流集成

- [X] T016 [P] 验证所有 6 个 `templates/commands/` 文件的 frontmatter 格式与现有命令一致（对比 `templates/commands/clarify.md`）
- [X] T017 [P] 验证所有 6 个 `.claude/commands/speckit.*.md` 文件的命名和内容与对应 templates/ 文件一致
- [X] T018 验证完整工作流链：funcspec → funcspec.eval → plan → designspec → designspec.eval → tasks → testspec → testspec.eval 的 handoffs 链正确连接
- [X] T019 [P] 按照 `quickstart.md` 中的工作流说明，端到端验证新命令在现有 speckit 工作流中的集成

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: 无依赖，立即开始
- **Foundational (Phase 2)**: 依赖 Phase 1，但本特性无实质性基础设施
- **User Stories (Phase 3–8)**: 依赖 Phase 1 完成；各故事之间无强依赖，可并行
- **Polish (Phase 9)**: 依赖所有用户故事完成

### User Story Dependencies

- **US1 (P1)**: 无依赖，可立即开始
- **US2 (P2)**: 无依赖，可与 US1 并行（不同文件）
- **US3 (P3)**: 无依赖，可与 US1/US2 并行
- **US4 (P4)**: 无依赖，可与其他故事并行
- **US5 (P5)**: 无依赖，可与其他故事并行
- **US6 (P6)**: 无依赖，可与其他故事并行

### Within Each User Story

- `templates/commands/` 文件先于 `.claude/commands/` 文件（后者复制前者内容）
- 每个故事的两个任务顺序执行（T004 → T005，T006 → T007，以此类推）

### Parallel Opportunities

- Phase 1 的 T002、T003 可并行
- Phase 3–8 的所有故事可并行（不同文件，无冲突）
- Phase 9 的 T016、T017、T019 可并行

---

## Parallel Example: User Stories 1–6

```bash
# 所有用户故事可并行实现（不同文件，无冲突）：
Task: "T004+T005: funcspec 命令文件"
Task: "T006+T007: funcspec.eval 命令文件"
Task: "T008+T009: designspec 命令文件"
Task: "T010+T011: designspec.eval 命令文件"
Task: "T012+T013: testspec 命令文件"
Task: "T014+T015: testspec.eval 命令文件"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. 完成 Phase 1: Setup（验证模板文件）
2. 完成 Phase 3: User Story 1（创建 funcspec 命令）
3. **STOP and VALIDATE**: 运行 `/speckit.funcspec` 验证生成结果
4. 如果通过，继续后续故事

### Incremental Delivery

1. Phase 1 → Phase 3 (US1) → 验证 → Phase 4 (US2) → 验证
2. Phase 5 (US3) → 验证 → Phase 6 (US4) → 验证
3. Phase 7 (US5) → 验证 → Phase 8 (US6) → 验证
4. Phase 9: 端到端集成验证

### Parallel Team Strategy

所有 6 个用户故事可由不同开发者并行实现，因为每个故事操作不同文件，无冲突。

---

## Notes

- [P] 任务 = 不同文件，无依赖，可并行
- 每个用户故事仅 2 个任务（templates/ 文件 + .claude/ 文件），极低复杂度
- 每个命令文件的核心价值在于 Markdown 执行指令的质量，需参考现有命令（如 `clarify.md`、`plan.md`）的写作风格
- 评估命令的评分逻辑完全在 Markdown 指令中描述，由 AI 执行，无需代码
- 完成后验证：`ls templates/commands/` 应有 6 个新文件，`ls .claude/commands/` 应有 6 个新 `speckit.*` 文件
