# <项目名称>

<项目名称>是一个面向职业技能等级认定业务的单机构机考考务后台系统。

当前仓库当前阶段的目标不是一次做完整平台，而是沿真实前后端主线落地最小可用闭环，先跑通以下主链路：

`创建考试计划 -> 导入/审核报名 -> 编排场次 -> 发布准考证 -> 导入/回传机考成绩 -> 成绩确认 -> 发布成绩`

## 当前阶段

当前仓库已从前期领域分析与原型设计正式进入开发阶段。

本阶段采用：

- 前端主链逐步切到独立 `frontend/` Vue 3 应用
- 用受控 demo seed / fixture 承载开发、测试与冒烟数据
- `Spring Boot + MySQL + MongoDB + Redis` 作为正式后端架构
- 保留 `app/` 与 `server/` 作为过渡期回退路径

## 工程治理入口

仓库现已引入适配当前阶段的 Harness 治理目录：

- 总入口：`references/Harness/AGENTS.md`
- 规则中心：`references/Harness/rules/`
- 角色定义：`references/Harness/agents/`
- 执行技能：`references/Harness/skills/`
- 变更闭环：`references/Harness/changes/`

如果要快速理解“当前需求到底以哪里为准、按什么顺序读”，先看：

- [docs/README.md](docs/README.md)

该治理目录保留 Harness 的结构和流程门禁；当前代码实现已扩展为 `frontend/ Vue 3 + backend/ Spring Boot` 的前后端分离基线，并继续保留 `Node 壳子 + app/ 原生前端` 的回退链路。

## 本阶段范围

- 建立真实代码仓库入口文档
- 建立前端应用目录结构
- 搭建统一后台布局与设计语法
- 落地第一批核心页面
- 以页面状态、表格、风险提示、发布控制为中心组织体验

## 原型基线

产品与原型基线来自 `docs/product/` 中整理后的正式文档，重点包括：

- [01-prd.md](docs/product/01-prd.md)
- [02-user-flow.md](docs/product/02-user-flow.md)
- [03-page-list.md](docs/product/03-page-list.md)
- [04-ui-rules.md](docs/product/04-ui-rules.md)

历史同步材料已迁出，不再保留独立的同步输入区目录。

## 开发顺序建议

1. 仓库骨架与统一布局
2. 工作台、考试计划列表、报名管理、成绩中心
3. 新建考试计划、编排任务、场次与考场分配
4. 准考证预览与发布、成绩确认与发布
5. 抽离共享组件、状态模型、后端契约
6. 接入 Spring Boot MVP 后端
7. 扩展真实业务数据与外部系统

## 本地运行

1. 安装要求：

- `Node.js 18+`
- `Java 21+`
- `Maven 3.9+`

2. 安装独立前端依赖：

```bash
cd frontend
npm install
```

3. 启动 Spring Boot demo 后端：

```bash
npm run dev:spring
```

4. 启动 Vue 3 前端：

```bash
npm run dev:frontend
```

5. 打开：

```text
https://127.0.0.1:5173
```

`frontend/` 使用 Vite proxy 将 `/api/*` 与 `/integration/*` 转发到 `http://127.0.0.1:8080`，因此前端代码只消费相对 API 路径。

本地前端通过 `frontend/certs/` 中的自签证书启用 HTTPS，浏览器首次访问时如果提示证书不受信任属于正常现象。

6. 旧 Node 演示服务仍可作为回退路径运行：

```bash
npm run dev
```

如需让旧 `app/` 通过 Node 壳子转发到 Spring Boot，可在新终端运行：

```bash
SPRING_API_PROXY_TARGET=http://127.0.0.1:8080 npm run dev
```

旧 Node 服务默认打开：

```text
http://127.0.0.1:4173
```

当前仓库中的 Node 服务负责：

- 提供 `app/` 下的前端静态文件
- 在未配置代理时继续提供原有最小 API
- 在配置 `SPRING_API_PROXY_TARGET` 时把 `/api/*` 与 `/integration/*` 转发给 Spring Boot

Spring Boot 后端位于 [`backend/pom.xml`](backend/pom.xml)，默认使用 `demo` profile 启动，不依赖本地数据库即可演示接口；`local` profile 预留给 MySQL、MongoDB、Redis 联调。

基础中间件编排位于 [`backend/docker-compose.yml`](backend/docker-compose.yml)。

### local profile 与 Docker Compose 本地部署

`demo` profile 继续作为契约安全、无中间件依赖的启动路径；`local` profile 用于连接 MySQL、MongoDB 和 Redis，承载第一批真实持久化结构、导入任务文档和任务进度缓存。

轻量依赖层：

```bash
docker compose -f backend/docker-compose.yml up -d
```

如果后端在宿主机源码启动，并连接上面的依赖层，需要显式覆盖容器网络默认主机名：

```bash
MYSQL_HOST=127.0.0.1 MONGO_HOST=127.0.0.1 REDIS_HOST=127.0.0.1 \
  mvn -f backend/pom.xml -Dmaven.repo.local=.m2 spring-boot:run -Dspring-boot.run.profiles=local
```

完整本地部署入口：

```bash
docker compose -f docker-compose.local.yml up --build
```

该 Docker Compose 拓扑包含 `frontend`、`backend`、`mysql`、`mongodb`、`redis`。其中 `frontend` 通过 nginx 提供 Vue 构建产物并把 `/api/*` 与 `/integration/*` 代理到 `backend`，`backend` 使用 `local` profile 通过容器服务名访问 `mysql`、`mongodb` 和 `redis`。

启动后打开：

```text
https://127.0.0.1:5173
http://127.0.0.1:8080/api/dashboard/overview
```

最小 smoke 入口：

```bash
npm run smoke:local
npm run smoke:compose
```

如果需要把本次结果沉淀成可复核记录，可直接使用：

```bash
npm run smoke:demo:record
npm run smoke:local:record
npm run smoke:compose:record
```

脚本会把摘要写到：

```text
docs/process/ai/smoke-runs/latest-demo-smoke.txt
docs/process/ai/smoke-runs/latest-local-smoke.txt
docs/process/ai/smoke-runs/latest-compose-smoke.txt
```

脚本会按固定顺序验证：

1. 创建计划
2. 导入报名
3. 审核报名
4. 编排
5. 准考证
6. 导入成绩
7. 确认成绩
8. 发布成绩

其中 `smoke:compose` 面向完整 `docker-compose.local.yml` 拓扑，额外验证：

1. `https://127.0.0.1:5173` 前端首页可访问
2. 前端 `/api/*` 代理可直通 Spring Boot
3. 前端代理登录后可继续读取 `/api/me`
4. 前端代理下的考试计划主链接口可读取
5. 后端 `http://127.0.0.1:8080` 直连接口可读取

## 验证命令

```bash
npm test
mvn -f backend/pom.xml -Dmaven.repo.local=.m2 test
npm --prefix frontend run build
docker compose -f docker-compose.local.yml config
```

## 当前目录

```text
ExamSystem/
├── README.md
├── AGENTS.md
├── PLANS.md
├── app/
│   ├── index.html
│   ├── scripts/
│   └── styles/
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   └── src/
├── server/
│   ├── index.js
│   ├── router.js
│   └── seed-data.js
├── backend/
│   ├── pom.xml
│   ├── docker-compose.yml
│   └── src/
├── docs/
│   ├── README.md
│   ├── product/
│   ├── architecture/
│   ├── process/
│   └── archive/
└── 历史同步材料
```
