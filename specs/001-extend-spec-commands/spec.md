# Feature Specification: Extend Speckit Workflow with Spec Generation and Evaluation Commands

**Feature Branch**: `001-extend-spec-commands`
**Created**: 2026-02-26
**Status**: Draft
**Input**: User description: "扩展现有工作流，新增 function spec、design spec、test spec 的生成与评估命令"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Generate Function Spec After Clarify (Priority: P1)

开发者在完成 `/speckit.clarify` 后，运行 `/speckit.funcspec` 命令，系统根据已有的 spec.md 和澄清结果，按照 `templates/function-spec-template.md` 的结构自动生成 function spec 文档，保存到当前 feature 目录。

**Why this priority**: Function spec 是连接需求规格与技术设计的关键文档，是后续 design spec 和 test spec 的基础，优先级最高。

**Independent Test**: 可通过单独运行 `/speckit.funcspec` 并检查生成的文档是否符合模板结构来独立验证，交付物为完整的 function spec 文件。

**Acceptance Scenarios**:

1. **Given** 当前 feature 目录存在 spec.md 且已完成 clarify，**When** 运行 `/speckit.funcspec`，**Then** 在 feature 目录生成 `function-spec.md`，内容覆盖模板所有必填章节
2. **Given** spec.md 不存在，**When** 运行 `/speckit.funcspec`，**Then** 命令报错并提示用户先运行 `/speckit.specify`
3. **Given** function-spec.md 已存在，**When** 再次运行 `/speckit.funcspec`，**Then** 提示用户确认是否覆盖

---

### User Story 2 - Evaluate Function Spec (Priority: P2)

开发者在生成 function spec 后，运行 `/speckit.funcspec.eval` 命令，系统对 function-spec.md 进行质量评估，输出 0-100 的评分和具体的修改建议。

**Why this priority**: 评估命令确保生成的文档达到质量标准，避免低质量文档流入后续流程。

**Independent Test**: 可通过提供一份已知质量的 function spec 文件，验证评分和建议是否准确反映文档质量。

**Acceptance Scenarios**:

1. **Given** function-spec.md 存在，**When** 运行 `/speckit.funcspec.eval`，**Then** 输出总分（0-100）、各维度得分和至少 3 条具体修改建议
2. **Given** function-spec.md 不存在，**When** 运行 `/speckit.funcspec.eval`，**Then** 报错提示先运行 `/speckit.funcspec`
3. **Given** function spec 评分低于 60，**When** 评估完成，**Then** 明确标注"需要重大修订"并列出关键缺失项

---

### User Story 3 - Generate Design Spec After Plan (Priority: P3)

开发者在完成 `/speckit.plan` 后，运行 `/speckit.designspec` 命令，系统根据 spec.md、plan.md 和 function-spec.md，按照 `templates/design-spec-template.md` 生成 design spec 文档。

**Why this priority**: Design spec 依赖 plan 阶段的产出，是技术实现的蓝图，优先级次于 function spec。

**Independent Test**: 可通过检查生成的 design-spec.md 是否包含架构描述、接口规范等核心章节来独立验证。

**Acceptance Scenarios**:

1. **Given** spec.md 和 plan.md 存在，**When** 运行 `/speckit.designspec`，**Then** 生成 `design-spec.md`，覆盖模板所有必填章节
2. **Given** plan.md 不存在，**When** 运行 `/speckit.designspec`，**Then** 报错提示先运行 `/speckit.plan`

---

### User Story 4 - Evaluate Design Spec (Priority: P4)

开发者在生成 design spec 后，运行 `/speckit.designspec.eval` 命令，系统对 design-spec.md 进行质量评估，输出评分和修改建议。

**Why this priority**: 确保设计文档在进入实现阶段前达到质量标准。

**Independent Test**: 可通过提供一份已知质量的 design spec，验证评分准确性。

**Acceptance Scenarios**:

1. **Given** design-spec.md 存在，**When** 运行 `/speckit.designspec.eval`，**Then** 输出总分、各维度得分和具体修改建议
2. **Given** design-spec.md 不存在，**When** 运行 `/speckit.designspec.eval`，**Then** 报错提示先运行 `/speckit.designspec`

---

### User Story 5 - Generate Test Spec After Implement (Priority: P5)

开发者在完成 `/speckit.implement` 后，运行 `/speckit.testspec` 命令，系统根据 spec.md、function-spec.md 和实现产出，按照 `templates/test-spec-template.md` 生成 test spec 文档。

**Why this priority**: Test spec 在实现完成后生成，确保测试覆盖实际实现的功能。

**Independent Test**: 可通过检查生成的 test-spec.md 是否包含功能测试用例列表来独立验证。

**Acceptance Scenarios**:

1. **Given** spec.md 和 tasks.md 存在，**When** 运行 `/speckit.testspec`，**Then** 生成 `test-spec.md`，包含功能测试用例列表
2. **Given** tasks.md 不存在，**When** 运行 `/speckit.testspec`，**Then** 报错提示先运行 `/speckit.tasks`

---

### User Story 6 - Evaluate Test Spec (Priority: P6)

开发者在生成 test spec 后，运行 `/speckit.testspec.eval` 命令，系统对 test-spec.md 进行质量评估，输出评分和修改建议。

**Why this priority**: 确保测试文档覆盖所有功能需求，避免测试遗漏。

**Independent Test**: 可通过检查评估报告是否指出测试覆盖率不足的具体功能点来独立验证。

**Acceptance Scenarios**:

1. **Given** test-spec.md 存在，**When** 运行 `/speckit.testspec.eval`，**Then** 输出总分、测试覆盖率评估和具体修改建议
2. **Given** test-spec.md 不存在，**When** 运行 `/speckit.testspec.eval`，**Then** 报错提示先运行 `/speckit.testspec`

---

### Edge Cases

- 当前 feature 目录不存在时，所有命令应报错退出（硬阻断），提示用户先运行 `/speckit.specify`\n- 前置依赖文件缺失时（如 funcspec 运行时 spec.md 不存在），发出软警告并等待用户确认是否继续
- 模板文件缺失时，生成命令应报错并提示具体缺失的模板路径
- 已存在的 spec 文件被手动修改后，eval 命令仍应正常评估
- 生成的 spec 内容为空或极短时，eval 命令应给出最低分并说明原因
- 多次运行生成命令时，应提示用户确认是否覆盖已有文件

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: 系统 MUST 提供 `/speckit.funcspec` 命令，在 clarify 完成后，根据 spec.md 和 `templates/function-spec-template.md`，由 AI 生成完整内容填充所有章节，输出 `function-spec.md`
- **FR-002**: 系统 MUST 提供 `/speckit.funcspec.eval` 命令，对 function-spec.md 进行质量评估，输出 0-100 评分和至少 3 条具体修改建议，结果同时打印到控制台并保存到 `function-spec-eval.md`
- **FR-003**: 系统 MUST 提供 `/speckit.designspec` 命令，在 plan 完成后，根据 spec.md、plan.md 和 `templates/design-spec-template.md`，由 AI 生成完整内容填充所有章节，输出 `design-spec.md`
- **FR-004**: 系统 MUST 提供 `/speckit.designspec.eval` 命令，对 design-spec.md 进行质量评估，输出评分和修改建议，结果同时打印到控制台并保存到 `design-spec-eval.md`
- **FR-005**: 系统 MUST 提供 `/speckit.testspec` 命令，在 implement 完成后，根据 spec.md、tasks.md 和 `templates/test-spec-template.md`，由 AI 生成完整内容填充所有章节，输出 `test-spec.md`
- **FR-006**: 系统 MUST 提供 `/speckit.testspec.eval` 命令，对 test-spec.md 进行质量评估，输出评分和修改建议，结果同时打印到控制台并保存到 `test-spec-eval.md`
- **FR-007**: 所有生成命令 MUST 在目标文件已存在时提示用户确认是否覆盖
- **FR-008**: 所有生成命令 MUST 在前置依赖文件缺失时发出软警告（说明缺失文件和建议的前置命令），并等待用户确认是否继续；用户确认后可继续执行，不强制阻断
- **FR-009**: 所有评估命令 MUST 按模板章节结构评分，分两个维度：（1）章节完整性——必填章节是否全部存在；（2）内容质量——各章节内容是否充实、具体、无占位符；两个维度加权汇总为总分（0-100）
- **FR-010**: 评估命令 MUST 将评分结果分为三个等级：≥80（可接受）、60-79（需要修订）、<60（需要重大修订）
- **FR-011**: 所有新命令 MUST 作为独立的 `.claude/commands/speckit.*.md` 文件实现，与现有命令保持一致的结构

### Key Entities

- **Function Spec**: 功能规格文档，描述特性的行为细节、接口和约束，文件名 `function-spec.md`
- **Design Spec**: 设计规格文档，描述技术架构、组件设计和实现方案，文件名 `design-spec.md`
- **Test Spec**: 测试规格文档，描述测试目标、测试用例和验收标准，文件名 `test-spec.md`
- **Evaluation Report**: 评估报告，包含总分、各维度得分和具体修改建议，内嵌在命令输出中

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 每个生成命令在 30 秒内完成，输出的文档覆盖对应模板的所有必填章节
- **SC-002**: 每个评估命令输出的评分与人工评审结果偏差不超过 15 分（基于同一份文档）
- **SC-003**: 评估命令对每份文档至少给出 3 条具体、可操作的修改建议（非泛泛而谈）
- **SC-004**: 新命令与现有工作流无缝集成，不破坏任何现有命令的功能
- **SC-005**: 90% 的用户能够在不查阅文档的情况下，通过命令输出的提示完成完整工作流

## Clarifications

### Session 2026-02-26

- Q: 生成命令的生成策略是什么？ → A: AI 根据上下文（spec.md、plan.md 等）生成完整内容，填充所有章节
- Q: eval 命令的评估维度如何定义？ → A: 基于模板章节结构，评估章节完整性（必填章节是否覆盖）和各章节内容质量（是否充实、具体、无占位符）
- Q: eval 命令的评估结果是否需要持久化？ → A: 打印到控制台，同时保存到对应的评估报告文件（如 function-spec-eval.md）
- Q: 生成命令在前置条件未满足时如何处理？ → A: 软警告，提示缺失文件和建议的前置命令，等待用户确认后继续，不强制阻断
- Q: 新命令的命名规范是什么？ → A: 保持草案命名，使用 speckit.funcspec / speckit.funcspec.eval 等形式，与现有命令风格一致

## Assumptions

- 现有工作流（specify → clarify → plan → analyze → tasks → implement）保持不变，新命令插入其中
- `templates/` 目录下的三个模板文件是权威来源，评估标准直接从模板章节结构派生
- 评分采用 0-100 分制，各章节按权重加权计算总分
- 新命令的实现方式与现有命令一致，均为 `.claude/commands/` 目录下的 Markdown 文件
- function-spec.md、design-spec.md、test-spec.md 均保存在当前 feature 目录（与 spec.md 同级）
