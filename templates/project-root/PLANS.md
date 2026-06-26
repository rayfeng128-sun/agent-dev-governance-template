# <项目名称>开发计划

## 当前定位

`PLANS.md` 现在承担两件事：

1. 作为当前开发计划的导航入口。
2. 作为当前阶段的高层执行看板，快速反映完成度、主线状态和下一步。

维护约束：

- 每次任务完成后，如果任务状态、阶段顺序、完成度判断或下一步发生变化，必须同步更新 `PLANS.md`。
- 每次新需求写好或已有需求边界、优先级、阶段归属发生变化时，如果用户要求“更新计划”，必须同步更新 `PLANS.md`。
- 不允许让 `PLANS.md` 长期落后于 `changes/`、当前主线架构文档或最新需求定义。

当前仓库已经从“前端 MVP 壳子阶段”进入“可用系统重构阶段”，后续计划以真实前后端闭环、正式接口契约和可持续演进为目标，而不是继续以 Mock 驱动演示为主。

## 当前总目标

把<项目名称>从原型仓库推进为一个可真实使用、可持续扩展的前后端分离系统：

- 前端主线：`frontend/` Vue 3
- 后端主线：`backend/` Spring Boot
- 需求主语：用户故事与端到端可用闭环
- 交付标准：真实联通、真实状态流转、真实保存、基础权限与异常处理可运行

## 当前总览

| 主题 | 当前状态 | 完成度 | 当前结论 | 下一步 |
|---|---|---|---|---|
| 治理与需求入口 | 已完成 | 高 | `AGENTS.md`、`references/Harness/`、轻量/完整流程、story-first 定义已成体系 | 继续按新规则执行，并减少历史 page-first 干扰 |
| 前后端分离骨架 | 已完成 | 高 | `frontend/` <前端技术栈> 与 `backend/` Spring Boot 主线已明确，legacy fallback 已降级 | 保持新需求默认走主线，不再扩展 `app/ + server/` |
| 代码结构规范 | 已建立 | 中 | 已补充 `docs/architecture/code-structure-guidelines.md`，明确前端 `shared + modules` 与后端单体分模块、业务域分包的目标结构；这只是规范建立，不代表现有代码迁移完成 | 后续新增或重写切片按业务域分包执行，迁移继续按用户故事或单个业务域小步推进 |
| 主链接口与页面联调 | 主链已通 | 高 | 认证、考试计划、报名、编排、准考证、成绩主链接口与页面已打通，`P04` 考试计划详情、`sessions / tickets` 下游上下文消费以及 `FEAT-009` 成绩发布闭环都已补强完成 | 继续补原型细节、异常态、完成态和跨页上下文透传 |
| 本地持久化与 Compose | 主链已验证 | 高 | `demo`、`local`、完整本地 Compose 拓扑、service 门面拆分、`Registration` 最小闭环测试与 smoke 入口都已落地；当前已有 `demo` / `local` / 完整 Compose 三份固定 smoke 记录，完整 Compose 也已通过真实 `up --build`、前端首页、代理登录/主链接口与后端直连接口验证 | 继续补 repository/job 边界、迁移、seed 与更多 local 持久化链路 |
| Story-first 需求闭环 | 部分完成 | 中 | `FEAT-017` 已作为样例落地，但历史需求仍以 page-first 为主 | 后续新增需求默认按用户故事切分和追踪 |

## 当前主线需求状态

| 需求/FEAT | 类型 | 状态 | 当前完成到哪里 | 主要缺口 | 下一步 |
|---|---|---|---|---|---|
| `FEAT-014-frontend-separation-vue3` | 架构主线 | 已完成 | 已建立 Vue 3 独立前端、会话初始化、主链 API 模块和 Spring 对接基线 | 仍需继续减少 placeholder 页占比 | 作为后续页面迁移与联调基座维持 |
| `FEAT-015-registration-management-vue3` | 主链业务 | 已完成 | 报名列表、导入结果、审核、权限和浏览器冒烟已落地；报名页与导入结果页已开始承接 `planId / planName` 上下文，前端已消费导入异常编号、批量审核摘要和详情侧边栏的 `reviewHistory`，后端 local 主记录、导入任务、导入异常明细和审核记录都已补 plan 维度过滤与落库，最近一次导入结果也已按当前 `planId` 取数；本地仓库路径下的 `stats / risks` 也已开始跟随当前计划过滤结果，且当前计划无导入批次时已返回明确空状态、不再混入 demo 最近批次；本轮进一步补齐了报名页所有“查看导入结果”入口的计划上下文透传，并让导入结果页在 `planId / planName / jobId` 变化时都会重新加载；后端 local 路径下导入任务的 `importedAt` 也已统一成与 demo 一致的页面展示时间格式；同时 local 报名列表的风险提示现在也会联动最近一次导入批次的异常数，不再只看主表状态而漏掉“失败明细待处理”；本轮再补齐 `RegistrationImportJobDocument.nextActions` 的持久化与回放，local 路径下最近一次导入结果刷新后不再丢失“返回报名列表修正异常数据 / 下载失败明细 / 重新导入报名名单”等后续动作；同时同一 `planId` 再次导入时，local 主记录已开始先清理旧报名快照再写入新批次，避免刷新后继续混入过期报名数据；即使本轮导入后的计划快照为空，旧报名主记录现在也会被正确清空，不会再让“当前计划已无报名数据”继续显示历史记录；并且 local 导入落库现在会按当前 `planId` 拉取计划作用域内的报名快照，不再把其他计划的 demo 报名行误写进当前计划；同一 `jobId` 的异常明细现在也已改为先清旧快照再保存新结果，当前批次已无异常时也不会再残留历史失败项；同时当前导入批次涉及报名记录的旧审核历史也会被同步淘汰，避免新批次刷新后继续挂着上一轮审核轨迹；本轮再把报名列表里的 `reviewHistory` 回放收口到 `RegistrationStore.findReviewHistories(...)` 批量边界，不再由 `RegistrationService` 逐条读取审核历史仓储；同时 `RegistrationImportJobStore.findLatest(...)` 也已收口到 repository 查询边界，不再回退成 `findAll().stream().filter(...)` 的内存扫描；报名导入写路径现在也会优先复用导入响应自带的 `rows / exceptions / nextActions` 落库，不再由 `RegistrationService` 在 service 层二次回读 demo `listRegistrations(...)`，只有旧响应未携带 `rows` 时才保留兜底快照读取；此外即使当前计划本地导入后主表仍是 `0` 条记录，报名列表现在也会继续保持 local 空状态并回放当前批次异常风险，不再回退 demo 报名数据；报名导入结果页现在也能在仅带 `jobId` 的深链接下通过返回结果中的 `planId` 自恢复计划上下文，返回报名列表与重新导入链路不会再因为缺少 query 中的计划标识而变弱；报名列表页在缺少 `query.planName` 的刷新或深链接场景下，现在也会优先根据最近一次导入任务回放中的 `planContext.planName` 恢复计划名称，前端标题和“查看最近导入结果”入口不再只回退到裸 `planId`；报名导入结果页在缺少 `query.planName` 的刷新或仅带 `jobId` 的深链接场景下，也会优先根据返回任务中的 `planContext.planName` 恢复计划名称，顶部上下文、返回报名列表和重新导入链路不再继续退化成纯 `planId`；同时 Spring 与 Node fallback 两条报名接口链路都已显式返回 `planContext`，当前过渡双栈不再出现一边能恢复名称、一边只能显示 ID 的分叉；本轮再补齐报名列表与导入结果页的 `返回计划详情` 入口，报名链现在可稳定回到 `P04` 上下文中心；同时也补上了 `进入编排任务` 入口，报名链可直接带当前计划上下文进入编排主链 | 仍可继续打磨原型细节与视觉层次 | 继续跟随后端契约细化 |
| `FEAT-017-exam-plan-draft-closure` | Story-first 样例 | 主链已通 | 已完成创建草稿并回到列表可见的最小故事闭环；本轮进一步把 local 路径下的新建考试计划写边界收口到 `ExamPlanStore`，repository 已可写时会直接生成本地草稿并返回创建回执，不再先读取 demo 创建结果再回写本地快照；同时创建回执里的 `stats` 也已收口到 `ExamPlanStore.countByStatus(...)` 计数边界，不再为了返回草稿统计由 `ExamPlanService` 再次回捞整份计划列表 | 仍需继续补更强验收与更多本地写路径细节 | 继续收敛到 local 持久化与更强验收 |
| `P04-exam-plan-detail-mainline` | 页面/能力 | 本轮补强完成 | Vue 主线独立详情页、路由、`P02 -> P04`、`P03 -> P04` 和后端 `progress / metrics / actions` 契约已补齐，并通过前端测试、后端契约测试和前端构建验证；本轮进一步补齐 `local` 仓库路径下考试计划发布后的详情完成态，刷新后 `progress / actions / blocks` 不再停留在草稿占位语义；同时本地考试计划现在也会真实保存 `owner / createdAt / updatedAt / publishedBy / publishedAt`，列表与详情刷新后不再继续回退成“本地用户 + 今天日期”的占位值，`P02` 侧栏与 `P04` 详情页也都已直接展示发布信息；本轮再补齐创建计划时的 `level / subjects / subjectSummary / examType / description / registrationStart / registrationEnd / examStart / examEnd / capacity / date` 本地持久化，刷新后列表与详情不再回退成“本地仓库计划 / 0 / 0 / 0 / 0”式占位值；同时 local 路径下考试计划列表与详情现在也会联动本地 `Registration` 仓储回放真实 `registeredCount` 与容量占用，刷新后不再固定显示 `0` 人；`ExamPlanStore.registeredCount(...)` 现在也已改走 repository 计数边界，不再在 store 内扫描全量报名主记录；此外当本地仓库已启用但当前还没有任何考试计划时，列表现在也会保持本地空状态，不再整页回退 demo 计划数据；当详情请求的 `planId` 在当前路径下不存在时，后端现在也会明确返回 `404`，不再回退 demo 详情；发布动作在同样的缺失计划场景下也已统一返回 `404`，不再回退 demo 发布结果；同时详情页快捷动作透传到下游页面时，`planName` 现在也会至少回退到当前 `planId`，避免详情刚刷新或名称尚未回填时把空上下文带进下游主链 | 详情上下文已完成第一批路由透传，但下游页面的数据消费与刷新保持仍需继续补齐 | 下一轮继续把详情上下文贯穿到下游主链页面 |
| `FEAT-005-scheduling-tasks` | 页面/能力 | 主链已通 | Vue 主线已补真实编排任务页，接入任务列表、风险、最近一次重算结果与重新计算动作，并可继续带上下文进入场次分配页；legacy fallback 也已补齐最近一次重算快照回放与 `plan-b` 独立编排数据，不再回退固定 `plan-a`；Spring `local` 路径现在也会把最近一次重算快照回填到主统计区，避免刷新后统计与回执分叉；本轮进一步补齐了 `latestJob.resultSummary / nextActions` 的持久化与回放，demo / local / legacy 三条路径在刷新后都会保留最近一次重算摘要和后续动作建议，前端编排页也已开始直接消费这些字段；同时 local 路径下的风险提示现在也会跟最近一次重算快照联动，当冲突数或待分配人数变化时，顶部风险区不再继续停留在旧 demo 风险文案；demo 编排主线现在也已按 `planId` 隔离最近一次重算与场次保存结果，`plan-a` 保存后的 `latestAllocation` 和已分配状态不会再串到 `plan-b`，`plan-b` 重算后的冲突数和未分配人数也不会再错误回退成 `plan-a` 的 `2 / 128`；本轮再补齐“重算会使旧场次分配失效”的语义，demo 与 local 路径下重新编排后都会清掉当前计划旧的 `latestAllocation` 快照，刷新场次页不再继续沿用重算前的旧考场分配；此外当 local 仓库仅存在其他计划的编排快照、当前计划尚无本地重算结果时，编排任务页现在会明确返回“先执行编排重算”的空状态提示，不再继续沿用 demo 任务与风险；这类本地空状态下，编排阶段条现在也会同步清空，不再一边提示“暂无本地编排快照”、一边继续显示 demo 阶段进度；同时 Spring `SchedulingService` 在这类本地空态下也已直接通过 job/store 返回空响应，不再先读取 demo `getSchedulingTasks(...)` 再覆盖为空；当当前计划已有本地重算快照时，`SchedulingService.getSchedulingTasks(...)` 现在也会直接通过 `SchedulingJobSnapshotStore` 回放 `latestJob / stages / stats / risks / tasks / strategies` 主响应，不再先读取 demo `getSchedulingTasks(...)` 作为底板；对应的重算写路径现在也会优先复用重算响应自带的 `stages / stats / risks / tasks / strategies` 来沉淀 job snapshot `resultSummary`，不再由 `SchedulingService` 在 service 层二次回读 demo `getSchedulingTasks(...)`，只有旧响应未携带这些展示字段时才保留兜底读取；当当前计划已经存在带展示摘要的本地 `latestJob` 快照时，`recalculateScheduling(...)` 现在也会优先直接通过 `SchedulingJobSnapshotStore` 组装本地重算回执，并继续沉淀最新重算快照，不再先调用 demo 重算接口再回写本地状态；前端页面也已把这类本地阻断态显式提升为“暂无本地编排快照 / 请先执行编排重算”的可见提示；同时页面头部已补 `返回计划详情`，当前主链可从 `P07` 稳定返回 `P04`；并且当前页在缺少 `query.planName` 的刷新或深链接场景下，也会优先根据后端返回的 `planContext.planName` 恢复计划名称，标题与进入场次分配链路不再只回退成裸 `planId`；Spring 与 Node fallback 两条编排接口链路现在也都会显式返回 `planContext`，过渡双栈对当前计划名称的恢复语义已对齐 | 原型交互、阻断逻辑和完成反馈仍可加强 | 补细节并继续接真实持久化 |
| `FEAT-006-session-allocation` | 页面/能力 | 主链已通 | 已有场次分配与确认链路，且已开始承接考试计划上下文；前端已开始消费最近一次保存快照；legacy fallback 也已补齐保存后刷新回放与 `plan-b` 独立场次分配状态；Spring `local` 路径也已开始用最近一次保存快照回填场次表格，避免刷新后重新出现“待确定”假状态；本轮进一步补齐 demo 主线下 `PUT /session-allocations -> GET /sessions` 的刷新回放，最近一次保存、第二场考场和分配状态已能在默认 profile 下稳定保留；同时 `SchedulingService` 现已在 demo / local 两条路径下回放 `latestAllocation.nextActions`，并把最近一次保存提示补进风险区，刷新后不会再只剩旧风险文案却丢掉“下一步去生成准考证/同步容量统计”的动作上下文；同一计划重新执行编排重算后，旧场次分配快照现在也会同步失效，场次页会回到当前重算后的待分配状态，而不会继续展示过期考场结果；本轮再补齐 local 边界下的空上下文语义：只有当当前计划既没有本地编排快照、也没有本地分配快照时，场次页才会明确返回“先执行编排重算”的空状态，不会误伤已有重算结果但尚未保存分配的主链；同时 Spring `SchedulingService` 在这类本地空态下也已直接通过 job/store 返回空响应，不再先读取 demo `getSessionAllocation(...)` 再覆盖为空；当前计划若已有带展示摘要的本地场次分配快照，`SchedulingService.getSessionAllocation(...)` 现在也会直接通过 `SchedulingAllocationSnapshotStore` 回放 `latestAllocation / stats / risks / sessions / rooms / rules` 主响应，不再先读取 demo `getSessionAllocation(...)` 作为底板；对应的保存写路径现在也会优先复用保存响应自带的 `stats / risks / sessions / rooms / rules` 来沉淀 allocation snapshot `resultSummary`，不再由 `SchedulingService` 在 service 层二次回读 demo `getSessionAllocation(...)`，只有旧响应未携带这些展示字段时才保留兜底读取；当当前计划只剩旧式精简 `latestAllocation` 快照、而 demo session-allocation 底板缺席或为空时，`getSessionAllocation(...)` 现在也会直接回落到本地场次分配空基座，并继续回放 `planContext / risks / latestAllocation`，不再把 `sessions / rooms / rules` 掉成空壳；当当前计划已经存在带展示摘要的本地 `latestAllocation` 快照时，`saveSessionAllocation(...)` 现在也会优先直接通过 `SchedulingAllocationSnapshotStore` 组装本地保存回执，并继续沉淀最新分配快照，不再先调用 demo 保存接口再回写本地状态；前端页面现在也会把这类状态显式展示为“暂无本地场次分配上下文 / 请先执行编排重算”，避免用户只看到空表格却不知道下一步；同时页面级主链导航已补齐，`P08` 现在可稳定返回 `P07`，也可继续进入 `P09` 准考证发布；此外当前页在缺少 `query.planName` 的刷新或深链接场景下，也会优先根据后端返回的 `planContext.planName` 恢复计划名称，标题与进入准考证发布链路不再只回退成裸 `planId`；Spring 与 Node fallback 两条场次接口链路现在也都会显式返回 `planContext`，过渡双栈对当前计划名称的恢复语义已对齐 | 自动推荐、冲突处理和更细状态仍偏薄 | 继续补真实资源与冲突约束 |
| `FEAT-007-ticket-publish` | 页面/能力 | 主链已通 | 已有查询、预览、生成和发布链路，且已开始承接考试计划上下文；后端已补生成任务与发布回执本地边界，并让 `GET /tickets` 返回真实 `planContext` 与按当前计划回填的预览信息；前后端已补第一批结构化 `blocks` 阻断规则，存在阻断项时页面会禁用发布，后端直调也会返回 `400`；生成成功后再次查询时，`latestGenerateJob` 最近一次生成批次、生成时间和生成数量也可稳定回放；本轮进一步补齐了 `latestGenerateJob.resultSummary / nextActions` 的持久化与回放，demo / local / legacy 三条路径刷新后都会保留最近一次生成摘要和后续动作；当前计划若已经存在带展示摘要的本地 `latestGenerateJob` 快照，`generateTickets(...)` 现在也会优先直接通过 `TicketGenerateJobStore` 组装本地生成回执，并继续沉淀最新生成快照，不再先调用 demo 生成接口再回写本地状态；当 `latestGenerateJob` 仍是旧式精简快照、而 demo ticket 底板缺席或为空时，`listTickets(...)` 现在也会直接回落到本地阻断态与计划预览，不再因为继续依赖 demo `getTickets(...)` 返回空壳而把 `preview / blocks / risks` 掉成空值；当 `latestGenerateJob` 仍是旧式精简快照、当前生成批次已经无异常、而 demo ticket 底板缺席或为空时，`listTickets(...)` 现在也会直接回落到本地“可继续发布”生成态与计划预览，不再误退成“请先生成准考证”的阻断空态；当 `latestPublishJob` 仍是旧式精简快照、而 demo ticket 底板缺席或为空时，`listTickets(...)` 现在也会直接回落到本地已发布态与计划预览，不再误退成“请先生成准考证”的阻断态；发布成功后再查询时，统计、风险、阻断项、列表状态以及 `latestPublishJob` 最近一次发布回执都可稳定回放完成态，且已开始回放 `resultSummary / nextActions`；Spring `local` 路径也已开始依据最近一次发布回执回填列表完成态，避免服务重启后主区域与回执分叉；前端已开始消费最近一次生成 / 发布结果，并已对齐 `generatedAt / publishedAt` 时间字段，刷新后不再丢失时间展示；本轮进一步补齐 demo 主线下 `plan-b` 的准考证行数据与预览样例，当前计划上下文不再只改标题而继续混入 `plan-a` 的考生记录；`TicketService` 在 local 路径下也已开始把最近一次生成快照回填到统计区，生成回执与主区域数字不再继续分叉；同时 local 路径下的“发布前风险”现在也会联动最近一次生成异常数，不再只保留旧风险文案而漏掉最新失败明细；生成批次若仍有异常待处理时，这条信息现在也会被正式提升为 `blocks`，demo 与 local 两条路径下页面禁用与后端 `400` 拦截已对齐；本轮再补齐发布动作前的阻断校验一致性，`publishTickets` 现已复用当前回放后的票据状态，最近一次发布回执已显示“无阻断”时，不会再被旧 demo `blocks` 误拦；同一计划重新生成准考证后，旧的发布回执现在也会同步失效，demo 与 local 路径下列表会回到当前生成态，而不会继续展示过期的“已发布 / 无阻断”状态；此外当 local 仓库里仅存在其他计划的准考证生成/发布记录时，当前计划现在也会明确返回“先生成准考证”的本地阻断语义，不再沿用 demo 语义直接放行发布；这类本地阻断态下，ticket 列表现在也会同步清空 demo 统计和准考证行数据，不再一边提示“暂无本地生成记录”、一边继续显示演示考生列表；同时 Spring `TicketService` 在这类本地空态下也已直接通过 job/store 返回空响应，不再先读取 demo `getTickets(...)` 再覆盖为空；当前计划若已有带展示摘要的本地生成 / 发布快照，`TicketService.listTickets(...)` 现在也会直接通过 `TicketGenerateJobStore` / `TicketPublishJobStore` 回放 `latestGenerateJob / latestPublishJob / stats / rows / risks / blocks / preview` 主响应，不再先读取 demo `getTickets(...)` 作为底板；对应的生成 / 发布写路径现在也会优先复用动作响应自带的 `stats / rows / risks / blocks / preview` 来沉淀 job `resultSummary`，不再由 `TicketService` 在 service 层二次回读 demo `getTickets(...)`，只有旧响应未携带这些展示字段时才保留兜底读取；同时 `publishTickets(...)` 在本地 job 快照已带展示摘要时，现在也会优先直接通过 `TicketGenerateJobStore` / `TicketPublishJobStore` 组装本地发布回执并继续沉淀最新发布快照，不再先调用 demo 发布接口再回写本地状态；前端准考证页现在也会把这类状态显式展示为“暂无本地准考证生成记录 / 请先生成准考证”，不再只是阻断列表中的一条普通文案；同时当前页在缺少 `query.planName` 的深链接或刷新场景下，也会优先根据后端返回的 `planContext.planName` 与预览区 `preview.plan` 恢复计划名称，标题与进入成绩中心链路不再只回退成裸 `planId`；本轮再把 `TicketService` 的计划上下文来源收口到 `ExamPlanContextStore`，票据列表里的 `planContext` 与 `preview.plan` 不再隐式依赖 demo `getTickets` 返回的名称字段；页面头部也已补 `返回计划详情` 与 `进入成绩中心`，`P09` 已可直接承接后续成绩主链 | 生成门槛和完成态颗粒度仍可继续加强 | 继续细化阻断规则来源、生成/发布回执结构与完成态表达 |
| `FEAT-008-score-list` | 页面/能力 | 主链已通 | 已有列表、导入、确认相关主链，并已开始按 `planId` 承接考试计划详情透传的成绩上下文；前端已消费发布结果摘要与后续动作，后端已补 `ScoreService` 边界、`ScoreRecord` 本地主记录仓储、导入异常明细、复核说明回放与发布后结构化结果回放；成绩导入任务在 demo / local / legacy 路径下也已补齐读取接口与缺失任务 `404` 边界，legacy 回退路径也已补齐 `/api/scores/import`、按 `planId` 取数，以及 `plan-b` 独立状态与确认动作的计划对齐；本轮进一步补齐了成绩发布后的列表完成态回填，demo / local / legacy 路径下 `GET /api/scores` 刷新后都不会再停留在旧统计和旧行状态；同时本地仓库路径下再次导入同一计划时，已开始先清理该 `planId` 的旧成绩快照，再写入新批次结果，避免列表混入过期成绩；成绩导入任务的 `nextActions` 也已开始持久化并在刷新后回放，导入后的复核建议不再只存在于动作当次响应里；成绩中心列表接口现在也会带回 `latestImportJob`，页面刷新后仍可恢复最近一次导入批次、异常摘要和后续动作；本轮还补齐了详情侧栏与当前选中成绩行的绑定，复核意见、回传摘要和处理历史不再混用列表第一条记录；成绩确认接口现在也已支持接收并保存真实复核意见，前端侧边栏可直接录入并在刷新后继续回放；后端 `ScoreService`、demo 主链和 legacy 回退链现在也会在确认后回放 `reviewComment / confirmedBy / confirmedAt`，`plan-b` 的确认摘要、处理历史和待确认统计不再混入 `plan-a` 的固定 `88 / 1,121 / 119` 口径；本轮再把确认快照回放收口到 `ScoreStore.findLatestConfirmations(...)` 批量边界，`ScoreService` 在 review 列表里不再逐条读取确认仓储；同时 `ScoreStore.findByPlanId(...)` 现在也已收口到 repository 查询边界，成绩列表与复核页按计划回放时不再先拉全量主记录再在 store 内筛选；成绩导入写路径现在也会优先复用导入响应自带的 `rows / exceptions / nextActions` 落库，不再由 `ScoreService` 在 service 层二次回读 demo `getScores(...)`，只有旧响应未携带 `rows` 时才保留兜底快照读取；同时 `ScoreService.confirmScore(...)` 在本地仓储路径下也已优先直接走 `ScoreStore` 完成主记录与确认快照写入，并在缺失记录时返回本地 `404`，不再先调用 demo 确认接口再反写本地状态；Spring `local` 路径下的成绩发布复核接口现在也会根据本地仓储重建 `summary / timeline / actions`，发布页顶部摘要、时间线和建议动作不再停留在 demo 旧占位值，发布完成后第三步时间线也会切到真实发布时间、建议动作会切到发布回执后续动作；同时 `GET /api/scores` 发布后详情区也会把“确认状态”回填为“已发布”，并追加发布历史，不再出现主状态已发布但详情仍停留在“可发布”的分叉；前端成绩详情侧栏现在也会直接展示“发布信息”，可读到 `publishedBy / publishedAt`，不用再只从计划级复核区反推发布回执；demo 与 legacy 主线下 `plan-b` 的成绩发布也已按计划维度独立保存，发布后刷新复核页与列表页不会再回退成 `待发布`，也不会再混入 `plan-a` 的固定发布口径；本轮再补齐 local 路径下复核页 `blocks` 的本地重建与发布前阻断校验，仓储里仍有待确认成绩时，review 页与发布动作现在会保持同一条阻断语义，而不会出现“页面已提示待处理，动作仍直接发布”的分叉；同一计划重新导入成绩后，旧的发布回执现在也会同步失效，demo 与 local 路径下列表会回到当前导入态，而不会继续展示过期的已发布完成态；同一 `jobId` 的成绩导入异常明细现在也已改为先清旧快照再保存新结果，当前批次已无异常时不会再残留历史失败项；同时同一计划重新导入后，当前批次涉及成绩的旧人工确认快照也会被同步淘汰，review 页不会再被历史 `可发布 / 复核意见` 覆盖回新导入批次；此外当本地仓库仅存在其他计划成绩时，当前计划的列表页与复核页现在也会返回明确空状态，不再泄漏 demo 考生详情或旧复核摘要；即使当前计划本地导入后主表仍是 `0` 条记录，成绩列表现在也会继续保持本地空状态并回放当前导入批次异常，而不再回退 demo 成绩数据；同时 Spring `ScoreService.listScores(...)` 与 `getScoreReview(...)` 在这类本地空态下也已直接通过 `ScoreStore` 返回空响应，不再先读取 demo `getScores(...)` / `getScoreReview(...)` 再覆盖为空；当 demo review 底板缺席或返回空壳、但当前计划已存在旧式精简 `latestPublishJob` 快照时，`getScoreReview(...)` 现在也会直接回落到本地已发布 review 状态，不再把 `planId / detail / summary / timeline / actions` 掉成半空壳；当 demo score 底板缺席或返回空壳、但当前计划已存在旧式精简 `latestPublishJob` 快照时，`listScores(...)` 现在也会直接回落到本地已发布空基座，并继续回放 `planContext / detail / latestPublishJob`，不再把列表页掉成无上下文空壳；当当前计划已有本地成绩主记录时，`ScoreService.listScores(...)` 现在也会直接通过 `ExamPlanContextStore + ScoreStore` 组装主响应，`getScoreReview(...)` 也会直接通过 `ScoreStore + ExamPlanContextStore` 组装复核页主响应，不再先读取 demo `getScores(...)` / `getScoreReview(...)` 作为底板；前端成绩中心现在也会把“暂无本地成绩数据 / 请先导入成绩”显式展示出来，并让侧栏发布按钮跟随复核阻断项禁用，不再出现页面可点、后端才报错的分叉；本轮再补齐成绩中心页的 `返回计划详情` 入口，当前成绩处理链可稳定回到 `P04`；同时也补上了成绩中心与成绩发布页的 `返回准考证发布` 入口，当前成绩链可直接回到准考证链；此外成绩中心在缺少 `query.planName` 的刷新或深链接场景下，现在也会优先根据 `GET /api/scores` 返回的 `planContext.planName` 恢复标题和下游跳转上下文，不再只依赖 query 或复核摘要 | 仍需更强原型对齐、异常态表达和更细的结构化规则 | 继续收口页面细节、上下文保持与持久化边界 |
| `FEAT-009-score-publish` | 页面/能力 | 本轮补强完成 | Vue 主线独立页已补齐复核加载、发布状态、阻断 / 风险提示、确认勾选、发布时间、结构化发布结果和返回入口；Spring demo 发布后刷新可见完成态，并已改为承接真实路由计划上下文；本轮进一步补齐 demo / local 路径下发布完成态对 `blocks / risks / stats / rows` 的回填，复核页刷新后不再出现“已发布但仍显示阻断项”的分叉；同时当前计划在 local 路径下暂无可发布成绩时，确认发布页也会显式展示“暂无可发布成绩 / 请先导入并确认成绩 / 确认最终发布批次”的阻断引导，不再只是把本地空状态埋在普通风险列表里；此外当前页在缺少 `query.planName` 的深链接或刷新场景下，也会优先根据复核摘要里的“当前计划”恢复计划名称，标题与返回成绩中心链路不再只回退到裸 `planId`；本轮再补齐 `返回计划详情` 入口，成绩发布完成态现在也能稳定回到 `P04` 上下文中心 | 结构化阻断规则和生产持久化仍需后续收敛 | 下一轮继续收敛结构化阻断契约与真实持久化边界 |
| `FEAT-002` ~ `FEAT-013` | 历史 page-first 记录 | 历史归档 | 已完成原型映射、页面记录和大部分实现登记 | 需求切分方式偏旧，不适合作为当前排期主轴 | 保留映射价值，不再作为默认排期模式 |
| `TECH-015` / `TECH-016` | 治理支撑 | 已完成 | 轻量治理与 story-first 定义已落地 | 仍需在后续需求中持续执行 | 用测试和模板继续守住规则 |

## 当前后端深化状态

| 主题 | 当前状态 | 当前结论 | 下一步 |
|---|---|---|---|
| profile 模式 | 基础已具备 | `demo` 与 `local` 双轨已建立，`demo` profile 回归与后端关键契约测试可稳定运行 | 继续增强 local 冒烟与真实数据边界 |
| 存储职责 | 已定义基础方向 | MySQL / MongoDB / Redis 职责已在架构文档中明确 | 将职责继续落到 repository、job 和迁移脚本 |
| Compose 拓扑 | 已完成基础搭建 | 依赖层编排和完整本地 Compose 均已存在，`docker compose -f docker-compose.local.yml config` 已通过，且已完成一次真实 `up --build` 与 `smoke:compose:record`，前端首页、代理 API 与后端直连都已有固定结果 | 增加更强的联调、健康检查和更深的主链覆盖 |
| 正式持久化 | 进行中 | `ExamPlan` / `Registration` 已切到 `Controller -> Service -> Repository/Job` 门面；`ExamPlan` 本轮进一步抽出 `ExamPlanStore`，统一承接考试计划主记录查询、保存与报名人数聚合，`ExamPlanService` 不再直接持有 `ExamPlanRepository` / `RegistrationRepository`；同时 local 路径下的新建考试计划现在也会优先直接写入 `ExamPlanStore` 并返回本地创建回执，不再先读取 demo `createExamPlan(...)` 结果再回写本地快照；创建回执里的 `stats` 现也已改走 `ExamPlanStore.countByStatus(...)` 计数边界，不再为了返回草稿统计再回捞整份计划列表；`ExamPlanContextStore` 在 local 路径下若本地 repository 已启用但当前计划缺失，也不会再回退 demo 计划详情，而是直接回落到静态占位名称，避免下游 `planContext` 在本地空态下重新混入 demo 计划语义；`Dashboard` 现在也已抽出 `DashboardOverviewStore`，统一承接工作台 `overview` 所需的计划数、待审核报名、待发布成绩和最新编排冲突聚合，`DashboardService` 不再直接持有 `ExamPlanRepository`、`RegistrationRepository`、`ScoreRecordRepository`、`SchedulingJobSnapshotRepository`；其中工作台里的“待审核报名”本轮已改走 `RegistrationRepository.countByStatus("待审核")` 计数边界，不再为了一个 overview 统计把全量报名主记录拉进 store；工作台里的总计划数与“进行中的考试计划”现在也已分别改走 `ExamPlanRepository.count()` 与 `countByStatusNot("已发布")`，不再为了计数回放把全量计划主记录拉进 store；工作台里的“待发布成绩”现在也已改走 `ScoreRecordRepository` 的 distinct plan 计数查询，不再为了一个 overview 统计把全量成绩主记录拉进 store；工作台里的“待处理编排冲突”现在也已改走 `SchedulingJobSnapshotRepository` 聚合最新计划快照的冲突总数，不再为了一个 overview 统计把全量编排快照拉进 store；同时本地列表与详情现在也会联动 `Registration` 主记录回放真实报名人数与容量占用，考试计划刷新后不再固定显示 `0`；并且当本地仓库已启用但当前还没有任何考试计划时，考试计划列表现在也会继续保持本地空状态，而不再回退 demo 计划数据；当前详情请求的 `planId` 若在当前路径下不存在，exam-plan 主链现在也会明确返回 `404`，不再继续回退 demo 详情；发布动作在同样的缺失计划场景下现在也已统一返回 `404`，不再继续回退 demo 发布结果；`Dashboard` 工作台 `overview` 现在也已开始联动本地计划数、待审核报名、待发布成绩和按计划最新快照聚合后的真实“待处理编排冲突”总数；本地仓库启用但暂无计划、暂无待发布成绩或已清空编排冲突时，不再继续显示 demo 计划计数、演示发布提醒与旧冲突数；`Registration` 已补主记录落库、导入任务、导入异常明细、审核更新、批量审核、审核记录以及 plan 维度过滤，且最近一次导入失败数也已开始反哺本地报名列表风险提示，并补齐了导入任务 `nextActions` 的持久化与刷新回放；本轮 `RegistrationService` 进一步改为通过 `ExamPlanContextStore` 统一读取计划上下文，不再自行解析 `planName` 或直接调用 `DemoBackendService.getExamPlanDetail`；同时 `RegistrationStore` 与 `RegistrationImportJobStore` 继续分别承接报名主记录/异常明细/审核轨迹持久化边界，以及导入 job 快照查询、Mongo 持久化与 Redis 进度键写入，`RegistrationService` 不再直接持有 `RegistrationRepository`、`RegistrationImportExceptionRepository`、`RegistrationReviewRecordRepository`、`RegistrationImportJobRepository` 或 `JobProgressCache`；报名列表当前若已带 `planId`，`RegistrationStore` 现在也会优先走 `RegistrationRepository.findByPlanIdOrderByUpdatedAtDesc(...)`，不再先把全量报名主记录拉回 store 再按计划筛选；批量审核当前若已走本地 repository 路径，`RegistrationService` 现在也会通过 `RegistrationStore.findByIds(...)` 一次性读取当前批次主记录，不再在 service 里逐条 `findById`；同一 `planId` 的重复导入现在也会先替换本地旧报名快照，再保存新批次结果，避免 local 路径继续累积陈旧记录；即使新的计划快照为空，也会先清掉当前计划旧主记录，避免刷新后继续显示历史报名数据；同时有计划上下文的报名导入现在也会按 `planId` 读取 demo 快照再落库，不再把其他计划的报名行误混进当前计划；同一 `jobId` 的异常明细也已改为覆盖型快照，本轮导入已无异常时会先清掉旧失败明细；并且当 local 仓库已启用但当前计划导入后主记录仍为 `0` 条时，报名列表现在也会继续保持本地空状态，并回放当前批次异常风险，而不再回退 demo 报名数据；同时当前导入批次涉及报名记录的旧审核历史也会被同步清理，避免刷新后把上一轮审核轨迹继续挂到新批次；同时报名列表里的审核历史回放现已补 `RegistrationStore.findReviewHistories(...)` 批量边界，`RegistrationService` 不再逐条读取审核历史仓储；`RegistrationImportJobStore.findLatest(...)` 现在也已补 repository 查询边界，不再通过 `findAll().stream().filter(...)` 在 store 内做内存筛选；同时报名列表顶部的最近一次导入异常风险现在也已直接复用 import-job 的 `failedCount`，不再为了这一条风险文案额外读取异常明细仓储；`Scheduling` 现在也已开始在重算时主动淘汰同计划旧的场次分配快照，避免刷新后继续混入重算前的考场结果；另外当 local 仓库仅存在其他计划的编排/场次分配快照时，当前计划的编排页与场次页现在也会明确返回“先执行编排重算”的本地空状态语义，不再继续沿用 demo 任务、风险或场次数据；其中编排任务页在这类本地空状态下也会同步清空 demo 阶段条，避免继续显示演示进度；同时这两类本地空态现在也已直接由 `SchedulingJobSnapshotStore` / `SchedulingAllocationSnapshotStore` 返回空响应，`SchedulingService` 不再先读取 demo `getSchedulingTasks(...)` / `getSessionAllocation(...)` 再覆盖为空；当当前计划已有本地重算快照时，`SchedulingService.getSchedulingTasks(...)` 现在也会直接通过 `SchedulingJobSnapshotStore` 回放 `latestJob / stages / stats / risks / tasks / strategies` 主响应，不再先读取 demo `getSchedulingTasks(...)` 作为底板；对应的重算写路径也会把这些展示字段一并沉淀进 job snapshot 的 `resultSummary`，供刷新后直接回放；当前计划若已有带展示摘要的本地场次分配快照，`SchedulingService.getSessionAllocation(...)` 现在也会直接通过 `SchedulingAllocationSnapshotStore` 回放 `latestAllocation / stats / risks / sessions / rooms / rules` 主响应，不再先读取 demo `getSessionAllocation(...)` 作为底板；对应的保存写路径也会把这些展示字段一并沉淀进 allocation snapshot 的 `resultSummary`，供刷新后直接回放；同时 Spring 与 Node fallback 两条编排 / 场次接口链路现在也都会显式返回 `planContext`，前端刷新或深链接时可从真实接口恢复当前计划名称；本轮 `scheduling` 再抽出 `ExamPlanContextStore`，统一承接计划名称解析与 `planContext` 组装，`SchedulingService` 不再直接通过 `DemoBackendService.getExamPlanDetail` 聚合计划上下文；`Ticket` 现在也已开始在重新生成时主动淘汰同计划旧的发布回执，避免刷新后继续混入旧的已发布完成态；同时最近一次生成若仍有异常待处理，ticket 主链现在会把这条结果同时回放到 `risks + blocks`，并在 demo / local 两条路径下统一拦截发布；另外当 local 仓库仅存在其他计划的准考证记录、当前计划尚无本地生成快照时，ticket 列表与发布动作现在也会明确返回“先生成准考证”的本地阻断语义，不再继续沿用 demo 状态放行，并同步清空 demo 统计与准考证行数据；同时这类 ticket 本地空态现在也已直接由 `TicketGenerateJobStore` / `TicketPublishJobStore` 返回空响应，`TicketService` 不再先读取 demo `getTickets(...)` 再覆盖为空；当前计划若已有带展示摘要的本地生成 / 发布快照，`TicketService.listTickets(...)` 现在也会直接通过 `TicketGenerateJobStore` / `TicketPublishJobStore` 回放 `latestGenerateJob / latestPublishJob / stats / rows / risks / blocks / preview` 主响应，不再先读取 demo `getTickets(...)` 作为底板；对应的生成 / 发布写路径也会把这些展示字段一并沉淀进 job `resultSummary`，供刷新后直接回放；本轮 `TicketService` 进一步改为通过 `ExamPlanContextStore` 统一回填 ticket 列表里的 `planContext` 与 `preview.plan`，不再隐式依赖 `DemoBackendService.getTickets` 返回的计划名称；本轮再把 `publishTickets(...)` 的发布前阻断补到 job 快照边界，在“已发布 / 最近一次生成仍有异常 / 当前计划暂无本地生成快照”这些本地已足够判断的场景下，不再先重建整份 demo ticket state；`Score` 现在也已开始在重新导入时主动淘汰同计划旧的发布回执，并把同一 `jobId` 的导入异常明细改成覆盖型快照，避免刷新后继续混入旧的已发布完成态或历史失败项；同时当前导入批次涉及成绩的旧确认快照也会被同步清理，避免 review 页被历史人工确认结果重新覆盖；同时已补异常明细、复核说明、review 页 `blocks` 本地重建与发布前阻断校验；本轮 `ScoreService` 进一步改为通过 `ExamPlanContextStore` 统一读取 review 摘要里的计划名称，不再通过 `DemoBackendService.getScores` 回查计划上下文；同时 `ScoreStore` 又补了一层 `findLatestConfirmations(...)` 批量确认快照边界，review 列表的确认状态与复核意见回放不再由 `ScoreService` 逐条读取确认仓储；本轮再补 `ScoreStore.countByPlanId(...)` 与 `countByPlanIdAndConfirmStatus(...)` 计划维度计数边界，`publishScores(...)` 在本地 repository 路径下不再为了发布前阻断重建整份 review state 或回查 demo review 数据；`ScoreService.confirmScore(...)` 现在也会在本地仓储路径下优先直接通过 `ScoreStore` 落成绩确认结果与确认快照，并在记录缺失时返回本地 `404`，不再先调用 demo 确认接口再回写本地状态；`ScoreService.publishScores(...)` 现在也会在本地仓储路径下优先直接通过 `ScoreStore` 的计划维度计数边界组装发布结果，并将本地回执继续沉淀到 `ScorePublishJobStore`，不再先调用 demo `publishScores(...)` 再回写本地发布快照；`ScoreStore`、`ScoreImportJobStore` 与 `ScorePublishJobStore` 继续分别承接成绩主记录/异常明细/确认快照边界，以及导入 / 发布 job 快照与 Redis 进度键写入；`ScoreService` 不再直接持有 `ScoreRecordRepository`、`ScoreExceptionRepository`、`ScoreConfirmationRepository`、`ScoreImportJobRepository`、`ScorePublishJobRepository` 或 `JobProgressCache`；`master-data` 与 `auth` 现在也分别抽出 `MasterDataStore` 与 `AuthStore`，承接计划表单基础数据与 demo 用户目录/角色列表读取，`MasterDataService`、`AuthService` 不再直接持有 `DemoBackendService`；并且当 local 仓库已启用但当前计划导入后主记录仍为 `0` 条时，score 列表与复核页现在也会继续保持本地空状态，并回放当前导入批次异常，而不再回退 demo 成绩数据；另外当本地仓库只存在其他计划成绩时，score 列表与复核页现在也会明确返回当前计划空状态，不再继续混入 demo 考生详情或旧复核摘要；`Ticket` 发布前校验现在也已和本地回放后的列表状态对齐，不再出现“列表显示可发、动作仍按旧阻断拦截”的分叉；当前 backend service 层里基于 `ObjectProvider<...Repository>` 的直连已清空 | 下一步继续把 `ticket / score / registration` 剩余列表回放里仍偏厚的 service 聚合逻辑下沉到更清晰的 store / repository 边界，并检查 controller 侧是否还残留可下沉的聚合逻辑 |

本轮边界收口补充：`exam-plan` 已新增 `ExamPlanStore`，承接考试计划主记录查询、保存与报名人数聚合；`ExamPlanService` 继续负责列表/详情契约组装与发布结果回放，不再直接持有 `ExamPlanRepository` 或 `RegistrationRepository`。本轮 `exam-plan` 再新增 `ExamPlanContextStore`，承接计划名称解析与 `planContext` 组装，并优先复用本地 `ExamPlanStore`、再回退 demo 计划详情与静态占位名称；同时本地创建回执里的 `stats` 也已收口到 `ExamPlanStore.countByStatus(...)` 计数边界，不再为了返回草稿统计再经由 `listExamPlans(Map.of())` 回捞整份计划列表。`dashboard` 已新增 `DashboardOverviewStore`，承接工作台 `overview` 所需的计划数、待审核报名、待发布成绩和最新编排冲突聚合；`DashboardService` 不再直接持有 `ExamPlanRepository`、`RegistrationRepository`、`ScoreRecordRepository`、`SchedulingJobSnapshotRepository`。`registration` 已新增 `RegistrationStore` 与 `RegistrationImportJobStore`，前者承接报名主记录查询/保存、导入异常快照覆盖与审核历史落库，后者承接报名导入 job 快照查询、Mongo 持久化与 Redis `import-job:*` 进度键写入；本轮进一步改为通过 `ExamPlanContextStore` 承接计划上下文解析，`RegistrationService` 继续负责导入结果回放、报名列表过滤与审核响应契约组装，不再直接持有 `RegistrationRepository`、`RegistrationImportExceptionRepository`、`RegistrationReviewRecordRepository`、`RegistrationImportJobRepository`、`JobProgressCache`，也不再直接通过 `DemoBackendService.getExamPlanDetail` 聚合计划上下文。`scheduling` 已新增 `SchedulingJobSnapshotStore` 与 `SchedulingAllocationSnapshotStore`，分别承接编排重算 job 快照、场次分配快照的查询与保存；本轮进一步改为通过 `ExamPlanContextStore` 承接计划上下文解析，`SchedulingService` 继续负责契约组装与场次分配回放，不再直接持有 `SchedulingJobSnapshotRepository`、`SchedulingAllocationSnapshotRepository`、`JobProgressCache`，也不再直接通过 `DemoBackendService.getExamPlanDetail` 聚合计划上下文。`ticket` 已新增 `TicketGenerateJobStore` 与 `TicketPublishJobStore`，分别承接准考证生成 / 发布 job 快照查询、保存和 Redis 进度键写入；本轮进一步改为通过 `ExamPlanContextStore` 承接 ticket 列表里的 `planContext` 与 `preview.plan` 组装，`TicketService` 继续负责列表回放、发布前阻断判断与响应契约组装，不再直接持有 `TicketGenerateJobRepository`、`TicketPublishJobRepository`、`JobProgressCache`，也不再隐式依赖 demo `getTickets` 返回的计划名称；本轮再把 `generateTickets(...)` 的生成回执收口到 job 快照边界，在当前计划已存在带展示摘要的本地 `latestGenerateJob` 时，不再先调用 demo 生成接口再回写本地状态；同时当 `latestGenerateJob` 仍是旧式精简快照、而 demo ticket 底板缺席或为空时，`listTickets(...)` 现在也会直接回落到本地阻断态与计划预览，不再把 `preview / risks / blocks` 掉回空壳；`publishTickets(...)` 的发布前阻断也已收口到 job 快照边界，在“已发布 / 最近一次生成仍有异常 / 当前计划暂无本地生成快照”这些本地已足够判断的场景下，不再先重建整份 demo ticket state。`score` 已新增 `ScoreStore`、`ScoreImportJobStore` 与 `ScorePublishJobStore`，前者承接成绩主记录查询/保存、导入异常快照覆盖与最新确认记录回放，后两者分别承接成绩导入 / 发布 job 快照查询、保存和 Redis 进度键写入；本轮进一步改为通过 `ExamPlanContextStore` 承接 review 摘要里的计划名称解析，`ScoreService` 继续负责列表与复核页回放、确认动作落库、发布前阻断判断与响应契约组装，不再直接持有 `ScoreRecordRepository`、`ScoreExceptionRepository`、`ScoreConfirmationRepository`、`ScoreImportJobRepository`、`ScorePublishJobRepository`、`JobProgressCache`，也不再直接通过 `DemoBackendService.getScores` 回查计划上下文；同时 `ScoreStore` 再新增 `findLatestConfirmations(...)` 批量确认快照边界，`ScoreService` 的 review 行状态回放不再逐条查询确认仓储；本轮再补 `countByPlanId(...)` 与 `countByPlanIdAndConfirmStatus(...)` 计划维度计数边界，`publishScores(...)` 在本地 repository 路径下不再为了发布前阻断重建整份 review state；同时 `confirmScore(...)` 现在也会在本地仓储路径下优先直接通过 `ScoreStore` 落成绩确认结果与确认快照，并在记录缺失时返回本地 `404`，不再先调用 demo 确认接口再回写本地状态；`publishScores(...)` 现在也会在本地仓储路径下优先直接通过 `ScoreStore` 的计划维度计数边界组装发布结果，并将本地回执继续沉淀到 `ScorePublishJobStore`，不再先调用 demo 发布接口再回写本地快照。`auth` 与 `master-data` 也已分别新增 `AuthStore` 与 `MasterDataStore`，承接 demo 用户目录/角色列表读取，以及计划表单基础数据读取；`AuthService`、`MasterDataService` 不再直接持有 `DemoBackendService`。已用 `ExamPlanStoreTest`、`ExamPlanContextStoreTest`、`ExamPlanServiceRepositoryFallbackTest`、`DashboardOverviewStoreTest`、`DashboardServiceRepositoryTest`、`RegistrationStoreTest`、`RegistrationImportJobStoreTest`、`RegistrationServiceRepositoryTest`、`RegistrationServiceBoundaryTest`、`SchedulingJobSnapshotStoreTest`、`SchedulingAllocationSnapshotStoreTest`、`SchedulingServiceRepositoryTest`、`SchedulingServiceBoundaryTest`、`TicketGenerateJobStoreTest`、`TicketPublishJobStoreTest`、`TicketServiceRepositoryTest`、`TicketServiceBoundaryTest`、`ScoreStoreTest`、`ScoreImportJobStoreTest`、`ScorePublishJobStoreTest`、`ScoreServiceRepositoryTest`、`ScoreServiceBoundaryTest`、`AuthStoreTest`、`AuthServiceBoundaryTest`、`MasterDataStoreTest`、`MasterDataServiceBoundaryTest` 验证这些切片；当前 backend service 层里基于 `ObjectProvider<...Repository>` 的直连已清空，下一步优先继续把 `ticket / score / registration` 剩余列表回放里仍偏厚的 service 聚合逻辑下沉到更清晰的 store / repository 边界，并检查 controller 残留聚合逻辑。

## 阶段化推进计划

当前计划采用“明确任务先做、不明确任务后置”的演进模式推进。每个阶段只放可执行、可验证、边界清晰的任务。

### Phase 1：当前立即执行

目标：把当前主线从“已能跑通”继续收口到“可稳定联调、可验证、可继续扩展”。

#### Phase 1 收口边界

当前 `Phase 1` 开发时间偏长，核心原因不是“没有进展”，而是阶段内同时混入了 3 类工作：

1. 主链页面与接口打通
2. `local` 持久化与 `repository / job` 收口
3. Docker Compose、smoke、计划与治理文档补齐

这 3 类工作都合理，但如果继续放在同一个“持续进行中”的阶段里，会让 `Phase 1` 看起来一直只差一点。后续判断 `Phase 1` 是否完成，统一改按下面这组硬边界，而不是继续按“还能再补一点细节”无限延长：

| 收口项 | 退出标准 |
|---|---|
| 前后端主链可访问 | `docker compose -f docker-compose.local.yml up --build` 后，`frontend + backend + mysql + mongodb + redis` 五服务正常运行 |
| 页面主链可演练 | 工作台、考试计划、报名、编排、场次、准考证、成绩、成绩发布 9 个主链页面都可进入，且路由上下文不丢失 |
| 主路径可跑通 | 至少能完成“创建计划 -> 导入报名 -> 审核 -> 编排 -> 场次分配 -> 准考证生成/发布 -> 成绩导入 -> 成绩确认 -> 成绩发布”一次完整演练 |
| 刷新后一致性 | 主链关键结果在 `local` 路径下可查询、可保存、刷新后不回退 demo 固定值 |
| 失败路径基础可用 | 核心动作具备基础 `400 / 404 / 401 / 403` 行为，页面有阻断或失败提示，不出现“前端可点、后端才报错”的明显分叉 |
| 验证证据完整 | `demo` / `local` / `compose` smoke 记录存在且当前可复核，关键契约测试与治理测试保持通过 |
| 文档与计划同步 | `PLANS.md`、阶段状态文档、边界文档与当前代码一致，不再把旧 page-first 记录误当当前排期 |

#### Phase 1 明确不再承载的内容

为了避免阶段继续膨胀，下面这些内容不再作为 `Phase 1` 关闭前必须完成的事项：

- 生产级真实登录鉴权
- 外部题库、机考、监管平台真实对接
- 全量页面视觉精修与原型级像素对齐
- 一次性补完全部 MySQL / MongoDB / Redis 正式持久化实现
- 高可用、弹性、监控、备份、预发布环境体系

换句话说，`Phase 1` 的结束口径应是“本地可用系统主链闭环 + 本地部署可复核”，而不是“所有后续深化工作全部完成”。

#### Phase 1 执行任务

`Phase 1` 现在只承载考试计划主链本身的任务。原“统一后端模块边界”不再作为单个进行中任务存在，而是转为长期跟踪的 Epic；除 `exam-plan` 直接相关切片外，其余 `registration / scheduling / ticket / score / governance / smoke` 任务统一顺延到后续阶段。

| 任务 | 关联需求/文档 | 范围 | 退出标准 | 验证方式 | 状态建议 |
|---|---|---|---|---|---|
| `BE-BOUNDARY-001 exam-plan 查询 / 创建 / 发布边界收口` | [2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 只处理 `exam-plan`：查询筛选、创建落库、发布回执、`planContext` | `ExamPlanService` 不再为列表/详情/发布回执回读整份 demo 计划集；本地 `404`、筛选、发布后回放口径一致；创建后的计划刷新后仍保持本地真实状态 | `./gradlew test --tests "*ExamPlan*"` | 进行中 |
| `PERSIST-001 exam-plan 第一批持久化对象验收` | [system-architecture.md](docs/architecture/system-architecture.md), [database-design.md](docs/architecture/database-design.md) | 只验收 `exam-plan` 已落地的本地持久化对象、筛选条件与刷新回放 | 当前已声明的 `exam-plan` 筛选、创建、发布、容量/状态/时间字段回放在 local 路径下可复核；不再把“继续补更多对象”作为同一任务完成标准 | `./gradlew test --tests "*ExamPlan*"` | 进行中 |

#### Phase 1 历史完成记录

这些事项保留为历史进展，但不再作为当前 `Phase 1` 执行面的一部分：

| 任务 | 当前结论 |
|---|---|
| 收口 score / scheduling / ticket 服务边界 | 已完成 `ScoreController`、`SchedulingController`、`TicketController` 从 demo 直连前推到 service 门面，并补齐计划上下文传递与回归测试 |
| 补强 `FEAT-009-score-publish` | 已完成发布时间、结构化阻断项 / 风险提示、风险确认勾选与完成态反馈补强 |

#### 后端模块边界治理 Epic / Theme

原 `Phase 1` 的“统一后端模块边界”调整为后端架构治理 Epic，不再按单任务追踪关闭。它的职责是把已经落下的 service / store / repository / job 分工持续整理为可演进的结构；真正进入执行面的，只是下面这些单模块切片。

已保留的历史进展摘要：

- `auth`、`master-data`、`dashboard` 已抽出独立 store，基础聚合不再直接卡在 service 对 demo 或 repository 的混合读取上。
- `exam-plan` 已抽出 `ExamPlanStore` 与 `ExamPlanContextStore`，创建、查询、发布与计划上下文开始按本地持久化边界回放。
- `registration`、`scheduling`、`ticket`、`score` 已形成对应 job/store 边界，并补过一轮 repository 级测试与 service 回归测试。
- backend service 层基于 `ObjectProvider<...Repository>` 的直连已清空；后续工作重点从“大范围重构”改成“按模块收口单点残留”。

| Epic 切片 | 范围 | 退出标准 | 验证方式 | 状态建议 |
|---|---|---|---|---|
| `BE-BOUNDARY-001 exam-plan 查询 / 创建 / 发布边界收口` | `exam-plan` 查询筛选、创建落库、发布回执、`planContext` | `ExamPlanService` 不再为列表/详情/发布回执回读整份 demo 计划集；本地 `404`、筛选、发布后回放口径一致 | `./gradlew test --tests "*ExamPlan*"` | Phase 1 执行中 |
| `BE-BOUNDARY-002 dashboard overview 聚合边界收口` | `dashboard overview` 里的计划数、待审核报名、待发布成绩、编排冲突聚合 | overview 四类统计全部走明确的 store/repository 聚合；空仓库不回退 demo 统计 | `./gradlew test --tests "*Dashboard*"` | 已完成首轮，后续按缺陷单补 |
| `BE-BOUNDARY-003 registration 导入 job 与主记录边界收口` | `registration` 导入 job、异常明细、审核记录与主记录回放 | 导入 job 查询、最近一次导入风险、审核历史回放全部走既定 store/repository 边界；相关计划刷新后不混入 demo 记录 | `./gradlew test --tests "*Registration*"` | Phase 2 后置 |
| `BE-BOUNDARY-004 scheduling 重算 job 与场次快照边界收口` | `scheduling` 重算 job、场次分配快照、空上下文与 `planContext` 回放 | 当前计划无本地快照时明确空状态；已有快照时直接回放 `latestJob / latestAllocation / nextActions`，不再混入 demo 任务或旧场次 | `./gradlew test --tests "*Scheduling*"` | Phase 2 后置 |
| `BE-BOUNDARY-005 ticket 生成 / 发布 job 边界收口` | `ticket` 生成/发布 job、预览摘要、发布前阻断与回执回放 | 生成/发布后刷新能稳定回放 `stats / rows / risks / blocks / preview / latest*Job`；阻断语义与动作接口一致 | `./gradlew test --tests "*Ticket*"` | Phase 2 后置 |
| `BE-BOUNDARY-006 score 导入 / 确认 / 发布边界收口` | `score` 导入 job、确认快照、发布回执、review 摘要与计划维度计数 | 当前计划的导入、确认、发布结果刷新后可稳定回放且缺失记录返回本地 `404`；review 页状态与发布动作口径一致 | `./gradlew test --tests "*Score*"` | Phase 2 后置 |
| `BE-BOUNDARY-007 auth / master-data 轻量边界收口` | `auth`、`master-data` 的 demo 目录读取、计划表单基础数据 | service 不再直接持有 `DemoBackendService`；基础用户/角色/表单数据来源边界清晰且测试覆盖在位 | `./gradlew test --tests "*Auth*" --tests "*MasterData*"` | 已完成首轮，后续按缺陷单补 |
| `BE-BOUNDARY-008 controller 聚合残留扫描与修正` | controller 层残留的跨模块聚合、demo 直连和厚响应拼装 | 列出 controller 残留清单并收掉本轮确认范围内的问题；剩余残留项明确归属哪个 Epic 切片 | `rg -n "DemoBackendService|ObjectProvider|Repository" backend/src/main/java/**/controller backend/src/main/java/**/web` | Phase 2 后置 |

#### 其他过大任务的拆分建议

下面这些描述也偏大，不再建议继续以“单任务进行中”方式挂在 `Phase 1`：

| 原任务 | 调整建议 | 推荐去向 |
|---|---|---|
| 落第一批真实持久化对象 | 拆成 `PERSIST-001 exam-plan`、`PERSIST-002 registration`、`PERSIST-003 scheduling / ticket job snapshots`、`PERSIST-004 score confirmation / publish receipts` | `PERSIST-001` 留在 Phase 1；其余进入 Phase 2 或缺陷驱动推进 |
| 收口 demo 与 local 双环境验证链路 | 拆成 `VERIFY-LOCAL-001 exam-plan smoke`、`VERIFY-LOCAL-002 全主链 smoke`、`VERIFY-LOCAL-003 compose 健康检查`、`VERIFY-LOCAL-004 失败路径记录` | 仅 `VERIFY-LOCAL-001` 可视进度进入 Phase 1；其余转 Phase 2 |
| 持续新增需求改为 story-first | 改成治理抽查任务与模板守护任务，不再作为无限期开发任务 | 治理项留长期，默认不占用 Phase 1 |

### Phase 2：主链增强与体验收口

目标：在主链已通的基础上，补强关键页面的异常态、阻断逻辑和原型对齐度。

| 任务 | 关联需求/文档 | 目标 | 完成标准 | 前置依赖 | 状态 |
|---|---|---|---|---|---|
| `BE-BOUNDARY-003 registration 导入 job 与主记录边界收口` | [2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 收口 `registration` 导入 job、异常明细、审核记录与主记录读取边界 | 导入 job 查询、最近一次导入风险、审核历史回放全部走既定 store/repository 边界；相关计划刷新后不混入 demo 记录 | Phase 1 的 `exam-plan` 主链先稳定 | 后置 |
| `BE-BOUNDARY-004 scheduling 重算 job 与场次快照边界收口` | [2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 收口 `scheduling` 重算 job、场次分配快照与空上下文回放边界 | 当前计划无本地快照时明确空状态；已有快照时直接回放 `latestJob / latestAllocation / nextActions` | 考试计划主链与计划上下文稳定 | 后置 |
| `BE-BOUNDARY-005 ticket 生成 / 发布 job 边界收口` | [2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 收口 `ticket` 生成/发布 job、预览摘要、发布前阻断与回执回放边界 | 生成/发布后刷新能稳定回放 `stats / rows / risks / blocks / preview / latest*Job`；阻断语义与动作接口一致 | 上游考试计划与编排上下文稳定 | 后置 |
| `BE-BOUNDARY-006 score 导入 / 确认 / 发布边界收口` | [2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 收口 `score` 导入 job、确认快照、发布回执、review 摘要与计划维度计数边界 | 当前计划的导入、确认、发布结果刷新后可稳定回放且缺失记录返回本地 `404`；review 页状态与发布动作口径一致 | 上游考试计划主链稳定 | 后置 |
| `BE-BOUNDARY-008 controller 聚合残留扫描与修正` | [2026-06-10-backend-mainline-hardening.md](docs/process/ai/plans/2026-06-10-backend-mainline-hardening.md) | 扫描 controller 层残留的跨模块聚合、demo 直连和厚响应拼装 | 列出 controller 残留清单并收掉本轮确认范围内的问题；剩余残留项明确归属哪个 Epic 切片 | Phase 1 的 `exam-plan` 切片先收口 | 后置 |
| `PERSIST-002 registration 第一批持久化对象验收` | [system-architecture.md](docs/architecture/system-architecture.md), [database-design.md](docs/architecture/database-design.md) | 验收 `registration` 已落地的本地持久化对象与刷新回放 | 报名导入、审核、风险回放在 local 路径下可复核 | `exam-plan` 持久化验收完成 | 后置 |
| `VERIFY-LOCAL-002 全主链 smoke` | [deployment.md](docs/architecture/deployment.md), [2026-06-10-backend-mainline-hardening.md](docs/process/ai/plans/2026-06-10-backend-mainline-hardening.md) | 固化非考试计划模块的主链 smoke 证据 | 报名、编排、准考证、成绩链路留有可复核的 `demo / local / compose` 记录 | Phase 1 的考试计划 smoke 先完成 | 后置 |
| `VERIFY-LOCAL-003 compose 健康检查` | [deployment.md](docs/architecture/deployment.md) | 继续收口 docker compose 下的健康检查与访问性记录 | 前端首页、代理 API、后端直连、依赖服务健康状态都可复核 | Phase 1 的考试计划主链先稳定 | 后置 |
| `VERIFY-LOCAL-004 失败路径记录` | [deployment.md](docs/architecture/deployment.md) | 固化 `400 / 404 / 401 / 403` 失败路径样例 | 关键动作的失败返回与页面阻断存在记录 | 主链 smoke 已稳定 | 后置 |
| `GOV-PLAN-001 story-first 需求入口抽查` | [TECH-016-story-first-demand-definition](references/Harness/changes/v0.1.0/TECH-016-story-first-demand-definition/change.md), [TECH-015-harness-lightweight-governance](references/Harness/changes/v0.1.0/TECH-015-harness-lightweight-governance/change.md) | 检查近期新增需求与变更记录是否继续误回 page-first | 近期待办入口、Harness 变更目录、计划文档中不再把页面名直接当默认需求切分单位 | Phase 1 收口后执行 | 后置 |
| 补强 `FEAT-005-scheduling-tasks` | [FEAT-005-scheduling-tasks](references/Harness/changes/v0.1.0/FEAT-005-scheduling-tasks) | 让编排任务页不只是“能看能点”，而是能表达阻断和建议 | 风险、阻断、重算反馈、完成后续动作更完整 | Phase 1 基础持久化与验证链路稳定 | 后置 |
| 补强 `FEAT-006-session-allocation` | [FEAT-006-session-allocation](references/Harness/changes/v0.1.0/FEAT-006-session-allocation) | 强化冲突处理、推荐分配和状态语义 | 资源冲突、推荐结果、确认后反馈更完整 | 编排任务与资源边界更清晰 | 后置 |
| 补强 `FEAT-007-ticket-publish` | [FEAT-007-ticket-publish](references/Harness/changes/v0.1.0/FEAT-007-ticket-publish) | 强化生成与发布门槛控制 | 生成前校验、最近一次生成回放、发布阻断项、发布完成态明确 | 上游编排与场次状态更稳定 | 后置 |
| 补强 `FEAT-008-score-list` | [FEAT-008-score-list](references/Harness/changes/v0.1.0/FEAT-008-score-list) | 强化成绩链路中的异常态和信息密度 | 异常记录、来源、状态、详情和原型层次更贴近业务 | 成绩主状态和导入批次结构更稳定 | 后置 |
| Vue 主线页面继续对齐原型 | [COVERAGE.md](references/Harness/changes/v0.1.0/COVERAGE.md), [docs/product/07-screen-specs.md](docs/product/07-screen-specs.md), [docs/product/08-screen-specs-batch-2.md](docs/product/08-screen-specs-batch-2.md) | 从“功能已通”继续收口到“信息表达准确” | 关键页面版式、层级、状态表达与原型主稿偏差减少 | 页面主链先稳定 | 后置 |

### Phase 3：高不确定性与外部依赖项

目标：把当前尚不清晰、依赖外部条件或不属于现阶段最优先主线的事项明确后置。

| 任务 | 关联需求/文档 | 目标 | 完成标准 | 前置依赖 | 状态 |
|---|---|---|---|---|---|
| 外部系统真实联通 | [api-design.md](docs/architecture/api-design.md), [frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md) | 推进题库、机考、监管平台真实对接 | 外部协议、网络、补偿和幂等策略确认 | 主链本地可用系统先稳定 | 后置 |
| 生产级鉴权与权限体系 | [AGENTS.md](AGENTS.md), [system-architecture.md](docs/architecture/system-architecture.md) | 从开发态身份能力升级到正式安全体系 | 登录、会话、安全边界、权限模型明确 | 当前阶段非目标结束后再进入 | 后置 |
| 测试 / 预发布 / 生产环境方案 | [deployment.md](docs/architecture/deployment.md) | 从本地部署延伸到环境体系 | 环境变量、部署编排、备份回滚和监控方案清晰 | 本地拓扑和主链持久化稳定 | 后置 |
| 文件存储与大文件导入导出 | [database-design.md](docs/architecture/database-design.md), [deployment.md](docs/architecture/deployment.md) | 明确附件、照片、证书和导出机制 | 存储方案、容量策略和失败恢复方案清晰 | 主链业务对象稳定 | 后置 |

## 当前阶段判断原则

下面这类任务优先进入当前阶段：

- 边界明确。
- 能形成独立验证结果。
- 能直接提升主链真实可用性。
- 不依赖外部系统最终协议。

下面这类任务默认后置：

- 依赖外部系统正式协议或网络条件。
- 业务规则仍未收敛。
- 没有最小可用验收口径。
- 会显著拉长当前主线闭环时间，但不能立即提升可用性。

## 当前有效计划入口

优先阅读以下计划与设计文档：

1. 当前主线设计
   [docs/process/ai/specs/2026-06-09-frontend-separation-vue3-design.md](docs/process/ai/specs/2026-06-09-frontend-separation-vue3-design.md)

2. 当前主线实施计划
   [docs/process/ai/plans/2026-06-09-frontend-separation-vue3.md](docs/process/ai/plans/2026-06-09-frontend-separation-vue3.md)

3. 当前后端深化设计
   [docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md](docs/process/ai/specs/2026-06-10-backend-mainline-hardening-design.md)

4. 当前后端深化实施计划
   [docs/process/ai/plans/2026-06-10-backend-mainline-hardening.md](docs/process/ai/plans/2026-06-10-backend-mainline-hardening.md)

   这是当前后端深化、真实持久化边界继续收敛和本地联调增强的执行入口。

5. 当前系统技术架构总览
   [docs/architecture/system-architecture.md](docs/architecture/system-architecture.md)

6. 当前前后端边界
   [docs/architecture/frontend-backend-boundary-2026-06-07.md](docs/architecture/frontend-backend-boundary-2026-06-07.md)

## 当前需求入口

如果要判断“现在到底该做什么需求”，按下面顺序读：

1. 根 [AGENTS.md](AGENTS.md)
2. [references/Harness/QUICKSTART.md](references/Harness/QUICKSTART.md)
3. [docs/README.md](docs/README.md)
4. [references/Harness/changes/v0.1.0/MAINLINE-PRIORITIES.md](references/Harness/changes/v0.1.0/MAINLINE-PRIORITIES.md)
5. [references/Harness/changes/v0.1.0/COVERAGE.md](references/Harness/changes/v0.1.0/COVERAGE.md)

## 当前主线优先级

当前最优先的正式需求主线：

- `FEAT-014-frontend-separation-vue3`
- `FEAT-015-registration-management-vue3`
- `FEAT-017-exam-plan-draft-closure`

当前最优先的后端深化主线：

- Spring Boot 主链从 `DemoBackendService` 过渡到可扩展的 `Controller -> Application Service -> Repository/Job` 结构
- 先落 MySQL / MongoDB / Redis 的职责边界与本地 `local` profile 联调能力
- 优先做真实保存、任务进度、异常处理和权限失败路径，而不是继续增加单纯演示接口

当前最优先的治理支撑项：

- `FEAT-001-governance-refactor`
- `TECH-015-harness-lightweight-governance`
- `TECH-016-story-first-demand-definition`

早期 `FEAT-002` 到 `FEAT-013` 继续保留，但默认作为历史页面映射资料与原型覆盖档案，不再作为唯一开发排期基线。

## 当前最近执行顺序

当前建议直接按下面顺序执行：

1. 完成 `BE-BOUNDARY-001 exam-plan 查询 / 创建 / 发布边界收口`
2. 完成 `PERSIST-001 exam-plan 第一批持久化对象验收`
3. 复核考试计划主链在 local 路径下的筛选、创建、发布与刷新一致性
4. Phase 1 关闭后，再进入 `registration / scheduling / ticket / score` 等后续模块

## 历史说明

旧版 `PLANS.md` 中记录的 “Stage 1 - Stage 6” 勾选内容，已经完成其历史作用：

- 它描述的是仓库从无框架状态进入前端 MVP 壳子阶段的路径
- 它不再代表当前有效主线计划
- 后续如果需要追溯该阶段，应以 git 历史和对应的 `changes/` 目录为准
