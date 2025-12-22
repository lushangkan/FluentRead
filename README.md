# FluentRead

基于 RSS 的英文阅读 App：面向母语为中文、正在学习英语的用户。通过用户的词汇水平（CEFR A1–C2）对文章进行「可读化重写」，在保留原意的前提下尽量降低阅读门槛，并支持点词释义与词表反馈。

产品需求与细节说明见：`docs/hackathon_mvp_prd.md`。

## MVP 目标（Happy Path）

1. 邮箱验证码登录（Supabase Auth）。
2. 首次登录完成 CEFR 词汇水平测试，生成个人基础词表。
3. 订阅/使用内置 RSS 源，看到文章列表。
4. 打开文章：优先展示「简化版」，支持一键切换「原文」。
5. 阅读中点击单词：查看英英释义，可切换中文释义；并可标记「认识/不认识」以更新个人词表。

## 核心功能

- 登录 & Onboarding：邮箱验证码登录；首次强制完成 CEFR 测试。
- RSS 订阅 & 列表：内置源默认可用；支持添加/删除 RSS；文章按时间倒序。
- 文章阅读 & 可读化：首次打开触发简化版生成；`pending/ready/failed` 状态可感知；失败可回退原文。
- 点词释义 & 词表标记：单词级释义（英英 + 中文可切换）；标记 known/unknown 更新用户 patch（不强制立即重算当前文章）。

## 技术栈（计划）

- 前端：Flutter（Android MVP）
- 后端：FastAPI + SQLAlchemy + Supabase Postgres
- Auth：Supabase Auth（邮箱验证码）
- NLP Utilities：分词/切句/词形归一化（内置于后端）
- AI / LLM：OpenAI 风格 HTTP API（文章简化、单词释义生成）
