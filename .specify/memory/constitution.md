<!--
Sync Impact Report

- 版本变更: 模板（未定版） → 0.1.0
- 原则变更:
  - 原则 1 占位符 → 一、MVP 优先与范围锁定
  - 原则 2 占位符 → 二、技术栈一致性（Flutter + FastAPI）
  - 原则 3 占位符 → 三、先确认需求再实施（非协商）
  - 原则 4 占位符 → 四、中文交付与规范化提交
  - 原则 5 占位符 → 五、可读性与最小复杂度
- 新增章节: Sync Impact Report（本段）
- 删除章节: 无
- 模板同步:
  - ✅ `.specify/templates/plan-template.md`
  - ✅ `.specify/templates/spec-template.md`
  - ✅ `.specify/templates/tasks-template.md`
  - ✅ `.specify/templates/checklist-template.md`
  - ✅ `.specify/templates/agent-file-template.md`
- 后续 TODO: 无
-->

# FluentRead Constitution

## Core Principles

### 一、MVP 优先与范围锁定

- 只实现 `docs/hackathon_mvp_prd.md` 明确标注的 MVP Happy Path 与范围内功能。
- PRD 的 Non-goals 一律不做；如需纳入，必须先与用户确认并更新计划。
- 任何“顺手优化/体验增强/架构升级”都必须先说明收益与代价并获得确认。

Rationale: MVP 以可演示的完整主流程为目标，避免范围膨胀导致无法交付。

### 二、技术栈一致性（Flutter + FastAPI）

- 移动端仅使用 Flutter（MVP 仅需 Android 可运行）；遵循 Flutter/Dart 社区通用工程规范。
- 后端使用 FastAPI（Python）+ SQLAlchemy；数据库使用 Supabase Postgres（按 PRD）。
- 后端依赖管理必须使用 `uv`；禁止在后端引入 `pipenv`/`poetry` 等并行方案。
- 除非有明确且已确认的必要性，不新增关键基础设施与框架（例如消息队列、微服务拆分）。

Rationale: 统一技术栈能显著降低集成成本，让团队在有限时间内稳定交付。

### 三、先确认需求再实施（非协商）

- 在实施任何变更前，必须先用中文明确需求边界，并列出可执行的实施计划，等待用户确认后再动手。
- 过程中若发现需求歧义或需要取舍，必须暂停实现并向用户确认决策点。
- 需求确认与计划更新本身也属于交付的一部分，必须可追溯（记录在文档或 PR 描述中）。

Rationale: 降低返工与误解成本，确保每一次改动都与目标一致。

### 四、中文交付与规范化提交

- 文档、PR 描述、commit message 均使用中文。
- commit message 必须使用约定前缀（示例：`feat:`、`fix:`、`docs:`、`refactor:`、`chore:`、`test:`），冒号后为中文描述。
- 代码注释优先使用中文，必要时可保留英文术语以避免歧义。
- 命名规则：产品名固定为 `FluentRead`；当工具/生态要求时使用小写命名（例如 Dart/Python 包名可用 `fluent_read`）。

Rationale: 降低团队沟通成本，统一交付物风格，便于审核与回溯。

### 五、可读性与最小复杂度

- 代码必须优先可读：清晰命名、单一职责、小函数；不同逻辑块之间用空行分隔。
- 关键业务逻辑、边界条件与易错点必须补充中文注释；避免“解释语法”的无效注释。
- MVP 也必须具备可用性底线：清晰的加载/失败状态、必要的错误处理、避免静默失败。
- 遵循“先简单后演进”：禁止为未来不确定需求做过度抽象（YAGNI）。

Rationale: 可读、可维护、可演示的实现，比“看起来高级”的架构更重要。

## 技术栈与基础约束

- 前端：Flutter（Android MVP）。
- 后端：FastAPI + SQLAlchemy + Supabase Postgres；按 PRD 对接 Supabase Auth。
- AI 能力（按 PRD）：LLM 兼容 OpenAI 风格接口，用于文章简化与词义生成。
- 隐私与安全底线：不得在日志/PR/截图中泄露验证码、访问令牌、密钥与用户隐私数据。
- 配置与密钥：通过环境变量管理；禁止将真实密钥提交到仓库。

## 开发工作流与质量门禁

- 分支粒度：一个功能一个分支，分支名建议使用 `###-short-name`（三位编号 + 短名，小写连字符）。
- 提交与合并：功能完成后通过 PR 合并到主线，合并方式使用 squash merge，主线保持一条清晰提交记录。
- 评审门禁（每个 PR 必须自检并在 PR 描述中说明）：
  - 是否严格在 MVP 范围内（引用 PRD 条目）。
  - 是否符合技术栈约束（Flutter/FastAPI；后端使用 `uv`）。
  - 是否遵循中文交付规范（PR/commit/文档中文，commit 前缀规范）。
  - 是否具备可用性底线（加载/失败状态、必要错误处理）。
  - 是否提供可复现的验证方式（至少包含手动验收步骤；自动化测试按需求取舍）。
- 文档要求：每个功能至少更新相应的说明（spec/plan/tasks 或 docs），并保持中文表述。

## Governance

- 本章程对仓库内所有实践具有最高优先级；与其他文档冲突时，以本章程为准并同步修订冲突文档。
- 修订流程：任何修改本章程的变更必须通过 PR 提交，说明修改动机、影响范围与迁移/落地方式。
- 版本策略：本章程使用语义化版本（SemVer）。
  - MAJOR：删除原则、显著改变治理/工作流（会导致既有流程不兼容）。
  - MINOR：新增原则/章节，或对现有原则做实质扩展（增加新的强制约束）。
  - PATCH：措辞澄清、示例补充、错别字修正（不改变约束语义）。
- 合规检查：每个实现计划必须包含“章程检查”，每个 PR 必须显式声明已逐条检查通过或给出例外理由。

**Version**: 0.1.0 | **Ratified**: 2025-12-22 | **Last Amended**: 2025-12-22
