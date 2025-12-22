---

description: "功能实现的任务清单模板"
---

# 任务清单：[FEATURE NAME]

**输入**: 来自 `/specs/[###-feature-name]/` 的设计与规格文档

**前置条件**: plan.md（必需）、spec.md（必需，用于用户故事）、research.md、data-model.md、contracts/

**测试**: 示例中包含测试任务。自动化测试是否编写以 spec.md 为准；MVP 阶段优先保证 Happy Path 可验收。

**组织方式**: 按用户故事分组任务，保证每个故事可独立实现与独立验收。

## 格式：`[ID] [P?] [Story] 描述`

- **[P]**: 可并行（不同文件、无依赖）
- **[Story]**: 归属的用户故事（例如 US1/US2/US3）
- 描述中必须包含精确文件路径

## 路径约定

- **单体项目**: 仓库根目录 `src/`、`tests/`
- **Web 应用**: `backend/src/`、`frontend/src/`
- **移动端 + API**: `api/src/` + `ios/src/` 或 `android/src/`
- 下方示例默认“单体项目”，以 plan.md 的结构为准调整

<!--
  ============================================================================
  重要：下方任务仅为示例。

  生成 tasks.md 时必须替换为真实任务，来源包括：
  - spec.md 的用户故事（含优先级 P1/P2/P3...）
  - plan.md 的功能需求与技术方案
  - data-model.md 的实体设计
  - contracts/ 的接口定义

  任务必须按用户故事组织，确保每个故事可以：
  - 独立实现
  - 独立验收（必要时独立测试）
  - 作为 MVP 增量独立交付

  不要在最终 tasks.md 中保留这些示例任务。
  ============================================================================
-->

## Phase 1：初始化（共享基础设施）

**目的**: 项目初始化与基础结构准备

- [ ] T001 按实施计划创建项目结构
- [ ] T002 初始化 [language] 工程并添加 [framework] 依赖
- [ ] T003 [P] 配置 lint 与格式化工具

---

## Phase 2：地基（阻塞性前置）

**目的**: 在任何用户故事开始前必须完成的核心基础设施

**⚠️ 关键**: 本阶段未完成前，不得开始用户故事实现

地基阶段示例任务（按项目实际情况调整）：

- [ ] T004 建立数据库 schema 与迁移机制
- [ ] T005 [P] 实现认证/鉴权框架
- [ ] T006 [P] 搭建 API 路由与中间件结构
- [ ] T007 创建所有故事依赖的基础模型/实体
- [ ] T008 配置错误处理与日志基础设施
- [ ] T009 建立环境配置管理机制

**检查点**: 基础设施就绪，可并行推进各用户故事

---

## Phase 3：用户故事 1 - [标题]（优先级：P1）🎯 MVP

**目标**: [该故事交付的价值，用一句话说明]

**独立验收方式**: [如何验证该故事可独立完成]

### 用户故事 1 的测试（可选：仅在 spec 明确要求时）⚠️

> **注意**: 先写测试并确保失败，再开始实现

- [ ] T010 [P] [US1] 为 [endpoint] 编写契约测试：tests/contract/test_[name].py
- [ ] T011 [P] [US1] 为 [user journey] 编写集成测试：tests/integration/test_[name].py

### 用户故事 1 的实现

- [ ] T012 [P] [US1] 创建 [Entity1] 模型：src/models/[entity1].py
- [ ] T013 [P] [US1] 创建 [Entity2] 模型：src/models/[entity2].py
- [ ] T014 [US1] 实现 [Service]：src/services/[service].py（依赖 T012/T013）
- [ ] T015 [US1] 实现 [endpoint/feature]：src/[location]/[file].py
- [ ] T016 [US1] 增加校验与错误处理
- [ ] T017 [US1] 增加用户故事 1 的关键日志

**检查点**: 此时用户故事 1 应可独立运行并完成验收

---

## Phase 4：用户故事 2 - [标题]（优先级：P2）

**目标**: [该故事交付的价值，用一句话说明]

**独立验收方式**: [如何验证该故事可独立完成]

### 用户故事 2 的测试（可选：仅在 spec 明确要求时）⚠️

- [ ] T018 [P] [US2] 为 [endpoint] 编写契约测试：tests/contract/test_[name].py
- [ ] T019 [P] [US2] 为 [user journey] 编写集成测试：tests/integration/test_[name].py

### 用户故事 2 的实现

- [ ] T020 [P] [US2] 创建 [Entity] 模型：src/models/[entity].py
- [ ] T021 [US2] 实现 [Service]：src/services/[service].py
- [ ] T022 [US2] 实现 [endpoint/feature]：src/[location]/[file].py
- [ ] T023 [US2] 与用户故事 1 的组件集成（如需要）

**检查点**: 此时用户故事 1 与 2 都应可独立运行并完成验收

---

## Phase 5：用户故事 3 - [标题]（优先级：P3）

**目标**: [该故事交付的价值，用一句话说明]

**独立验收方式**: [如何验证该故事可独立完成]

### 用户故事 3 的测试（可选：仅在 spec 明确要求时）⚠️

- [ ] T024 [P] [US3] 为 [endpoint] 编写契约测试：tests/contract/test_[name].py
- [ ] T025 [P] [US3] 为 [user journey] 编写集成测试：tests/integration/test_[name].py

### 用户故事 3 的实现

- [ ] T026 [P] [US3] 创建 [Entity] 模型：src/models/[entity].py
- [ ] T027 [US3] 实现 [Service]：src/services/[service].py
- [ ] T028 [US3] 实现 [endpoint/feature]：src/[location]/[file].py

**检查点**: 此时所有用户故事应可独立运行并完成验收

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N：打磨与横切关注点

**目的**: 影响多个用户故事的改进（MVP 阶段谨慎引入）

- [ ] TXXX [P] 更新文档：docs/
- [ ] TXXX 代码清理与重构
- [ ] TXXX 跨故事的性能优化
- [ ] TXXX [P] 增加单元测试（如需要）：tests/unit/
- [ ] TXXX 安全加固（仅做必要项）
- [ ] TXXX 验证 quickstart.md 可跑通

---

## 依赖关系与执行顺序

### 阶段依赖

- **初始化（Phase 1）**: 无依赖，可立即开始
- **地基（Phase 2）**: 依赖 Phase 1 完成；会阻塞所有用户故事
- **用户故事（Phase 3+）**: 都依赖 Phase 2 完成
  - 人手足够时可并行推进
  - 否则按优先级顺序（P1 → P2 → P3）推进
- **打磨（最终阶段）**: 依赖所有计划纳入的用户故事完成

### 用户故事依赖

- **用户故事 1（P1）**: Phase 2 完成后即可开始；不依赖其他故事
- **用户故事 2（P2）**: Phase 2 完成后即可开始；可与 US1 集成，但必须可独立验收
- **用户故事 3（P3）**: Phase 2 完成后即可开始；可与 US1/US2 集成，但必须可独立验收

### 用户故事内部顺序

- 测试（如纳入）必须先写并确保失败，再开始实现
- 先模型再服务
- 先服务再接口/页面
- 先核心实现再集成
- 一个故事完成后再推进下一个优先级

### 可并行项

- Phase 1 标记为 [P] 的任务可并行
- Phase 2 标记为 [P] 的任务可在阶段内并行
- Phase 2 完成后，多个用户故事可按团队容量并行
- 同一用户故事内标记为 [P] 的测试可并行
- 同一用户故事内标记为 [P] 的模型可并行
- 不同用户故事可由不同同学并行推进

---

## 并行示例：用户故事 1

```bash
# 并行启动用户故事 1 的全部测试（如纳入测试）：
Task: "Contract test for [endpoint] in tests/contract/test_[name].py"
Task: "Integration test for [user journey] in tests/integration/test_[name].py"

# 并行创建用户故事 1 的全部模型：
Task: "Create [Entity1] model in src/models/[entity1].py"
Task: "Create [Entity2] model in src/models/[entity2].py"
```

---

## 实施策略

### MVP 优先（先只做用户故事 1）

1. 完成 Phase 1：初始化
2. 完成 Phase 2：地基（关键：会阻塞所有用户故事）
3. 完成 Phase 3：用户故事 1
4. **停止并验收**：确保用户故事 1 可独立跑通
5. 视情况部署/演示

### 增量交付

1. 完成初始化 + 地基 → 基础设施就绪
2. 增加用户故事 1 → 独立验收 → 部署/演示（MVP）
3. 增加用户故事 2 → 独立验收 → 部署/演示
4. 增加用户故事 3 → 独立验收 → 部署/演示
5. 每个故事都在不破坏之前能力的前提下增加价值

### 多人并行策略

多人协作时：

1. 团队共同完成初始化 + 地基
2. 地基完成后：
   - 开发者 A：用户故事 1
   - 开发者 B：用户故事 2
   - 开发者 C：用户故事 3
3. 各故事独立完成后再集成

---

## 备注

- [P] 表示任务可并行（不同文件、无依赖）
- [Story] 用于将任务映射到具体用户故事，便于追踪
- 每个用户故事应可独立完成与独立验收
- 测试（如纳入）应先失败再实现
- 建议按任务或逻辑小组提交，保持提交原子性
- 每到检查点就停下来做独立验收
- 避免：描述含糊的任务、多人同时改同一文件、打破独立性的跨故事强耦合
