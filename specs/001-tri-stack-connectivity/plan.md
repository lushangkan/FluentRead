# 实施计划：三端互联互通骨架（连通性与健康检查）

**分支**: `001-tri-stack-connectivity` | **日期**: 2025-12-22 | **规格**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/spec.md`

## 概要

本计划实现一个“可互联互通的三端基础骨架”，聚焦连通性与健康检查，提供：

- 安卓端（Flutter）一键触发“移动端 → 后端”连通性检查，并展示：可达/不可达/超时、检查时间、健康摘要、失败归因与下一步建议（FR-001/FR-004）。
- 后端（FastAPI）提供匿名可访问的健康入口，返回后端自身状态 + 外部依赖状态明细；本期外部依赖列表必须包含数据库（Supabase Postgres），并通过 ORM 执行 `SELECT 1` 以确保 ORM 配置正确（FR-002/FR-003/FR-005/FR-006/FR-007）。
- 开发期后端地址由手动配置完成，不做应用内环境切换或编辑入口（FR-008）。

不做（明确锁定范围）：

- 不做 Supabase Auth 登录流程、不做数据库表/业务数据、不做 RSS/文章/词表/LLM 业务能力（均在 `docs/hackathon_mvp_prd.md` 的后续功能中）。
- 说明：本期会连接数据库用于健康检查（ORM `SELECT 1` 验证链路），但不新增任何业务表与数据模型。
- 不做 iOS/Web、CI/CD、灰度、复杂监控与告警体系（仅做最小可用健康检查）。

## 技术背景

**Language/Version**: Flutter stable（建议 3.22+）/ Dart 3.x；Python 3.13（建议）

**Primary Dependencies**: Flutter（`dio` + `--dart-define`/`flutter_dotenv`）；FastAPI（Uvicorn/Pydantic v2/httpx/SQLAlchemy）；后端依赖管理 `uv`

**Storage**: 不新增业务表；但必须配置 `DATABASE_URL`，并通过 SQLAlchemy ORM 执行 `SELECT 1` 作为健康检查的一部分

**Testing**: pytest（后端）+ `flutter_test`（移动端）

**Target Platform**: Android（真机/模拟器）+ 本地开发后端（跨平台）

**Project Type**: 移动端 + API（Flutter App + FastAPI）

**Performance Goals**: 正常网络下，90% 情况下“点按钮 → 展示结果”不超过 3 秒（SC-002）

**Constraints**: 匿名 `/health`（FR-006）且不泄露敏感信息；失败可定位（FR-003/FR-004）；依赖仅做连通性探测（FR-007）；数据库连通性检查必须通过 ORM 执行 `SELECT 1`，并且项目数据操作统一使用 ORM

**Scale/Scope**: 1 个移动端页面 + 1 个后端健康入口 + 最小配置与验收文档

## 你需要提供什么（环境变量/配置）

为保证“可联调、可验收”，建议最少准备以下配置项（开发期用 `.env`/`--env-file` 管理，真实值禁止提交）：

**移动端（Flutter）**

- `API_BASE_URL`：后端基地址（Android 模拟器常用 `http://10.0.2.2:8000`；真机需同网段可达地址）
- （可选）`SUPABASE_URL`、`SUPABASE_ANON_KEY`：后续登录功能会用到；本功能不强制

**后端（FastAPI）**

- `APP_ENV`：`local`/`dev`/`prod`
- `CORS_ALLOW_ORIGINS`：开发期允许移动端/调试工具访问的 origin（或开发期放宽到 `*`，但必须仅限 local/dev）
- 外部依赖连通性探测（至少 1 个；本期固定包含数据库）：
  - 必需：`DATABASE_URL`（Supabase Postgres 连接串；通过 SQLAlchemy ORM 执行 `SELECT 1`；属于敏感信息）
  - 可选：`SUPABASE_URL`（用于 HTTP/TCP 可达性探测；用于更丰富的健康摘要展示）

## Constitution Check（门禁）

*门禁：Phase 0 研究前必须通过；Phase 1 设计后需要复核。*

- ✅ 严格在 MVP 范围内：本期仅“连通性 + 健康检查”，明确不做登录/RSS/LLM 业务功能。
- ✅ 技术栈一致：Flutter + FastAPI +（可选）Supabase 作为外部依赖；后端依赖管理使用 `uv`。
- ✅ 中文交付：本计划及输出文档均使用中文；后续 commit message 按约定前缀。
- ✅ 先计划后实施：本 PR/变更先产出 `plan.md`/`research.md`/`contracts` 等，再进入编码。
- ✅ 最小复杂度：只做必要的端到端链路与可定位错误，不引入额外基础设施。

## 项目结构

### 文档（本功能）

```text
specs/001-tri-stack-connectivity/
├── spec.md
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
└── contracts/
    └── openapi.yaml
```

### 源码（仓库根目录，计划创建）

```text
/home/zara/Documents/Project/fluent-read/
├── api/                 # FastAPI 项目根（uv + src 布局）
│   ├── pyproject.toml
│   └── src/
│       └── fluent_read_api/
│           ├── main.py
│           ├── api/
│           │   └── health.py
│           └── core/
│               ├── config.py
│               └── logging.py
└── app/                 # Flutter 项目根（Android MVP）
    ├── pubspec.yaml
    └── lib/
        ├── app.dart
        ├── core/
        │   ├── config.dart
        │   └── http_client.dart
        └── features/
            └── connectivity_check/
                ├── connectivity_check_page.dart
                ├── connectivity_check_controller.dart
                └── models.dart
```

**结构选择**: 移动端 + API（Flutter App + FastAPI），并在 `specs/001-tri-stack-connectivity/contracts/openapi.yaml` 定义两端契约。

## 复杂度跟踪

本功能无章程例外项。

## Phase 0：研究结论（输出 research.md）

- 明确健康检查的“依赖项选择、探测方式、状态枚举、错误归因映射”
- 明确移动端如何配置后端地址（开发期手动配置）
- 明确契约（OpenAPI）与 quickstart 的最小可跑通步骤

## Phase 1：设计与契约（输出 data-model.md / contracts / quickstart.md）

- 设计健康接口的数据结构（`HealthReport`/`DependencyStatus`）
- 产出 OpenAPI 契约并锁定字段含义，保证移动端与后端一致
- 编写 quickstart（本地跑通 + 常见故障演示）

## Constitution Check 复核（Phase 1 后）

- ✅ 仍在 MVP 范围内：仅定义健康契约与最小跑通方式，不引入业务功能。
- ✅ 技术栈不偏离：契约与数据结构均围绕 Flutter/FastAPI/Supabase（可选）约束设计。
- ✅ 交付物中文化：`plan.md`/`research.md`/`data-model.md`/`quickstart.md`/`openapi.yaml` 均为中文说明。
- ✅ 最小复杂度：依赖探测限定为连通性；错误归因通过 `error_code/action_hint` 稳定输出。

## Phase 2：实施拆解（仅规划，不在本阶段写代码）

建议按 2 条端到端链路切片：

1. 后端 `/health` 先返回固定结构（不做真实探测）+ 移动端展示（跑通链路）
2. 接入至少 1 个外部依赖真实探测 + 错误归因与建议（覆盖 P2“失败可定位”）
   - 本期外部依赖至少包含：数据库（基于 `DATABASE_URL`，ORM 执行 `SELECT 1`）
