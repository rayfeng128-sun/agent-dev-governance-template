# CODEX.md

本目录作为<项目名称>仓库在 Codex 中的治理入口使用。

执行顺序如下：

1. 先阅读 [references/Harness/QUICKSTART.md](QUICKSTART.md)
2. 若命中完整闭环升级条件，再阅读仓库根目录 [AGENTS.md](../../AGENTS.md)
3. 再阅读 [references/Harness/AGENTS.md](AGENTS.md)
4. 根据任务类型进入 `rules/`、`agents/`、`skills/` 和 `changes/`

当前仓库使用的是“适配型 Harness”：

- 保留 Harness 的结构、门禁和变更闭环
- 当前主线为 `frontend/ Vue 3 + backend/ Spring Boot`
- `app/` 与 `server/` 仅作为 legacy fallback
- 不将本治理目录视为强制迁移技术栈的依据
