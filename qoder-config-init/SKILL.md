---
name: qoder-config-init
description: 引导用户在其自己的项目中按需创建 Qoder 的 10 项项目级配置（AGENTS.md、rules、settings.json、settings.local.json、skills、commands、agents、workflows、.mcp.json、output-styles）。当用户想初始化/搭建/配置 Qoder 项目、询问"该准备哪些 .qoder 配置"、要求生成 AGENTS.md 或编码规则或权限白名单或 MCP 接入、或说"帮我配置 Qoder / 初始化项目配置"时触发。也可用 /qoder-config-init 手动唤起。
---

# Qoder 项目配置初始化向导

帮助用户在**他们自己的项目**里，按需生成 Qoder 的项目级配置。你的职责不是替用户把 10 项全建出来，而是：先访谈 → 推荐起步集 → 按需求增量补齐 → 收尾校验。

## 铁律（务必遵守）

1. **写到用户当前项目，不是写到本 skill 目录**。所有路径都相对用户项目根目录。
2. **生成任何一项前，先读同目录 `REFERENCE.md` 的对应小节**，照抄其中的路径与 frontmatter 格式。严禁凭记忆编造字段名或取值（例如 rules 的 `trigger` 取值、agent 的字段、workflow 的 meta 结构）。
3. **一次聚焦一项**：展示将写入的内容 → 用户确认 → 写文件 → 一句话说明如何验证。
4. 目录约定：`AGENTS.md` 与 `.mcp.json` 在**项目根**；其余 8 项在 `.qoder/` 下对应子目录（rules/skills/commands/agents/workflows/output-styles，以及 settings.json、settings.local.json）。

## 工作流程

### 第 1 步 · 访谈（最多 3 问，能推断就别问）

1. 项目主要语言 / 技术栈？→ 决定 rules 的 `glob` 与示例语言。
2. 个人项目还是团队协作？→ 团队建议提交 `settings.json`；个人可只用 `settings.local.json`。
3. 现在最想解决什么？（统一代码风格 / 加权限护栏 / 接外部工具 / 固化某类任务流程 / 统一回复风格）

> 若能从项目现状（已有文件、语言）推断答案，直接说明你的假设并继续，不要逐条盘问。

### 第 2 步 · 推荐"起步三件套"

除非用户另有要求，先建这三项（覆盖约 80% 价值），每项先展示内容再写：

1. `AGENTS.md` —— 项目说明书（技术栈 / 启动命令 / 测试命令 / 目录结构 / 约束红线）
2. `.qoder/rules/<lang>-style.md` —— 一条 `trigger: always_on` 的编码规范
3. `.qoder/settings.json` —— 权限白名单（`allow` 常用只读与测试命令）+ `deny` 危险操作（`rm`、`git push --force`、读 `.env`）

### 第 3 步 · 按需增量

依据第 1 步的诉求，从其余 7 项里挑用户真正需要的，用 `REFERENCE.md` 模板生成：

- 高频固定任务 → `.qoder/commands/`
- 可复用专业流程 → `.qoder/skills/`
- 独立上下文的专家角色 → `.qoder/agents/`
- 多阶段/并行大任务 → `.qoder/workflows/`
- 统一回复语气详略 → `.qoder/output-styles/`
- 接外部系统（网页/DB/API）→ `.mcp.json`
- 个人本机覆盖（不提交）→ `.qoder/settings.local.json`

不要为了"凑满 10 项"而生成用户用不到的配置。

### 第 4 步 · 收尾

1. 提醒把 `.qoder/settings.local.json`、`.env` 加入 `.gitignore`。
2. 提示**文件夹信任**：项目级配置仅在工作目录被信任时才加载；队友首次克隆需先信任目录。
3. 输出**已创建文件清单**（路径 + 一句话作用），并给出验证方式（新开一个会话，测试对应能力是否生效）。

## 触发方式

- 手动：输入 `/qoder-config-init`
- 自动：用户说"帮我初始化 Qoder 项目配置""这个项目该配哪些 .qoder""给我加一条编码规则/权限白名单"等，模型据 description 自动加载本 skill。
