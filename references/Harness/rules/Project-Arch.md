# 工程结构规范

## 1. 目标

本规范用于统一<项目名称>当前仓库的目录职责、分层边界和依赖方向。当前阶段以 `frontend/` Vue 3 与 `backend/` <后端技术栈 API> 为主线，`app/` 与 `server/` 仅保留为 legacy fallback。

## 2. 仓库目录结构

当前仓库建议结构如下：

```text
<project-root>/
  frontend/
    package.json
    vite.config.js
    src/
  backend/
    pom.xml
    src/
    docker-compose.yml
  app/
    index.html
    scripts/
    styles/
  server/
    index.js
    router.js
    tests/
  docs/
  references/
    Harness/
```

目录职责：

- `frontend/`
  放当前主线 Vue 3 + Vite 前端应用、路由、状态、页面和共享样式。
- `frontend/src/`
  放 Vue 入口、应用壳、路由、状态管理、API client 与页面视图。
- `backend/`
  放当前主线 <后端技术栈 API>、开发环境 profile、测试和未来真实后端接入边界。
- `backend/src/`
  放 Java 应用入口、controller、service、security、common 和测试代码。
- `app/`
  放 legacy 原生前端回退路径；新页面默认不再进入此目录。
- `server/`
  放 legacy Node 静态壳与回退 API；新接口默认不再进入此目录。
- `server/tests/`
  放 Node 内建测试，覆盖 legacy 路径、前后端闭环和关键治理结构校验。
- `docs/`
  放正式产品、架构、过程与归档文档。
- `references/Harness/`
  放工程治理规则、角色、技能和变更闭环资料。

## 2.1 文档架构分层

`docs/` 目录当前建议按以下职责划分：

- `docs/product/`
  放产品需求、用户流程、页面清单、界面规则和原型基线，是产品真相源。
- `docs/architecture/`
  放系统技术架构、前后端边界、数据架构、部署架构和集成边界，是技术方案真相源。
- `docs/process/`
  放实施计划、AI spec/plan、阶段性过程资料和决策痕迹，不替代正式产品或架构真相源。
- `docs/archive/`
  放归档说明、历史导入说明和过期资料索引。

## 3. 前端分层规则

当前主线前端依赖方向必须保持为：

`view/route -> store/api/client -> backend API or controlled transitional data`

强约束：

- 新页面、新导航和新状态默认放在 `frontend/src/`。
- 路由元信息应承载页面标题、权限和导航语义，避免页面内重复硬编码。
- 可复用界面拼装、状态标签、统计条、风险面板和表格样式应优先抽离为共享结构或共享样式。
- 页面状态优先通过 Pinia store、路由状态或职责清晰的本地状态管理，不得散落跨模块魔法字符串。
- API 消费优先走共享 API client 或业务 API 封装，页面中禁止直接拼接底层请求细节。
- 若维护 legacy 页面，仍遵循 `app/` 的既有分层：`screen -> ui/state/api -> mock-data`。

## 4. 后端分层规则

当前主线后端依赖方向必须保持为：

`controller -> service -> seed/fixture or persistence boundary`

强约束：

- `backend/` 是新接口、新契约和正式后端能力的默认实现路径。
- controller 负责 HTTP 入参、响应和错误边界，不承载复杂业务组装。
- service 负责接口级业务数据组装，并明确测试数据、过渡逻辑或真实持久化边界。
- 开发环境 profile 不得伪装成真实生产持久化；未确认字段和状态必须在服务层或契约文档中说明。
- 若维护 legacy Node 回退路径，仍遵循 `server/` 的既有分层：`http entry -> router -> service -> seed/mock data`。

## 5. 命名规范

- Vue 组件和视图文件使用现有 `frontend/` 约定，优先保持语义清晰。
- 前端路由、权限 key、状态名称必须与原型主稿和后端权限契约保持一致。
- Java 类名使用清晰业务语义，例如 `ExamPlanController`、`DemoBackendService`。
- 测试数据、seed 和 API 封装使用能表达业务边界的名称，禁止用无意义缩写。
- 文档目录中的需求目录使用 `<TYPE>-<SEQ>-<slug>`。

## 6. 验证命令

常用验证命令：

```bash
npm test
npm --prefix frontend run build
mvn -f backend/pom.xml -Dmaven.repo.local=.m2 test
```

按变更范围选择最小有效验证；跨前后端或治理规则改动优先执行 `npm test`。

## 7. 架构红线

1. 不允许在当前阶段无依据地新增脱离需求的复杂后端分层或伪数据库抽象。
2. 不允许页面直接跳过 API 封装或状态层拼接业务请求。
3. 不允许为单个页面私自发明与原型不一致的状态流转。
4. 不允许把治理文件散落到 `references/Harness/` 之外。
5. 不允许把 legacy fallback 当作新需求默认落点，除非任务明确要求维护 `app/` 或 `server/`。

## 8. 技术架构文档规范

### 8.1 文档目标

技术架构文档必须优先回答以下问题：

1. 当前前端、后端、数据与集成分别采用什么技术。
2. 当前仓库主线目录与 legacy fallback 如何分工。
3. 本地开发、联调和完整部署的运行拓扑是什么。
4. 哪些能力已经落地，哪些仍是规划或外部依赖。

### 8.2 推荐文档分工

- `system-architecture.md`
  系统技术架构总览，回答“整套系统怎么搭起来”。
- `frontend-backend-boundary-*.md`
  当前前后端职责、接口优先级、迁移策略和联调边界。
- `api-design.md`
  接口域划分和接口总清单，偏目录与设计总览。
- `database-design.md`
  数据建模、存储职责、核心实体、状态模型和持久化边界。
- `deployment.md`
  环境分层、部署拓扑、依赖组件、运行方式和发布约束。

### 8.3 system-architecture.md 最少应包含

- `## 目标与范围`
- `## 当前主线技术栈`
- `## 仓库主线与 legacy fallback`
- `## 系统运行拓扑`
- `## 前端架构`
- `## 后端架构`
- `## 数据架构`
- `## 外部集成架构`
- `## 环境分层`
- `## 当前已实现边界`
- `## 相关文档索引`

### 8.4 强制表格化内容

以下内容默认必须优先使用表格表达，而不是纯段落描述：

- 技术栈清单
  推荐列：`层级 | 目录/服务 | 技术 | 当前用途 | 说明`
- 运行拓扑
  推荐列：`节点 | 技术 | 角色 | 依赖对象 | 入口/端口`
- 数据职责
  推荐列：`存储 | 技术 | 承载数据 | 读写方 | 当前状态`
- 环境分层
  推荐列：`环境 | 组成 | 用途 | 数据策略 | 说明`
- 外部集成清单
  推荐列：`系统 | 方向 | 协议/入口 | 业务作用 | 当前状态`

### 8.5 状态标记要求

- 文档必须明确区分：
  - `当前已实现`
  - `当前主线计划内`
  - `历史 fallback`
  - `未来规划 / 未落地`
- 不允许把规划态内容写成已实现事实。
- 不允许保留与当前仓库结构冲突的阶段描述。
