# Research: Extend Speckit Workflow Commands

**Feature**: 001-extend-spec-commands | **Date**: 2026-02-26

## Summary

所有技术决策均通过检查现有命令文件和项目结构得出，无外部依赖需要研究。

## Findings

见 `plan.md` Phase 0 Research 章节，包含 5 个决策：

1. **命令文件结构**: 复用现有 YAML frontmatter 格式
2. **前置条件检查**: 复用 `check-prerequisites.sh --json --paths-only`，AI 逻辑处理文件存在性
3. **评估维度权重**: 章节完整性 40% + 内容质量 60%
4. **目录关系**: `templates/commands/`（权威）→ `.claude/commands/`（Claude 副本）
5. **Handoffs 链**: 生成命令 → eval 命令 → 工作流下一步

## No Unknowns Remaining

所有 NEEDS CLARIFICATION 项已在 `/speckit.clarify` 阶段解决，记录在 `spec.md` 的 Clarifications 章节。
