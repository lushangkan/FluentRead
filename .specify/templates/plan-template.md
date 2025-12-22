# 实施计划：[FEATURE]

**分支**: `[###-feature-name]` | **日期**: [DATE] | **规格**: [link]
**输入**: 来自 `/specs/[###-feature-name]/spec.md` 的功能规格说明

**说明**: 该模板通常由 `.specify/scripts/bash/setup-plan.sh` 复制到 `specs/.../plan.md` 作为起始文件。

## 概要

[从 spec.md 提取：主要需求 +（如有）研究结论中的技术方案]

## 技术背景

<!--
  需要你填写：将本节替换为本功能的技术上下文。
  注意：本项目章程要求“先确认需求与计划，再开始实施”。
-->

**Language/Version**: [e.g., Python 3.11 or NEEDS CLARIFICATION]

**Primary Dependencies**: [e.g., FastAPI or NEEDS CLARIFICATION]

**Storage**: [if applicable, e.g., PostgreSQL, files or N/A]

**Testing**: [e.g., pytest or NEEDS CLARIFICATION]

**Target Platform**: [e.g., Linux server, Android or NEEDS CLARIFICATION]

**Project Type**: [single/web/mobile - determines source structure]

**Performance Goals**: [domain-specific, e.g., 1000 req/s, 60 fps or NEEDS CLARIFICATION]

**Constraints**: [domain-specific, e.g., <200ms p95, <100MB memory or NEEDS CLARIFICATION]

**Scale/Scope**: [domain-specific, e.g., 10k users, 50 screens or NEEDS CLARIFICATION]

## Constitution Check

*门禁：Phase 0 研究前必须通过；Phase 1 设计后需要复核。*

- 是否严格在 MVP 范围内（明确列出不做项）。
- 是否符合技术栈约束（Flutter/FastAPI；后端使用 `uv`）。
- 是否遵循中文交付规范（文档/PR/commit 中文，commit 前缀规范）。
- 是否按“先确认需求与计划，再开始实施”的流程执行。
- 是否保持实现最小复杂度与可读性（空行分隔、中文注释、必要错误处理）。

## 项目结构

### 文档（本功能）

```text
specs/[###-feature]/
├── plan.md              # 本文件（工具输出）
├── research.md          # Phase 0 输出
├── data-model.md        # Phase 1 输出
├── quickstart.md        # Phase 1 输出
├── contracts/           # Phase 1 输出
└── tasks.md             # Phase 2 输出（通常由 tasks 工具生成）
```

### 源码（仓库根目录）
<!--
  需要你填写：将占位树替换为本功能的真实目录结构。
  删除未使用的选项，并将所选结构扩展为真实路径。
-->

```text
# [未使用则删除] 选项 1：单体项目（默认）
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# [未使用则删除] 选项 2：Web 应用（检测到“frontend + backend”时）
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# [未使用则删除] 选项 3：移动端 + API（检测到“iOS/Android”时）
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure: feature modules, UI flows, platform tests]
```

**结构选择**: [记录所选结构，并引用上面真实目录]

## 复杂度跟踪

> 仅当“章程检查”存在例外时填写，并说明理由与替代方案

| 例外项 | 为什么必须这样做 | 为什么不能用更简单方案 |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |
