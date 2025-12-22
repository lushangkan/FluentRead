# fluent-read 开发指南

由所有功能的 plan.md 自动生成。最后更新：2025-12-22

## 当前技术栈

- (001-tri-stack-connectivity)

## 项目结构

```text
src/
tests/
```

## 常用命令

# Add commands for 

## 代码风格

: Follow standard conventions

## 最近变更

- 001-tri-stack-connectivity: Added

<!-- MANUAL ADDITIONS START -->
<!-- MANUAL ADDITIONS END -->

## Active Technologies
- Flutter stable（建议 3.22+）/ Dart 3.x；Python 3.12（建议） + Flutter（`dio` + `--dart-define`/`flutter_dotenv`）；FastAPI（Uvicorn/Pydantic v2/httpx/SQLAlchemy）；后端依赖管理 `uv` (001-tri-stack-connectivity)
- N/A（本功能不落库）；可选探测 Supabase Postgres（`DATABASE_URL`） (001-tri-stack-connectivity)
- Flutter stable（建议 3.22+）/ Dart 3.x；Python 3.13（建议） + Flutter（`dio` + `--dart-define`/`flutter_dotenv`）；FastAPI（Uvicorn/Pydantic v2/httpx/SQLAlchemy）；后端依赖管理 `uv` (001-tri-stack-connectivity)
- 不新增业务表；但必须配置 `DATABASE_URL`，并通过 SQLAlchemy ORM 执行 `SELECT 1` 作为健康检查的一部分 (001-tri-stack-connectivity)

## Recent Changes
- 001-tri-stack-connectivity: Added Flutter stable（建议 3.22+）/ Dart 3.x；Python 3.12（建议） + Flutter（`dio` + `--dart-define`/`flutter_dotenv`）；FastAPI（Uvicorn/Pydantic v2/httpx/SQLAlchemy）；后端依赖管理 `uv`
