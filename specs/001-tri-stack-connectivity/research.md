# Phase 0 研究：三端互联互通骨架（连通性与健康检查）

**规格**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/spec.md`  
**实施计划**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/plan.md`

## 研究目标

- 把“健康检查”做成稳定契约：字段含义固定、可自动化验收（对齐 SC-004）。
- 把“失败可定位”做成端到端能力：后端有清晰状态与依赖明细，移动端能映射为“失败类型 + 下一步建议”（对齐 FR-003/FR-004/SC-003）。
- 明确你需要提供哪些配置（数据库/Supabase/后端地址），并标注敏感程度与用途。

## 决策记录（Decisions）

### 1) 健康入口路径与访问控制

- **Decision**: 后端提供 `GET /health`（匿名可访问），返回结构化 `HealthReport`。
- **Rationale**: 直观、通用；满足 FR-002/FR-006；移动端一键检查只需一个入口即可闭环。
- **Alternatives considered**:
  - `/healthz`/`/readyz`：更偏 K8s 习惯；可作为后续增强，但 MVP 先只做一个入口。
  - 需要鉴权：与 FR-006 冲突，不采纳。

### 2) 状态枚举（整体与依赖）

- **Decision**:
  - 整体 `overall_status`: `ok | degraded | down`
  - 依赖 `status`: `ok | error | unknown`
- **Rationale**:
  - `degraded` 明确表达“后端仍可响应但外部依赖异常”（FR-005）。
  - `unknown` 允许配置缺失或超时等情况，不把“没配”误报成“故障”。
- **Alternatives considered**:
  - 仅 `ok/fail`：无法表达降级与部分失败，不利于定位。
  - 过细枚举（如 `timeout/dns_error/tls_error` 作为主状态）：可做 `error_code` 字段承载，避免把枚举膨胀为契约负担。

### 3) 外部依赖至少 1 项：选择与探测方式（本期固定包含数据库）

- **Decision**: 依赖项必须包含 `database`（基于 `DATABASE_URL` 使用 SQLAlchemy ORM 执行 `SELECT 1`），可选增加 `supabase`（通过 `SUPABASE_URL` 做 HTTP/TCP 可达性探测）。
- **Rationale**:
  - 用 ORM 执行 `SELECT 1` 能验证 ORM/连接池/驱动等配置链路正确，且符合“项目数据操作统一使用 ORM”的约束。
  - 本期要求仅“连通性检查”，不要求鉴权与读写权限（FR-007），因此优先做“可达性/可查询”而非“权限正确性”。
- **Alternatives considered**:
  - 直接用 `SUPABASE_ANON_KEY` 调用 Supabase REST/API 做更深层校验：会引入密钥管理与权限差异，且超出 FR-007 的“连通性”范围。
  - 仅检查 DNS：太弱，无法覆盖 TLS/网络策略/端口阻断等问题。

### 4) 超时与并发策略（后端）

- **Decision**:
  - 每个依赖探测有独立超时（建议 1.5s），整体健康检查总超时（建议 2.5s）。
  - 依赖探测并发执行（`async` + `httpx`），避免串行导致延迟累积。
- **Rationale**: 对齐 SC-002（3 秒内得到结果）；并发与分层超时能保证在依赖挂死时仍可快速返回“可定位”的失败。
- **Alternatives considered**:
  - 串行探测：实现简单但易超时，体验差。
  - 无超时：会导致请求挂起，无法给用户可行动反馈。

### 5) 错误归因与“下一步建议”

- **Decision**:
  - 后端为每个依赖返回：`error_code`（机器可读）+ `message`（人可读）+ `action_hint`（下一步建议）。
  - 移动端将常见失败映射为 3 类主结果：`reachable` / `unreachable` / `timeout`（满足 FR-001），并在详情里展示依赖明细与建议（满足 FR-003/FR-004）。
- **Rationale**: 把“可定位”从主观描述变成稳定输出；移动端无需解析异常堆栈即可展示清晰结论。
- **Alternatives considered**:
  - 只返回后端异常字符串：不可控、难以本地化、难以做自动化验收。

### 6) 不泄露敏感信息（日志与响应）

- **Decision**: `/health` 响应与日志不返回/不打印：密钥、连接串、token、内部堆栈；只返回“安全摘要”。
- **Rationale**: 章程安全底线；`/health` 匿名可访问更应谨慎。
- **Alternatives considered**:
  - 直接返回异常堆栈：排障快但风险高，不采纳。

### 7) 配置注入（移动端与后端）

- **Decision**:
  - 移动端：开发期用 `--dart-define` 或 `flutter_dotenv`（二选一，建议 `--dart-define` 更安全），最少提供 `API_BASE_URL`。
  - 后端：使用环境变量（可配合 `.env` 文件），将敏感变量标为“只在本地/部署环境存在”。
- **Rationale**: 与章程“环境变量管理密钥”一致；不把密钥编进 App 或仓库。
- **Alternatives considered**:
  - 把后端地址做成 App 内可编辑：与 FR-008 冲突（本期不做）。

## 需要你提供的配置清单（最小集合）

### 必需（才能验收 P1）

- `API_BASE_URL`（移动端）：后端基地址  
  - Android 模拟器：常用 `http://10.0.2.2:8000`（本机 `localhost:8000` 的映射）
  - 真机：同网段可达的后端地址（例如电脑局域网 IP）
- `DATABASE_URL`（后端，敏感）：Supabase Postgres 连接串（用于 SQLAlchemy ORM 执行 `SELECT 1`）

### 推荐（用于“外部依赖”验收与演示）

- `SUPABASE_URL`（后端 + 可选移动端）：你的 Supabase 项目 URL（例如 `https://xxxx.supabase.co`）

### 可选增强

- N/A

## 结论

本阶段已将所有“NEEDS CLARIFICATION”收敛为可执行决策：入口路径、状态枚举、依赖探测策略、超时与并发、错误归因与配置要求。下一步进入 Phase 1：输出数据结构（`data-model.md`）、OpenAPI 契约（`contracts/openapi.yaml`）与 quickstart（`quickstart.md`）。
