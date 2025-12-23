# FluentRead · Flutter App（MVP）

FluentRead 是一款基于 RSS 的英文阅读 App：根据用户词汇水平，将英文文章生成「更易读的简化版」，并在阅读中支持点词查释义与标记认识/不认识。

本目录为 Flutter 客户端（MVP 仅要求 Android 可运行），产品范围以 `docs/hackathon_mvp_prd.md` 为准。

## 当前状态

- 目前代码仍为 Flutter 默认模板骨架，后续将按 PRD 逐步补齐登录、测试、RSS、阅读与点词等功能。

## MVP Happy Path（目标）

1. 邮箱验证码登录
2. 首次登录完成 CEFR 词汇测试（A1–C2）
3. 使用内置/自定义 RSS 源浏览文章列表（BBC / Hacker News）
4. 打开文章：默认展示简化版，可一键切换原文（简化可能需要 1–3 分钟，需有明确的 pending/failed 反馈）
5. 点词查看英英释义，可切换中文释义，并标记 known/unknown 更新词表

## 运行环境

- Flutter stable（建议 3.22+）/ Dart 3.x
- Android Studio / Android SDK（用于本地运行 Android）

## 本地运行

在仓库根目录执行：

1. 安装依赖：`cd app && flutter pub get`
2. 运行：`flutter run`

如果需要指定设备：`flutter devices` 后使用 `flutter run -d <device_id>`

## 配置（密钥与后端地址）

按章程要求：真实密钥只通过环境变量/构建参数注入，禁止提交到仓库。

推荐使用 `--dart-define` 注入配置（实际键名以代码实现为准）：

- `API_BASE_URL`：后端 FastAPI 基地址（例如 `http://10.0.2.2:8000`）
- `SUPABASE_URL`、`SUPABASE_ANON_KEY`：Supabase Auth 配置（如客户端直连 Supabase）

示例：

`flutter run --dart-define=API_BASE_URL=http://10.0.2.2:8000`

## 开发约定（简要）

- MVP 优先：只做 `docs/hackathon_mvp_prd.md` 的范围内功能，Non-goals 不做
- 文档/提交信息使用中文；命名固定为 `FluentRead`
