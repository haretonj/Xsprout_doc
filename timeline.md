# XSprout MVP 开发时间线（5 周）

本文档基于 `doc/Gap分析文档.md` 与 `doc/改进建议与Timeline.md` 中的需求，按 5 周、三人分工与工时约束制定的可执行时间线。

---

## 1.

**角色分工：**

- **XJ**：Database migration（Redis → PostgreSQL）、美国区 database 云服务设置（**仅 03/01 及之后**）。
- **PF**：所有前端代码 + 后端 API（除 AI 相关）。
- **LN**：所有后端 AI 代码 + 后端 API（与 PF 协作的后端部分由 PF 主责非 AI，LN 主责 AI 及配套 API）。

**XJ Week 1 仅 3h 的应对：** Week 1 只交付核心表 schema + 迁移步骤大纲；其余表（sessions, counselor_profile, student_requests/counselor_requests 等）与完整迁移检查清单由 Week 2（1.5h）补全。Week 2 结束前 schema 与迁移计划定稿，不影响 Week 3 迁移执行与 PF 后端对接。

---

## 2. MVP 需求与覆盖关系

| # | 需求（来自 Gap 分析） | 对应本时间线任务 |
|---|------------------------|-------------------|
| 1 | 学生和 counselor 的注册 | 功能1 完善（角色选择、密码强度） |
| 2 | Counselor 查看学生信息 | 功能2：/students 扩展 + 前端接 API |
| 3 | Counselor 维护学生信息与申请状态标签 | 功能3：学生 CRUD + 标签 API + 前端 |
| 4 | AI 辅助生成学生 summary（可定制提示词） | 功能4：Summary API + 前端 |
| 5 | AI 推荐信模板 + 选中文字润色 | 功能5：Polish API + 前端文本选择与润色 UI |
| 6 | HTTPS 网页 | 功能6：HTTPS/反向代理配置 |
| 7 | 数据库持久化与备份 | 功能7：PostgreSQL 迁移 + 美国云库 + 备份策略 |

---

## 3. 按周详细计划

**任务类型图例**（每项任务会标注所属类型）：

| 类型 | 含义 | 示例 |
|------|------|------|
| **Database** | 数据库设计、迁移、云服务、备份/恢复 | schema、迁移脚本、备份脚本、美国云库 |
| **Frontend** | 前端页面、组件、状态、构建与部署 | 页面、表单、API 客户端、HTTPS 部署 |
| **API** | 后端非 AI 接口：路由、权限、校验、读写 DB | GET/PUT /students、标签 CRUD、注册校验 |
| **AI** | 后端 AI 相关：prompt、模型调用、AI 接口 | Summary 生成、推荐信润色、prompt 模板 |

**功能编号**：F1=注册, F2=查看学生, F3=维护学生与标签, F4=AI Summary, F5=推荐信与润色, F6=HTTPS, F7=持久化与备份。

---

### Week 1（02/15/2026）

**目标**：基础设施与 API 打通、数据库方案落稿；为 F2–F5 铺路。

#### XJ（3h）

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Database] PostgreSQL **核心表** schema 设计：users, student_profiles, requests, student_status_tags | F7 | 核心表 DDL 或设计文档 |
| 2 | [Database] Redis → PostgreSQL 迁移步骤大纲（顺序、键映射、注意事项） | F7 | 迁移步骤大纲文档 |
| 3 | 说明：不执行迁移、不建美国云库；其余表与检查清单在 Week 2 补全 | — | — |

#### PF

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Frontend] 统一 API 客户端：fetch 封装、Authorization token、错误处理、loading 状态 | F2–F5 共用 | `api-client` 模块（如 `src/lib/api-client.ts`） |
| 2 | [API] 扩展 `GET /students`：从 student_profiles 返回完整字段（grade, gpa, satScore, actScore, applicationStatus 等） | F2 | 更新后的 `/students` 接口及响应格式文档 |
| 3 | [Frontend] 学生列表页接入新 API 的占位或试跑（可选，为 Week 3 做准备） | F2 | — |

#### LN

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [API] 后端 Blueprint/模块化；统一错误处理中间件；日志（Python logging） | 全栈基础 | 重构后的后端目录与错误/日志规范 |
| 2 | [API] `POST /counselor/students/{student_id}/summary` 接口占位（路由、权限校验、请求体校验） | F4 | Summary 接口骨架（可返回占位文本） |
| 3 | [AI] Summary 用 prompt 模板草稿：支持 focus_areas、custom_prompt 占位 | F4 | `prompt.py` 中 summary 模板草稿 |

**本周交付物汇总**：核心表设计文档、迁移步骤大纲；前端 api-client、扩展版 GET /students；后端重构、Summary 接口骨架与 prompt 草稿。

**里程碑**：前端可调通后端；数据库核心表设计就绪（Week 2 补全后即可执行迁移）；Summary 接口可联调。

---

### Week 2（02/22/2026）— 仅 30% 工作量

**目标**：维持进度、小步交付；不安排大功能开发；XJ schema 与迁移计划定稿。

#### XJ

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Database] 审阅 Week 1 的 schema 与迁移文档 | F7 | 审阅意见或小修订 |
| 2 | [Database] 补全表设计：sessions, counselor_profile, student_requests, counselor_requests | F7 | 完整 schema 文档 |
| 3 | [Database] 迁移检查清单：键映射、数据类型、索引、顺序 | F7 | 迁移检查清单 |

#### PF

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Frontend] 注册页：角色选择器（student / counselor） | F1 | 注册表单角色选择 UI |
| 2 | [Frontend] 注册页：密码强度指示器（实时显示） | F1 | 密码强度组件/展示 |

#### LN

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [AI] Summary 的 prompt 模板定稿（focus_areas + custom_prompt） | F4 | `prompt.py` 中定稿的 summary 模板 |
| 2 | [API] 可选：`GET /counselor/students/{student_id}/summary`（读缓存或占位） | F4 | GET summary 端点（若实现） |

**本周交付物汇总**：完整 schema、迁移检查清单；注册角色选择与密码强度；Summary prompt 定稿（及可选 GET summary）。

**里程碑**：注册体验增强（F1 部分）；Summary 后端可联调准备就绪；**XJ schema 与迁移计划定稿**（Week 3 可开工）。

---

### Week 3（03/01/2026）

**目标**：数据库迁移与美国云库就绪；F2/F3 学生维护与标签上线；F4 AI Summary 后端完成。

#### XJ

**执行顺序**：先 1→2，以便 PF/LN 不阻塞；3 可与后端开发并行。

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Database] **美国区** PostgreSQL 云服务创建与配置；按 Week 2 定稿 schema **建表（空表）**，交付连接信息 | F7 | 美国区实例可连接、schema 就绪（**后端可据此开工**） |
| 2 | [Database] 执行 Redis → PostgreSQL 迁移脚本（或首版）：users, profiles, requests, 集合与标签 | F7 | 迁移脚本、迁移执行记录 |
| 3 | [Database] 验证迁移结果：users、student_profiles、requests 等数据一致性 | F7 | 验证结果说明或检查清单 |

#### PF

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [API] `GET /counselor/students/{student_id}`：返回单个学生完整信息（含标签），权限仅 counselor | F2, F3 | GET 学生详情接口 |
| 2 | [API] `PUT /counselor/students/{student_id}`：更新学生信息，权限与校验 | F3 | PUT 学生接口 |
| 3 | [API] `POST /counselor/students/{student_id}/tags`、`DELETE .../tags/{tag_id}`；标签数据结构与权限 | F3 | 标签增删接口 |
| 4 | [Frontend] `/counselor/students` 接真实 API（替换 mock）；loading、错误、刷新 | F2 | 学生列表页接后端 |
| 5 | [Frontend] 学生详情页或模态框：信息展示 + 编辑表单 + 保存 | F3 | 学生详情/编辑 UI |
| 6 | [Frontend] 标签展示与添加/删除 UI（学生详情内或列表卡片） | F3 | 标签组件与交互 |

#### LN

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [AI] `POST /counselor/students/{student_id}/summary` 完整逻辑：组合 prompt、调用现有 AI、返回 summary | F4 | Summary 生成 API 可用 |
| 2 | [API] 权限与参数校验（counselor、student_id、focus_areas、custom_prompt） | F4 | 接口符合文档 |
| 3 | [AI] 可选：summary 缓存（Redis 或 PostgreSQL）避免重复生成 | F4 | 缓存策略（若实现） |

**本周交付物汇总**：美国云 PostgreSQL + 迁移脚本与验证；学生 GET/PUT、标签 CRUD API；学生列表/详情/标签前端；Summary 生成 API。

**里程碑**：主数据在 PostgreSQL（美国）；counselor 可查看/编辑学生并管理标签（F2、F3）；F4 AI Summary 后端完成。

---

### Week 4（03/08/2026）

**目标**：F4 Summary 前端完成；F5 推荐信润色端到端完成；F7 备份与 F6 准备启动。

#### XJ

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Database] 备份策略文档（频率、保留策略、存储位置） | F7 | 备份策略文档 |
| 2 | [Database] PostgreSQL 备份脚本（定时导出或云厂商快照） | F7 | 备份脚本 |
| 3 | [Database] 保留策略（如最近 7 天）；可选：恢复步骤文档 | F7 | 保留策略说明；恢复文档（可选） |

#### PF

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Frontend] Summary 展示区：学生详情内或独立页，格式化展示 summary 文本 | F4 | Summary 展示组件/页面 |
| 2 | [Frontend] focus_areas 选择器、custom_prompt 输入框；“生成 Summary”按钮、loading、错误、重新生成 | F4 | Summary 生成 UI |
| 3 | [Frontend] 推荐信页：文本选择（Textarea + 选中）、润色按钮（选中后出现） | F5 | 文本选择与润色入口 UI |
| 4 | [Frontend] 调用 polish API、润色结果替换原文本、简单撤销 | F5 | 润色交互与状态 |

#### LN

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [API] `POST /counselor/recommendation/polish`：请求体 text、instruction（可选） | F5 | Polish 接口 |
| 2 | [AI] 润色 prompt 设计与实现；调用现有 AI 服务 | F5 | 润色 prompt 与模型调用 |
| 3 | [API] 与现有推荐信流程集成（权限、可选 student_id/recommendation_id） | F5 | 接口联调完成 |
| 4 | [API] 可选：协助 HTTPS/部署相关后端配置 | F6 | — |

**本周交付物汇总**：备份策略、备份脚本、保留策略；Summary 前端完整流程；推荐信选中+润色 UI + Polish API 与 prompt。

**里程碑**：F4 Summary、F5 润色端到端完成；F7 备份方案可执行。

---

### Week 5（03/15/2026）

**目标**：F6 HTTPS 上线；F7 备份自动化与验证；全量回归，MVP 七项需求收尾。

#### XJ

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Database] 美国云库备份与恢复验证（执行恢复测试并记录） | F7 | 恢复验证记录 |
| 2 | [Database] 备份自动化：cron 或云厂商定时任务 | F7 | 自动备份就绪 |
| 3 | [Database] 证书或 Nginx/反向代理相关协作（文档或权限、与 PF/LN 对齐） | F6 | 协作文档或配置说明 |

#### PF

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [Frontend] 前端生产构建与 HTTPS 部署配置 | F6 | 生产构建与部署流程 |
| 2 | [Frontend] Nginx/反向代理或托管平台：SSL 配置、HTTP→HTTPS 重定向 | F6 | HTTPS 前端/入口就绪 |
| 3 | [Frontend] 全功能回归：F1 注册、F2 学生列表、F3 详情与标签、F4 Summary、F5 推荐信与润色 | F1–F5 | 回归清单/测试结果 |

#### LN

| # | 任务（类型） | 对应功能 | 交付物 |
|---|----------------|----------|--------|
| 1 | [API] 后端在 HTTPS 下的配置与联调（CORS、代理、环境变量） | F6 | 后端 HTTPS 就绪 |
| 2 | [API] API 与 AI 流程回归（学生、标签、Summary、Polish） | F2–F5 | 回归结果 |
| 3 | [API] Bug 修复、日志与错误信息完善 | 全栈 | 修复与文档 |
| 4 | [API] 可选：证书续期或健康检查配置 | F6 | — |

**本周交付物汇总**：自动备份与恢复验证；HTTPS 全站配置与回归；后端联调与修复。

**里程碑**：F6 HTTPS 全站可用；F7 备份与恢复验证通过；MVP 七项需求全部覆盖。

---

## 4. 按人汇总（谁在哪周做什么）

类型与功能图例同 §3：**Database / Frontend / API / AI**；**F1–F7** 为功能编号。

### XJ（Database migration + 美国云库 + 备份）

| 周 | 任务（类型 · 功能） | 交付物 |
|----|----------------------|--------|
| 02/15 | [Database] 核心表 schema、迁移步骤大纲（F7） | 核心表设计、迁移大纲 |
| 02/22 | [Database] 审阅、补全表设计、迁移检查清单（F7） | 完整 schema、检查清单 |
| 03/01 | [Database] 美国区 PostgreSQL、迁移脚本与验证（F7） | 云实例、迁移脚本、验证结果 |
| 03/08 | [Database] 备份策略、备份脚本、保留策略、恢复文档（F7） | 备份脚本与文档 |
| 03/15 | [Database] 自动备份、恢复验证、HTTPS/运维协作（F6/F7） | 自动备份就绪、恢复验证记录 |

### PF（前端 + 后端非 AI API）

| 周 | 任务（类型 · 功能） | 交付物 |
|----|----------------------|--------|
| 02/15 | [Frontend] API 客户端；[API] 扩展 GET /students（F2） | api-client、/students 扩展 |
| 02/22 | [Frontend] 注册：角色选择、密码强度（F1） | 注册表单增强 |
| 03/01 | [API] 学生 GET/PUT、标签 CRUD；[Frontend] 列表接 API、详情与标签 UI（F2/F3） | 学生与标签 API + 前端 |
| 03/08 | [Frontend] Summary 展示与生成 UI；推荐信选中+润色 UI（F4/F5） | Summary 前端、润色 UI |
| 03/15 | [Frontend] HTTPS 部署、全功能回归（F6，F1–F5 回归） | HTTPS 就绪、回归清单 |

### LN（后端 AI + 后端 API 协作）

| 周 | 任务（类型 · 功能） | 交付物 |
|----|----------------------|--------|
| 02/15 | [API] 后端重构、错误/日志；[API] Summary 接口占位；[AI] Summary prompt 草稿（F4） | 后端重构、Summary 骨架与 prompt 草稿 |
| 02/22 | [AI] Summary prompt 定稿；[API] 可选 GET summary（F4） | Prompt 定稿、GET summary（可选） |
| 03/01 | [AI] POST summary 完整实现（F4） | Summary 生成 API |
| 03/08 | [API] POST recommendation/polish；[AI] 润色 prompt、与推荐信集成（F5） | Polish API、润色 prompt |
| 03/15 | [API] 后端 HTTPS、回归、Bug 修复与文档（F6，F2–F5 回归） | 后端 HTTPS、修复与文档 |

---

## 5. 与 doc 需求的对应关系

- **Gap分析文档.md**：功能 1–7 的 Gap 均在本时间线中有对应任务（见第 2 节表格）。
- **改进建议与Timeline.md**：  
  - 数据库：PostgreSQL 引入、迁移、美国云库由 XJ 在 03/01 及之后执行。  
  - 功能 2–5 的 API 与前端按“改进建议”拆到各周；功能 6 HTTPS、功能 7 备份按 5 周压缩到 Week 4–5。
- **02/22 仅 30%**：该周仅安排审阅、小增强和收尾类工作，无大功能开发。
- **03/01 起 XJ 美国数据库**：迁移与云库仅在 03/01/2026 及之后安排，符合约束。
- **XJ 02/15 仅 3h**：计划已按“Week 1 核心表 + 大纲、Week 2 补全”设计，七项 MVP 需求覆盖与交付时间不变。

---

## 6. 风险与依赖

### Database migration 会 block 后端吗？

**结论：不会，前提是 Week 3 按下面顺序执行。**

- **依赖关系**：PF/LN 在 Week 3 开发的学生/标签 API 和 Summary API 需要**可连接的 PostgreSQL 与已建好的 schema**，不需要等「Redis 数据全部迁完」才能开工。
- **推荐做法（不阻塞）**：  
  1. **Week 3 初**：XJ 先完成「美国区 PostgreSQL 实例创建 + 按 Week 2 定稿 schema 建表（空表即可）」并交付连接信息。  
  2. **PF/LN**：即可基于该空库（或少量 seed 数据）开发/联调学生、标签、Summary 等 API，**与 XJ 的数据迁移并行**。  
  3. **XJ**：在 Week 3 内完成 Redis → PostgreSQL 的数据迁移与验证；迁移完成后后端无需改代码，只是数据从空变为完整。
- **若顺序颠倒**：若 PF/LN 必须等「迁移完成、数据就绪」再开发，则会被 block。因此 **XJ Week 3 的优先交付物是「实例 + schema 就绪」**，数据迁移为同周内后续步骤。
- **备选**：若 Week 3 初 PG 暂不可用，PF 可先对**现有 Redis** 实现 GET/PUT/标签 API，待 PG 就绪后切换数据源（需后端做一层 DB 抽象或配置切换），避免空等。

### 其他风险与依赖

- **XJ Week 1 仅 3h**：已通过“核心表 + 迁移大纲”在 Week 1 完成、Week 2 补全其余表与检查清单的方式吸收；总交付物与 Week 3 依赖不变，不影响总体计划。
- **迁移时序**：Week 3 后端学生/标签 API 需能接 PostgreSQL（或双写过渡），依赖 **Week 2 结束前定稿的** schema；**不依赖迁移完成**，依赖「实例 + schema 就绪」。PF 开发前与 XJ 对齐字段与键名。
- **LN 03/01 仅 10h**：Summary 后端以“可用”为目标，前端与 polish 集中在 Week 4。
- **HTTPS**：若使用云厂商 SSL（如 Cloudflare/ALB），可减少自建 Nginx 工作量，PF/LN 需预留与 XJ/运维的对接时间。

---

*文档版本：基于 doc/Gap分析文档.md、doc/改进建议与Timeline.md 与前后端仓库结构整理。*
