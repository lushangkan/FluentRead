# Quickstart：三端互联互通骨架（连通性与健康检查）

本文件描述本功能在实现完成后的**本地跑通与验收**步骤（对齐 spec 的“独立验收方式”与 SC-001/SC-002/SC-003/SC-004）。

**规格**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/spec.md`  
**契约**: `/home/zara/Documents/Project/fluent-read/specs/001-tri-stack-connectivity/contracts/openapi.yaml`

## 0) 你需要准备的配置

### 移动端（Flutter）

- `API_BASE_URL`：后端基地址  
  - Android 模拟器：常用 `http://10.0.2.2:8000`  
  - 真机：同网段可达地址（例如 `http://192.168.1.10:8000`）

### 后端（FastAPI）

- `APP_ENV`：`local`（默认）
- 外部依赖（至少 1 个；本期固定包含数据库）：
  - `DATABASE_URL`（敏感，Supabase Postgres 连接串；用于 ORM `SELECT 1`）
  - （可选）`SUPABASE_URL`（例如 `https://xxxx.supabase.co`）

> 安全：任何真实密钥/连接串禁止提交到仓库；仅通过环境变量或本地 `.env` 文件提供。

## 1) 启动后端（实现完成后）

```bash
cd /home/zara/Documents/Project/fluent-read

# 示例：本地环境变量（也可以使用 --env-file 加载）
export APP_ENV=local
export DATABASE_URL="postgresql+psycopg://USER:PASSWORD@HOST:PORT/postgres"
# 可选
# export SUPABASE_URL="https://xxxx.supabase.co"

# 使用 uv 安装/运行（具体命令以最终 api/pyproject.toml 为准）
cd /home/zara/Documents/Project/fluent-read/api
uv sync
uv run uvicorn fluent_read_api.main:app --reload --host 0.0.0.0 --port 8000
```

## 2) 用 curl 验收后端健康入口

```bash
curl -sS http://localhost:8000/health | jq .
```

预期：

- 永远返回结构化 JSON（字段含义稳定），符合契约 `openapi.yaml`
- 通过 `overall_status` 区分：
  - `ok`：后端自身 + 外部依赖均正常
  - `degraded`：后端可响应，但至少 1 个外部依赖异常（FR-005）

## 3) 制造常见故障并验收“失败可定位”（P2）

1. **地址错误/不可达**（移动端侧）
   - 将 `API_BASE_URL` 指向错误地址或断网
   - 预期：移动端显示 `不可达/超时`，并给出建议（检查网络/检查地址）
2. **外部依赖异常**（后端侧）
   - 将 `DATABASE_URL` 改为不可用连接串/断网
   - 预期：`/health` 返回 `overall_status=degraded`，依赖项包含 `error_code` 与 `action_hint`

## 4) 启动移动端并验收“一键自检”

（实现完成后补齐实际命令；本段仅定义验收方法）

验收步骤：

1. 打开 Android App 的“连通性检查”页面
2. 点击“检查连接”
3. 预期：
   - 展示本次检查时间
   - 展示“后端可达/不可达/超时”之一（FR-001）
   - 请求成功时展示健康摘要与依赖明细（FR-002/FR-003）
   - 失败时展示失败类型与下一步建议，并支持重试（FR-004）
