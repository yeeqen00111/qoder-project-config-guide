# REFERENCE —— Qoder 项目级配置 10 项权威速查

> 本文件是 `qoder-config-init` skill 的知识底座。**生成任何一项前，先读对应小节，照抄路径与 frontmatter 格式，不要凭记忆编造字段。**
> 核验来源：docs.qoder.com 官方文档（2026-09）。Qoder 迭代快，字段可能随版本变化；若用户环境格式不同，以「先用 UI 建一条同类规则/命令再打开文件照抄」为准。

## A. 全局事实

### A.1 路径速查

| 项 | 路径 | 提交 Git |
|---|---|---|
| AGENTS.md | `<项目根>/AGENTS.md` | ✅ |
| rules | `<项目根>/.qoder/rules/<名>.md` | ✅ |
| settings.json | `<项目根>/.qoder/settings.json` | ✅ |
| settings.local.json | `<项目根>/.qoder/settings.local.json` | ❌ 必须 gitignore |
| skills | `<项目根>/.qoder/skills/<名>/SKILL.md` | ✅ |
| commands | `<项目根>/.qoder/commands/<名>.md` | ✅ |
| agents | `<项目根>/.qoder/agents/<名>.md` | ✅ |
| workflows | `<项目根>/.qoder/workflows/<名>.js` | ✅ |
| .mcp.json | `<项目根>/.mcp.json` | ✅（需脱敏） |
| output-styles | `<项目根>/.qoder/output-styles/<名>.md` | ✅ |

### A.2 同名覆盖方向（各类型不一致，易错）

- settings 配置值：本地级 > 项目级 > 用户级（逐字段深度合并）
- commands：**用户级 > 项目级**
- output-styles：**项目级 > 用户级** > 插件 > 内置
- workflows：项目级 > 插件级 > 内置
- skills：官方 IDE/CLI 表述矛盾，实践中避免同名

### A.3 前提与硬性限制

- **文件夹信任**：项目级/本地级配置仅在工作目录被信任时加载（`security.folderTrust.enabled` 默认开）；未信任时只读用户级配置。队友首次克隆需先信任目录。
- rules 全部活跃文件合计 ≤ 100,000 字符（超出截断）；仅支持自然语言，不支持图片/链接。
- SKILL.md：`name` ≤64 字符（仅小写字母/数字/连字符，与目录名一致）、`description` ≤1024 字符。
- workflows 脚本不能直接访问 shell/fs/网络/MCP，副作用只能经它启动的子 Agent 发生。
- output-styles 只叠加表达偏好，不能替换/绕过安全约束；改 `outputStyle` 需重启。
- AGENTS.md 与 rules 内容冲突时，**rules 优先**。

---

## B. 逐项模板（照抄格式，替换 `<占位符>`）

### 1. AGENTS.md（项目根）

纯 Markdown，无需注册（settings 的 `context.fileName` 默认值即它）。必含：简介 / 技术栈 / 启动命令 / 测试命令 / 目录结构；强烈建议：约束红线。精简为佳（≤60 行）。生成技巧：会话执行 `/init` 让 Qoder 自动分析生成再人工修订。

````markdown
# AGENTS.md —— 项目说明

## 项目简介
<一句话说清项目是什么>

## 技术栈
<语言 + 框架 + 关键依赖>

## 常用命令
```powershell
<安装依赖命令>
<启动命令>
<测试命令>
```

## 目录结构
- `<入口文件>`    <作用>
- `<目录>/`       <作用>

## 约束红线（必须遵守）
- <最重要的业务/安全约束>
- <明确禁止的事项>
````

### 2. rules（`.qoder/rules/<名>.md`）

Markdown + YAML frontmatter。四种 `trigger`（下划线写法），一文件一主题：

**始终生效**
```markdown
---
trigger: always_on
---

# <规则标题>

- <要 / 不要 条款>
```

**模型决策**（需 `description`）
```markdown
---
trigger: model_decision
description: <什么任务场景下启用本规则>
---

# <规则标题>

- <条款>
```

**指定文件生效**（需 `glob`）
```markdown
---
trigger: glob
glob: **/*.py
---

# <规则标题>

- <条款>
```

**手动引入**（会话中 `@规则名`）
```markdown
---
trigger: manual
---

# <规则标题>

<内容>
```

### 3. settings.json（`.qoder/settings.json`）

JSON，支持 `//` 注释，值可引用环境变量。常用块：`permissions`(allow/ask/deny)、`hooks`、`model`、`language`(顶层)、`mcp`、`context.fileName`、`outputStyle`(顶层)、`security`。起步模板（权限白名单 + deny 危险操作）：

```json
{
  "language": "Chinese",
  "permissions": {
    "allow": ["Read", "Glob", "Grep", "Bash(<测试命令>)"],
    "ask": ["Bash(git commit*)"],
    "deny": ["Bash(rm *)", "Bash(git push --force*)", "Read(.env)"]
  }
}
```

权限 / MCP 类改动需重启会话生效。Hooks 四种条目：`command`/`http`/`prompt`/`agent`；脚本经 stdin 收 JSON、用 exit code 控制（`0` 放行、`2` 阻塞），Windows 下可加 `"shell": "powershell"`。

### 4. settings.local.json（`.qoder/settings.local.json`）

与 settings.json 同构，仅本机生效、优先级最高。**创建后立即加入 .gitignore**。只写要覆盖的字段（逐层合并，不必全量复制）。

```json
{ "permissions": { "allow": ["Bash(<本地调试命令>)"] } }
```

`.gitignore` 追加一行：`.qoder/settings.local.json`

### 5. skills（`.qoder/skills/<名>/SKILL.md`）

frontmatter 两个必填字段：

```markdown
---
name: <小写-连字符，≤64，与目录名一致>
description: <功能 + 何时使用 + 触发关键词，≤1024>
---

# <技能标题>

## 工作流程
1. <步骤>

## 约束
- <防错条款>
```

可选辅助文件：`REFERENCE.md` / `scripts/` / `templates/`（渐进式披露：主文件精简，细节外置按需加载）。`description` 是触发关键——写清「何时用 + 关键词」，模糊描述永不被调用。加载：重启会话或 CLI `/skills reload`；触发：描述需求或 `/<技能名>`。

### 6. commands（`.qoder/commands/<名>.md`）

frontmatter：`description` **必填**，`name` 可选（仅 TUI 展示，**调用名由文件路径决定**）。子目录即命名空间（`git/commit.md` → `/git:commit`）。

```markdown
---
description: <功能，显示在命令清单>
---

<命令被触发时注入的系统提示词 / 任务步骤>
```

命名小写 + 连字符。同名时**用户级覆盖项目级**。触发：`/<命令名>`；CLI 中 `/commands` 重载。

### 7. agents（`.qoder/agents/<名>.md`）

frontmatter：`name`/`description` 必填；`model`/`tools`/`skills`/`mcpServers` 可选。

```markdown
---
name: <智能体名>
description: <专长 + 何时委派，供模型自动选择>
tools: Read, Grep, Glob, Bash
---

<该 Agent 的系统提示词：角色、审查/工作清单、输出格式>
```

`tools` 取值：`Bash`/`Edit`/`Write`/`Glob`/`Grep`/`Read`/`WebFetch`/`WebSearch`（不给则继承）。推荐用内置 `/create-agent <诉求>` 交互生成。触发：自然语言（模型按 description 自动选）或 `/<智能体名>`。

### 8. workflows（`.qoder/workflows/<名>.js`）

JavaScript，以 `export const meta` 开头声明元信息：

```javascript
export const meta = {
  name: "<名>",
  description: "<做什么>",
  whenToUse: "<何时该用，供模型判断>",
  phases: [
    { title: "<阶段1>", detail: "<说明>" },
    { title: "<阶段2>", detail: "<说明>" }
  ]
};

// 正文用辅助函数编排：agent() / parallel() / pipeline() / phase() / log() / workflow() / args
```

`args` 接收每次运行的入参。副作用只能经子 Agent 发生（脚本本身无 shell/fs/网络权限）。触发：自然语言或按名调用；TUI 中 `/workflows` 看阶段/日志面板。同名时项目级 > 插件级 > 内置。

### 9. .mcp.json（项目根）

顶层 `mcpServers` 对象，每个服务器一个键。STDIO（本地进程）或 SSE（远程服务）：

```json
{
  "mcpServers": {
    "<远程服务名>": { "type": "sse", "url": "<https://...>" },
    "<本地工具名>": {
      "command": "npx",
      "args": ["-y", "<包名>"],
      "env": { "<TOKEN_KEY>": "${<ENV_VAR>}" }
    }
  }
}
```

密钥纪律：token 用 `${ENV_VAR}` 占位，真值放 `.env`（不提交）。批准：项目级 MCP 首次使用需批准，记入 settings 的 `mcp.enabledProjectMcpServers`；团队信任场景可设 `mcp.enableAllProjectMcpServers: true` 自动放行。

### 10. output-styles（`.qoder/output-styles/<名>.md`）

可选 frontmatter（`name`/`description`），正文即叠加进系统提示的风格说明：

```markdown
---
name: <风格名，缺省用文件名>
description: <描述，缺省取正文首个非标题行>
---

<表达偏好：语气 / 详略 / 结构，保持精简>
```

启用：在 settings.json 写顶层 `"outputStyle": "<风格名>"`，**重启生效**；临时用可启动加 `--output-style <名>`（仅本次会话，优先级更高，免重启）。只描述表达层，别试图覆盖安全约束。同名时项目级 > 用户级 > 插件 > 内置。
