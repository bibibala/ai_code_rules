---
name: 'spec-workflow'
description: '前端场景下的结构化需求→设计→任务拆分工作流。做前端新功能、前端复杂改造、多前端模块集成、涉及页面/组件/交互设计时调用。'
---

# Spec Workflow

## 何时使用

在需要结构化研发流程时使用本技能，例如：

- 前端新功能从 0 到 1
- 前端复杂页面、复杂交互或前端架构重构
- 多前端模块集成
- 涉及页面、组件、交互或前端信息架构设计
- 需要更高质量的前端需求分析与验收标准

不适用于：

- 简单 bug 修复
- 文档更新
- 配置改动
- 纯重构（除非用户明确要求走流程）
- 后端服务设计
- 数据库设计
- 非前端业务流程建模

补充说明：

- 本技能是“用户明确希望按 spec 流程推进”时才启用的工作流。
- 如果用户只是要直接实现功能，不应强制要求先走完整四阶段文档流。
- 本技能只用于前端研发流程，不应用来约束后端或数据库方案设计。

## 使用方式（对编码助手）

1. 严格按阶段推进
   - 每个阶段都必须完成并由用户确认后，才能进入下一阶段
   - 不允许跳过阶段
   - 需要澄清信息时，使用当前编码助手可用的提问/确认机制；不要依赖固定平台专属工具名

2. 需求描述使用 EARS
   - 格式：`While <可选前置条件>, when <可选触发>, the <system name> shall <system response>`

3. 涉及 UI 时联动 UI 设计技能
   - 当需求包含前端页面/组件/交互时，必须调用 `ui-design` 技能先产出设计规格（Design Specification）

4. 任务跟踪
   - 执行阶段需要持续更新 `tasks.md` 的状态（若当前工作流要求）

---

## Phase 1：需求与验收标准

- 使用 EARS 输出需求与验收标准
- 若涉及 UI：先调用 `ui-design` 技能确定风格、配色、字体、布局策略
- 产物保存：`specs/spec_name/requirements.md`
- 必须让用户确认后再进入下一阶段

参考格式：

```markdown
# Requirements Document

## Introduction

Requirement description

## Requirements

### Requirement 1 - Requirement Name

**User Story:** User story content

#### Acceptance Criteria

1. While <optional precondition>, when <optional trigger>, the <system name> shall <system response>.
2. ...
```

## Phase 2：技术方案设计

- 基于已确认的需求，输出前端技术方案：前端架构、技术选型、接口对接方式、状态管理、测试策略、安全性
- 必要时用 mermaid 图
- 产物保存：`specs/spec_name/design.md`
- 必须让用户确认后再进入下一阶段

## Phase 3：任务拆解

- 基于需求与技术方案拆解可执行任务
- 产物保存：`specs/spec_name/tasks.md`
- 必须让用户确认后再进入下一阶段

参考格式：

```markdown
# Implementation Plan

- [ ] 1. Task Information
  - Specific things to do
  - ...
  - \_Requirement: Related requirement point number
```

## Phase 4：任务执行

- 开始正式开发
- 持续更新 `tasks.md`（如工作流要求）
- 完成后标记任务已完成
