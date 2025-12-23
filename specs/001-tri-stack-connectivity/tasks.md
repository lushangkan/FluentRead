# 任务清单：三端互联互通骨架（连通性与健康检查）

**输入**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/` 下的设计与规格文档  
**计划**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/plan.md`  
**规格**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/spec.md`  
**研究**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/research.md`  
**数据结构**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/data-model.md`  
**契约**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/contracts/openapi.yaml`  
**验收步骤**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/quickstart.md`

## 格式：`- [ ] T### [P?] [US#?] 描述（含文件路径）`

- **[P]**：可并行（不同文件、无未完成依赖）
- **[US#]**：用户故事标签（仅出现在用户故事阶段：US1/US2/US3）
- 每条任务描述必须包含明确文件路径（例如 `api/src/...`、`app/lib/...`）

## 路径约定（本功能：移动端 + API）

- 后端（FastAPI）：`api/src/fluent_read_api/`
- 移动端（Flutter）：`app/lib/`

---

## Phase 1：初始化（项目骨架与最小可运行结构）

**目的**: 创建与 plan.md 一致的目录与工程骨架，为后续阶段提供落点。

- [ ] T001 创建后端工程骨架与依赖清单：`api/pyproject.toml`
- [ ] T002 [P] 创建后端包结构占位文件：`api/src/fluent_read_api/__init__.py`
- [ ] T003 [P] 创建后端应用入口占位：`api/src/fluent_read_api/main.py`
- [ ] T004 [P] 添加后端环境变量示例（禁止提交真实值）：`api/.env.example`
- [x] T005 创建移动端 Flutter 工程骨架：`app/pubspec.yaml`
- [x] T006 [P] 添加移动端入口与应用壳：`app/lib/main.dart`
- [ ] T007 [P] 创建移动端核心配置读取（`API_BASE_URL`）：`app/lib/core/config.dart`
- [ ] T008 [P] 创建移动端 HTTP 客户端封装（Dio 初始化）：`app/lib/core/http_client.dart`

---

## Phase 2：地基（阻塞性前置：可启动后端 + 固定结构 /health）

**目的**: 在任何用户故事开始前必须完成的核心基础设施（本阶段完成后，可用 curl 跑通 `/health` 的固定结构）。

**独立验收方式（本阶段）**:
- `uv run uvicorn ...` 能启动后端，且 `curl http://localhost:8000/health` 返回符合契约字段的 JSON（可先返回固定值）。

- [ ] T009 实现后端配置加载（`APP_ENV`/`CORS_ALLOW_ORIGINS`/`DATABASE_URL` 等）：`api/src/fluent_read_api/core/config.py`
- [ ] T010 [P] 实现后端日志初始化与敏感信息保护约束：`api/src/fluent_read_api/core/logging.py`
- [ ] T011 [P] 实现后端健康检查 Pydantic 模型（对齐 `openapi.yaml`）：`api/src/fluent_read_api/schemas/health.py`
- [ ] T012 [P] 实现后端请求标识（`request_id`）注入工具：`api/src/fluent_read_api/core/request_id.py`
- [ ] T013 实现 `/health` 路由（先返回固定结构，字段对齐契约）：`api/src/fluent_read_api/api/health.py`
- [ ] T014 将路由、CORS、错误响应挂载到应用入口：`api/src/fluent_read_api/main.py`

---

## Phase 3：用户故事 1 - 安卓端到端连通性自检（优先级：P1）🎯 MVP

**目标**: 安卓端一键触发检查，展示“可达/不可达/超时”、检查时间、健康摘要（FR-001/FR-004）。

**独立验收方式**:
- Android 模拟器/真机启动 App，点击“检查连接”，能够看到一次成功或失败结果；可重复执行（spec “可重复执行”）。

**独立测试标准（手工）**:
- 后端可达：展示 `reachable` + `checked_at` + `overall_status/summary`（来自 `/health`）
- 后端不可达：展示 `unreachable` 或 `timeout`，且允许重试

- [ ] T015 [P] [US1] 定义移动端 HealthReport/DependencyStatus 解析模型：`app/lib/features/connectivity_check/models.dart`
- [ ] T016 [P] [US1] 定义移动端连通性结果模型（reachability/checkedAt/healthReport）：`app/lib/features/connectivity_check/models.dart`
- [ ] T017 [US1] 实现连通性检查控制器（发起 `/health` 请求并更新状态）：`app/lib/features/connectivity_check/connectivity_check_controller.dart`
- [ ] T018 [US1] 实现“连通性检查”页面 UI（按钮/加载态/成功态/失败态）：`app/lib/features/connectivity_check/connectivity_check_page.dart`
- [ ] T019 [US1] 将页面接入应用入口并设为首页：`app/lib/app.dart`
- [ ] T020 [US1] 补齐移动端 `API_BASE_URL` 使用说明（dart-define 示例）：`app/README.md`

---

## Phase 4：用户故事 2 - 统一的后端健康状态入口（优先级：P1）

**目标**: 提供匿名 `GET /health`，返回后端自身状态 + 外部依赖状态明细；外部依赖必须包含数据库并通过 ORM 执行 `SELECT 1`（FR-002/FR-005/FR-006/FR-007/FR-009）。

**独立验收方式**:
- `curl http://localhost:8000/health` 可复制得到结构化 JSON，并能区分“后端自身正常但依赖异常”的降级状态。

**独立测试标准（手工）**:
- `DATABASE_URL` 正常：`dependencies` 中 `database.status=ok`，且 `overall_status=ok`
- `DATABASE_URL` 缺失或错误：`dependencies` 中 `database.status=unknown|error`（按约定），并给出 `error_code/action_hint`；整体状态符合规则

- [ ] T021 [P] [US2] 实现进程 uptime 计算工具（供 backend.uptime_s）：`api/src/fluent_read_api/core/uptime.py`
- [ ] T022 [P] [US2] 实现数据库健康探测（SQLAlchemy ORM `SELECT 1`）：`api/src/fluent_read_api/healthchecks/database.py`
- [ ] T023 [P] [US2] 实现可选 Supabase 可达性探测（基于 `SUPABASE_URL`）：`api/src/fluent_read_api/healthchecks/supabase.py`
- [ ] T024 [US2] 实现依赖探测编排（并发 + 分层超时，对齐 research 决策）：`api/src/fluent_read_api/healthchecks/runner.py`
- [ ] T025 [US2] 将 `/health` 从“固定结构”升级为“真实探测 + overall_status 判定 + summary 生成”：`api/src/fluent_read_api/api/health.py`
- [ ] T026 [US2] 为 `/health` 增加 `Cache-Control: no-store` 响应头：`api/src/fluent_read_api/api/health.py`

---

## Phase 5：用户故事 3 - 失败可定位（优先级：P2）

**目标**: 常见失败可归因并给出下一步建议；移动端与后端输出一致且可行动（FR-003/FR-004/SC-003）。

**独立验收方式**:
- 制造至少三类失败（网络不可用/地址配置错误/外部依赖异常），每次都能看到明确归因与建议（在移动端或 `/health` 中）。

**独立测试标准（手工）**:
- 数据库异常：`/health` 返回 `overall_status=degraded`，`database.error_code` 与 `action_hint` 不为空
- App 侧：展示失败类型与建议文案，并可重试

- [ ] T027 [P] [US3] 定义后端错误归因映射（`error_code` + `action_hint` 生成规则）：`api/src/fluent_read_api/healthchecks/errors.py`
- [ ] T028 [US3] 将数据库探测异常映射为稳定 `error_code`（config_missing/timeout/tcp_unreachable 等）：`api/src/fluent_read_api/healthchecks/database.py`
- [ ] T029 [US3] 将 HTTP 探测异常映射为稳定 `error_code`（dns_error/tls_error/http_error 等）：`api/src/fluent_read_api/healthchecks/supabase.py`
- [ ] T030 [US3] 在 `/health` summary 中体现“失败归因与建议的摘要”（不包含敏感信息）：`api/src/fluent_read_api/api/health.py`
- [ ] T031 [US3] 扩展移动端失败分类与建议映射（network/config/backend/dependency）：`app/lib/features/connectivity_check/connectivity_check_controller.dart`
- [ ] T032 [US3] 在移动端 UI 中展示依赖级 `action_hint`（若存在）：`app/lib/features/connectivity_check/connectivity_check_page.dart`

---

## Phase 6：打磨与横切关注点（跨故事、MVP 后再做）

**目的**: 文档与收口，确保 quickstart 可完整跑通，避免“能跑但不可验收”。

- [ ] T033 [P] 更新后端启动与环境变量示例（以实际 `uv` 命令为准）：`specs/001-tri-stack-connectivity/quickstart.md`
- [ ] T034 [P] 更新移动端启动与 `--dart-define` 示例（以实际工程为准）：`specs/001-tri-stack-connectivity/quickstart.md`
- [ ] T035 更新仓库级说明，补齐“如何本地联调”的最短路径：`README.md`
- [ ] T036 验证并修订契约一致性（后端实际响应字段对齐 `openapi.yaml`）：`specs/001-tri-stack-connectivity/contracts/openapi.yaml`

---

## 依赖关系与执行顺序（用户故事完成顺序）

- Phase 1 → Phase 2 为所有用户故事的前置
- 用户故事依赖图（建议顺序）：
  - US1 依赖 Phase 2（需要可用的 `/health` 固定结构即可联调）
  - US2 依赖 Phase 2（在其基础上把 `/health` 升级为真实探测）
  - US3 依赖 US1 + US2（需要两端都有可展示/可输出的错误归因落点）
- 推荐完成顺序（按可验收闭环与依赖）：Phase 1 → Phase 2 → US1 → US2 → US3 → Phase 6

## 并行示例（每个用户故事内）

### US1 并行示例

- 任务 A：`T015` 实现解析模型（`app/lib/features/connectivity_check/models.dart`）
- 任务 B：`T017` 实现控制器（`app/lib/features/connectivity_check/connectivity_check_controller.dart`，依赖 `T015/T016`）

### US2 并行示例

- 任务 A：`T022` 数据库探测（`api/src/fluent_read_api/healthchecks/database.py`）
- 任务 B：`T023` Supabase 探测（`api/src/fluent_read_api/healthchecks/supabase.py`）

### US3 并行示例

- 任务 A：`T028` 数据库异常归因（`api/src/fluent_read_api/healthchecks/database.py`）
- 任务 B：`T029` HTTP 异常归因（`api/src/fluent_read_api/healthchecks/supabase.py`）

---

## 实施策略（MVP 优先）

1. Phase 1：先落地 `api/` 与 `app/` 工程骨架
2. Phase 2：确保 `/health` 固定结构可用（端到端联调“插头”就绪）
3. MVP：完成 US1（安卓一键自检闭环，可演示）
4. 在 MVP 基础上完成 US2（真实依赖探测 + 降级判定）
5. 最后完成 US3（失败可定位）与 Phase 6 文档收口
