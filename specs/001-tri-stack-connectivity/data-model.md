# Phase 1 数据结构：三端互联互通骨架（连通性与健康检查）

**研究结论**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/research.md`  
**契约**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/contracts/openapi.yaml`

## 1) 领域对象（不落库）

本功能不引入业务数据与表结构；仅定义端到端“健康报告”数据结构，用于移动端展示与自动化验收（SC-004）。

### 1.1 HealthReport（健康报告）

表示一次健康检查的结果。

| 字段 | 类型 | 必填 | 规则 |
|---|---|---:|---|
| `checked_at` | string(datetime) | 是 | ISO 8601（UTC），例如 `2025-12-22T08:00:00Z` |
| `overall_status` | string | 是 | 枚举：`ok \| degraded \| down` |
| `summary` | string | 是 | 面向人类的简短摘要（不包含敏感信息） |
| `backend` | BackendStatus | 是 | 后端自身状态（见下） |
| `dependencies` | DependencyStatus[] | 是 | 至少包含 1 项；本期必须包含 `database` |
| `request_id` | string | 否 | 用于排障关联（例如 UUID） |

**整体状态判定规则（FR-005）**

- 若后端自身不可用：`down`
- 若后端可响应，但任一外部依赖为 `error`：`degraded`
- 若后端可响应且依赖均为 `ok`（或仅存在 `unknown` 但不影响主链路时，按团队约定）：`ok`

> 建议 MVP 规则：只要存在 `error` → `degraded`；`unknown` 不直接导致降级，但需要在 `summary` 中提示“配置缺失/未检查”。

### 1.2 BackendStatus（后端自身状态）

| 字段 | 类型 | 必填 | 规则 |
|---|---|---:|---|
| `status` | string | 是 | 枚举：`ok \| error` |
| `service` | string | 是 | 固定：`fluent-read-api` |
| `environment` | string | 是 | `local \| dev \| prod` |

### 1.3 DependencyStatus（外部依赖状态）

| 字段 | 类型 | 必填 | 规则 |
|---|---|---:|---|
| `name` | string | 是 | 机器可读名，例如 `database`（必含）/ `supabase`（可选） |
| `status` | string | 是 | 枚举：`ok \| error \| unknown` |
| `message` | string | 是 | 面向人类的简短说明（不含敏感信息） |
| `error_code` | string | 否 | 机器可读错误码（见下） |
| `action_hint` | string | 否 | 下一步建议（面向开发/测试） |

**本期约束（database）**

- 当 `name=database` 时，探测必须通过 SQLAlchemy ORM 执行 `SELECT 1`，用于验证 ORM 配置链路正确。

**建议的 `error_code` 枚举（可扩展）**

- `config_missing`：未配置（例如缺少 `DATABASE_URL`）
- `dns_error`：域名解析失败
- `tcp_unreachable`：端口不可达/网络不可达
- `tls_error`：TLS 握手失败
- `http_error`：HTTP 非 2xx
- `timeout`：超时
- `unexpected`：其他未知错误

## 2) 移动端展示模型（不作为后端契约）

### 2.1 ConnectivityCheckResult（移动端连通性检查结果）

| 字段 | 类型 | 说明 |
|---|---|---|
| `reachability` | enum | `reachable \| unreachable \| timeout`（满足 FR-001） |
| `checked_at` | datetime | 本地记录的触发/完成时间 |
| `health_report` | HealthReport? | 请求成功时包含 |
| `failure_category` | enum? | 用于 FR-003/FR-004：`network \| config \| backend \| dependency \| unknown` |
| `user_hint` | string | 中文建议文案（例如“检查网络/检查地址/稍后重试”） |

## 3) 状态流转（移动端）

`idle → checking → success(fetched HealthReport) | failure(mapped)`  

要求：

- 每次检查必须可重复触发（spec“可重复执行”）。
- failure 必须映射到可定位类别与下一步建议（FR-003/FR-004）。
