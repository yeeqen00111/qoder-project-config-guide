# Qoder 项目级配置完全教程

> **核验来源**：本教程逐项对照 Qoder 官方文档（docs.qoder.com）核验整理，涉及十一篇：规则（Rules）、配置作用范围、配置项参考（settings-reference）、技能（Skills）、命令（Commands）、钩子（Hooks）、MCP、自定义智能体（Subagent）、动态工作流（Workflows）、输出风格（Output Styles）、插件（Plugins）。
> **核验时间**：2026-09。Qoder 迭代较快，目录与字段约定可能随版本变化，请以官方文档为准。

---

## 0. 自检结论与全景地图

### 0.1 完整性自检

结论：用 Qoder 开发项目需要准备的项目级配置共 **10 项**，另有 1 项高级打包机制（插件）单列，以及若干自动生成内容明确无需准备：

| # | 配置项 | 覆盖情况 | 官方核验依据 |
|---|--------|----------|--------------|
| 1 | `AGENTS.md` | ✅ 见第 1 节 | 规则文档「AGENTS.md 兼容性」；settings 的 `context.fileName` 默认值 |
| 2 | `.qoder/rules/` | ✅ 见第 2 节（4 种触发类型） | 规则文档 |
| 3 | `.qoder/settings.json` | ✅ 见第 3 节（权限 / Hooks / 模型 / MCP 批准） | 配置项参考 + Hooks 文档 |
| 4 | `.qoder/settings.local.json` | ✅ 见第 4 节 | 配置作用范围文档 |
| 5 | `.qoder/skills/` | ✅ 见第 5 节（SKILL.md 规范） | Skills 文档 |
| 6 | `.qoder/commands/` | ✅ 见第 6 节 | 命令文档 |
| 7 | `.qoder/agents/` | ✅ 见第 7 节（自定义子智能体） | 自定义智能体文档 |
| 8 | `.qoder/workflows/` | ✅ 见第 8 节（动态工作流） | 动态工作流文档 |
| 9 | `.mcp.json` | ✅ 见第 9 节（STDIO / SSE 两种传输） | MCP 文档 |
| 10 | `.qoder/output-styles/` | ✅ 见第 10 节（回复表达风格） | 输出风格文档 |

> **插件（Plugins）**：把上述能力打包分发、可 Project 级安装的机制，属高级可选项，见第 11 节——日常开发不必准备，约定多到需整体分发时才升级。

**明确排除（自动生成，不要手动创建）**：

- `.qoder/worktrees/` —— `--worktree` 创建的隔离工作树
- `.qoder/scheduled_tasks.json` —— 定时任务定义
- `.qoder/sessions/` —— 动态工作流运行的脚本/日志/输出
- 自动记忆目录 —— 存于用户目录 `~/.qoder/projects/<项目>/memory/`，不在项目内

### 0.2 目录全景

```
<项目根目录>/
├── AGENTS.md                      # ① 项目说明书（AI 进项目先读它）
├── .mcp.json                      # ⑨ 外部工具接入（MCP 服务器）
└── .qoder/
    ├── rules/                     # ② 编码规则（约束 AI 怎么写代码）
    ├── skills/                    # ⑤ 专业技能（教 AI 怎么干一类事）
    ├── commands/                  # ⑥ 自定义斜杠命令（一键唤起固定任务）
    ├── agents/                    # ⑦ 自定义子智能体（独立上下文的专家 Agent）
    ├── workflows/                 # ⑧ 动态工作流（多 Agent 编排脚本）
    ├── output-styles/             # ⑩ 输出风格（回复的表达偏好）
    ├── settings.json              # ③ 团队共享设置 → 提交 Git
    └── settings.local.json        # ④ 个人本地覆盖 → 不提交
```

### 0.3 十项速览

| 配置 | 一句话定位 | 提交 Git？ | 生效方式 |
|---|---|---|---|
| AGENTS.md | 告诉 AI「项目是什么、怎么跑」 | ✅ | 所有会话自动加载 |
| .qoder/rules/ | 约束 AI「代码必须怎么写」 | ✅ | 按触发类型自动/手动生效 |
| .qoder/settings.json | 团队统一的权限、钩子、模型 | ✅ | 当前项目所有人 |
| .qoder/settings.local.json | 只属于你这台机器的覆盖 | ❌ | 仅本机，优先级最高 |
| .qoder/skills/ | 打包「专业流程」供复用 | ✅ | 模型自动判断或 `/技能名` |
| .qoder/commands/ | 预设提示词注册成命令 | ✅（建议） | 手动 `/命令名` 触发 |
| .qoder/agents/ | 独立上下文的专家子智能体 | ✅ | 模型自动选择或 `/智能体名` |
| .qoder/workflows/ | 多 Agent 分阶段编排脚本 | ✅ | 自然语言或按名调用 |
| .qoder/output-styles/ | 定制 AI 回复的语气与详略 | ✅ | settings 里 outputStyle 引用，重启生效 |
| .mcp.json | 给 AI 接外部工具 | ✅（注意脱敏） | 会话中自动发现工具 |

**三条全局规则**：

- settings 合并顺序（深度合并）：内置默认 < 用户级（`~/.qoder/settings.json`）< 项目级 < 本地级（`settings.local.json`）< 命令行 `--settings`
- AGENTS.md 与 rules 内容冲突时，**rules 优先**
- **文件夹信任前提**：项目级/本地级配置仅在工作目录被信任时才加载（`security.folderTrust.enabled`，默认开）；未信任时 Qoder 只读用户级配置，项目里的 `settings.json`/`settings.local.json` 一律忽略——队友首次克隆需先信任目录

**命名资产的同名覆盖方向（易错，各类型不一致）**：

| 资产 | 同名时谁生效 |
|---|---|
| settings 配置值 | 本地级 > 项目级 > 用户级（逐字段深度合并） |
| commands（第 6 节） | **用户级 > 项目级** |
| output-styles（第 10 节） | **项目级 > 用户级** > 插件 > 内置 |
| workflows（第 8 节） | 项目级 > 插件级 > 内置 |
| skills（第 5 节） | 官方 IDE/CLI 表述矛盾，实践避免同名 |

**四类扩展能力如何选**（易混，先记住这张表）：

| 能力 | 本质 | 何时用 |
|---|---|---|
| Skill | 可复用的说明/领域知识 | 主 Agent 需遵循某套流程或知识 |
| Command | 预设提示词快捷方式 | 需明确 `/` 触发的固定任务 |
| Subagent | 独立上下文的专家 Agent | 单个聚焦子任务，返回总结 |
| Workflow | 多 Agent 编排脚本 | 多阶段/并行/需交叉验证的大任务 |

> 注：输出风格（output-style）与语言（language）属「表达层」偏好，不在上述四类「任务能力」中；插件（Plugin）则是把这四类 + rules/hooks/MCP 整体打包的分发机制。

---

## 1. AGENTS.md —— 项目说明书

### 1.1 它是干嘛的

AI 打开项目时第一个读的文件，相当于给新入职工程师的「项目交接文档」。没有它，AI 每次都要重新猜项目结构、启动方式、技术栈。Qoder 兼容 AGENTS.md 开放标准，文件放项目根目录即自动识别，**无需任何注册配置**（settings 的 `context.fileName` 默认值就是它）。

### 1.2 怎么使用

1. 在项目根目录创建 `AGENTS.md`
2. 新开会话自动生效
3. 验证：新会话问「这个项目怎么启动」，看是否直接答对

偷懒技巧：会话中执行 `/init`，Qoder 会分析项目结构自动生成 AGENTS.md，再在其基础上人工修订，比从零写快得多。

### 1.3 应该怎么写、需要包含什么

- **必须**：一句话简介、技术栈、启动命令、测试命令、目录结构
- **强烈建议**：业务红线（AI 必须遵守的约束）、环境准备、常见陷阱
- **写法原则**：像写给「聪明但不了解背景的新同事」；用列表别写长篇；命令写成可直接复制执行的完整形式；保持精简（业界经验：60 行以内为佳）

### 1.4 Demo 示例

以一个虚构的 Python FastAPI 项目为例：

````markdown
# AGENTS.md —— 项目说明

## 项目简介
用户管理 + 订单查询的 Python Web API 服务。

## 技术栈
Python 3.11 + FastAPI + uvicorn + requests + python-dotenv（见 requirements.txt）

## 常用命令
```powershell
pip install -r requirements.txt                  # 安装依赖
python -m uvicorn app:app --reload --port 8000   # 启动开发服务
python -m pytest tests/ -v                       # 跑测试
```

## 目录结构
- `app.py`         入口与路由
- `services/`      业务逻辑
- `tests/`         pytest 测试
- `.env`           环境变量（从 `.env.example` 复制，勿提交真实值）

## 约束红线（必须遵守）
- 所有配置从环境变量读取，禁止硬编码密钥
- 外部服务调用必须设超时，且写好失败降级路径
- 新增接口必须配套测试
````

### 1.5 Demo 解读

技术栈、命令、目录让 AI 不用猜；「约束红线」是 AGENTS.md 里价值最高的部分——把最容易踩的业务约束写死在开头，AI 从第一轮对话就遵守。

---

## 2. .qoder/rules/ —— 编码规则

### 2.1 它是干嘛的

约束 AI 生成代码的风格与行为，相当于「团队编码规范」。文件为 Markdown + YAML frontmatter，放在 `.qoder/rules/` 下，文件名即规则名，通过 Git 与团队共享。

### 2.2 怎么使用

两种创建方式：

- **UI 方式（推荐）**：设置（Ctrl+Shift+,）→ 规则 → 添加 → 输入名称 → 选类型 → 文件自动落到 `.qoder/rules/`
- **手动方式**：直接建 `.md` 文件写 frontmatter

四种触发类型（`trigger` 取值经多源核验为下划线写法）：

| 类型 | frontmatter | 生效时机 | 适合 |
|---|---|---|---|
| 始终生效 | `trigger: always_on` | 每次会话都注入 | 编码风格、命名规范、禁止操作 |
| 模型决策 | `trigger: model_decision` + `description:` | AI 判断任务匹配描述时启用 | 场景化任务（生成测试时…） |
| 指定文件生效 | `trigger: glob` + `glob:` | 编辑匹配 glob 的文件时 | 语言/目录专属规则 |
| 手动引入 | `trigger: manual` | 会话中 `@规则名` 引用 | 自定义提示词片段 |

> ⚠️ 个别版本/入口的字段写法可能有差异（如连字符 vs 下划线）。最稳妥做法：**先用 UI 创建一条同类型规则，打开生成的文件确认格式，再照此手写其余规则**。

### 2.3 应该怎么写、需要包含什么

- **必须**：明确的「要/不要」条款，逐条列出
- **建议**：一正一反代码示例；一文件一主题（python-style.md、api-design.md 各管各的）
- **硬性限制**：所有活跃规则合计 ≤ 100,000 字符（超出截断）；仅支持自然语言，不支持图片/链接

### 2.4 Demo 示例（4 个文件对应 4 种类型）

**`.qoder/rules/python-style.md`（始终生效）**

```markdown
---
trigger: always_on
---

# Python 编码规范

- 遵循 PEP 8，缩进 4 空格，禁止 Tab
- 公开函数必须带类型注解和 docstring
- 异常必须捕获处理，禁止裸 except 或吞掉异常
- 新增依赖必须先说明理由，优先复用现有依赖

## 正例
def get_order(order_id: int) -> Order:
    """按 ID 查询订单。"""

## 反例
def get_order(order_id):    # 缺注解、缺 docstring
```

**`.qoder/rules/api-design.md`（模型决策）**

```markdown
---
trigger: model_decision
description: 修改 API 路由、请求/响应模型、错误处理或接口结构时使用。
---

# API 设计规范

- 路由统一挂 /api/ 前缀，健康检查为 GET /health
- 外部调用必须设置超时与失败降级路径，禁止异常穿透到用户
- 响应携带降级标记（如 fallback: true）
- 新增接口必须同步补测试
```

**`.qoder/rules/data-guard.md`（指定文件生效）**

```markdown
---
trigger: glob
glob: **/*.json
---

# 数据文件守护规范

- JSON 数据文件是唯一事实源，代码中不得硬编码其中的数据
- 修改数据结构必须向后兼容：新增字段可选，不得删除/改名现有字段
- 改动后必须跑对应测试验证
```

**`.qoder/rules/report-style.md`（手动引入）**

```markdown
---
trigger: manual
---

# 报告输出风格

生成分析报告时按此结构输出：
1. 元数据与变更统计
2. 按严重度分级的问题清单（阻塞/建议/风格）
3. 每个问题附上下文与修改建议
4. 整体结论

使用方式：会话中输入 @report-style 后再下达报告任务。
```

### 2.5 Demo 解读

四种类型各司其职：风格类用「始终生效」全量注入；场景类用「模型决策」按需加载省 token；文件专属约束用 glob 精准命中；低频模板用「手动引入」完全由你控制时机。

---

## 3. .qoder/settings.json —— 团队共享的项目设置

### 3.1 它是干嘛的

JSON 配置，承载团队需统一约定：**权限规则**（哪些操作自动放行/必须询问/一律拒绝）、**Hooks**（AI 执行流关键节点自动跑脚本）、模型选择、语言偏好。提交 Git 后团队自动生效。

### 3.2 怎么使用

1. 创建 `.qoder/settings.json` 写入配置
2. 提交仓库，队友拉取即应用
3. 权限/MCP 类配置需重启会话生效

### 3.3 应该怎么写、需要包含什么

常用配置块（字段均经官方 settings 参考核验）：

| 配置块 | 关键字段 | 作用 |
|---|---|---|
| permissions | `allow` / `ask` / `deny` | 自动放行 / 需确认 / 一律拒绝的规则列表 |
| hooks | `PreToolUse` / `PostToolUse` 等事件 | 工具执行前/后等节点自动执行脚本 |
| model | `name` / `reasoningEffort` | 项目统一模型与推理强度 |
| language | `language`（顶层） | AI 回复首选语言（需重启）；界面语言另见 `ui.language` |
| mcp | IDE：`enableAllProjectMcpServers` / `enabledProjectMcpServers`；CLI：顶层 `mcpServers` + `mcp.allowed` / `mcp.excluded` | 项目级 MCP 批准 / 白名单（两种入口字段名不同） |
| context | `fileName` | 上下文文件名（默认 AGENTS.md） |
| outputStyle | `outputStyle`（顶层）/ `general.outputStyle` | 指定回复输出风格（见第 10 节），改后需重启 |
| security | `folderTrust.enabled` / `disableYoloMode` / `blockGitExtensions` / `allowedExtensions` / `environmentVariableRedaction.enabled` | 目录信任、禁 YOLO 权限、插件来源管控、环境变量脱敏 |

> 顶层键（不属任何分组）：`outputStyle`、`language`、`agent`（主线程 Agent 名，需重启）。settings.json 支持 `//` 注释，值可引用环境变量；完整字段见官方《配置项参考》。

Hooks 要点（详细规范见官方 Hooks 文档）：

- 四种条目类型：`command`（跑脚本）、`http`（POST 到 URL）、`prompt`（单轮模型判定）、`agent`（起子 Agent 核查）
- 脚本通过 **stdin 收 JSON**，用 **exit code 控制行为**：`0` 放行、`2` 阻塞并把 stderr 反馈给 AI
- `matcher` 匹配工具名，支持 `|` 多值与正则；Windows 下可指定 `"shell": "powershell"`

### 3.4 Demo 示例

```json
{
  "language": "Chinese",
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Bash(python -m pytest *)",
      "Bash(pip install -r requirements.txt)"
    ],
    "deny": [
      "Bash(rm *)",
      "Bash(git push --force*)",
      "Read(.env)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "shell": "powershell",
            "command": "$input_json = [Console]::In.ReadToEnd(); if ($input_json -match 'git push.*--force') { Write-Error '禁止 force push'; exit 2 } else { exit 0 }"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "shell": "powershell",
            "command": "Write-Host '提示：文件已修改，记得运行测试验证'"
          }
        ]
      }
    ]
  }
}
```

### 3.5 Demo 解读

- `permissions.allow`：读文件、搜索、跑测试自动放行——AI 干活不用频繁弹窗
- `permissions.deny`：`Read(.env)` 防密钥进入上下文；`rm`、force push 直接拒绝
- `PreToolUse` Hook：从 stdin 读工具输入 JSON，检测到 force push 就 `exit 2` 阻塞
- `PostToolUse` Hook：每次写/改文件后提醒跑测试

> PowerShell 的 stdin 读取写法为示意，实际以本机验证为准；官方 Hooks 文档的示例以 bash（`input=$(cat)` + `jq`）为主。

---

## 4. .qoder/settings.local.json —— 个人本地覆盖

### 4.1 它是干嘛的

与 settings.json 完全同构、字段相同，但**只在本机生效**且**优先级最高**。适合放个人临时调试开关、本地服务地址、对自己放得更宽的权限。

### 4.2 怎么使用

1. 创建 `.qoder/settings.local.json`
2. **立刻加入 .gitignore**（防止误提交）
3. 重启会话生效

### 4.3 应该怎么写

只写想覆盖的字段——配置逐层合并，不必全量复制 settings.json。

### 4.4 Demo 示例

```json
{
  "permissions": {
    "allow": [
      "Bash(python *)"
    ]
  }
}
```

对应 `.gitignore` 追加：

```gitignore
.qoder/settings.local.json
```

### 4.5 Demo 解读

本地调试时给自己放开任意 python 命令——团队仓库里的红线 settings.json 原封不动，其他人不受影响。

---

## 5. .qoder/skills/ —— 可复用的专业技能

### 5.1 它是干嘛的

把一套「专业流程/领域知识」打包成文件，AI 匹配场景时自动加载执行，或 `/技能名` 手动触发。与 rules 的区别：rules 是**约束**（不许怎样），skill 是**教程**（教你怎么干一件事）。

### 5.2 怎么使用

1. 创建 `.qoder/skills/<技能名>/SKILL.md`（项目级，团队共享）
2. 可选加辅助文件：`REFERENCE.md`、`scripts/`、`templates/`
3. 重启会话或 CLI 中 `/skills reload` 加载
4. 触发：直接描述需求（模型自动判断）或输入 `/技能名`

### 5.3 应该怎么写、需要包含什么

SKILL.md = YAML frontmatter（两个必填字段）+ Markdown 正文：

| 字段 | 必填 | 规范 |
|---|---|---|
| `name` | ✅ | 仅小写字母/数字/连字符，≤64 字符，与目录名一致 |
| `description` | ✅ | ≤1024 字符，写清「功能 + 何时使用 + 触发关键词」——AI 靠它决定是否调用 |

正文中按需包含：分步 Instructions、输出格式约定、示例、辅助文件引用（渐进式披露：主文件精简，细节放 REFERENCE.md 按需加载）。

**description 是成败关键**：

- ❌ `description: 帮助处理日志`（太模糊，永远不会被触发）
- ✅ `description: 分析日志文件识别错误、模式与性能问题。当用户提到调试日志、排查错误、查看应用行为时触发。`

### 5.4 Demo 示例

**`.qoder/skills/changelog-writer/SKILL.md`**

````markdown
---
name: changelog-writer
description: 生成或更新 CHANGELOG.md 时使用。当用户提到记录变更、写发布说明、整理版本日志时触发。
---

# 变更日志撰写器

## 工作流程

1. 读取 `CHANGELOG.md`（不存在则按 Keep a Changelog 格式新建）
2. 用 `git log` 获取自上个版本以来的提交
3. 将提交归类到对应变更类型：
   - Added（新增）/ Changed（变更）/ Fixed（修复）/ Removed（移除）
4. 每条变更写一行，格式：动词开头 + 模块 + 具体内容
5. 未发布内容统一放在 `## [Unreleased]` 小节

## 约束

- 只依据 git 提交记录归纳，禁止编造不存在的变更
- 提交信息含糊时，先读对应 diff 再归类，归类不了的列入「待确认」向用户提问

## 输出要求

更新完成后展示新增的条目全文，并提示：请人工确认归类是否准确。
````

### 5.5 Demo 解读

这个 skill 打包了「规范写变更日志」的完整流程——格式标准、归类规则、防编造约束。以后你说「整理一下这个版本的变更日志」，AI 自动按这套标准执行。

---

## 6. .qoder/commands/ —— 自定义斜杠命令

### 6.1 它是干嘛的

把一段预设提示词注册成 `/命令名`，一键唤起固定任务。与 Skill 的区别：**Command 只能手动 `/` 触发**，Skill 可被模型自动调用；适合「需要明确触发、无需模型自主判断」的任务。

### 6.2 怎么使用

1. 创建 `.qoder/commands/<命令名>.md`（项目级，建议提交共享）
2. 会话中输入 `/命令名` 触发
3. CLI 运行中执行 `/commands` 重新加载
4. 子目录即命名空间：`commands/git/commit.md` → `/git:commit`

### 6.3 应该怎么写、需要包含什么

Markdown = frontmatter + 系统提示词正文：

| 字段 | 必填 | 说明 |
|---|---|---|
| `description` | ✅ | 功能描述，显示在命令清单（多行用 YAML `|` 语法） |
| `name` | ❌ | 仅作 TUI 展示名，**调用名由文件路径决定** |

命名规范：小写字母 + 连字符；文件名与 name 保持一致。

优先级：项目级与用户级（`~/.qoder/commands/`）同名时，**用户级覆盖项目级**（注意与 output-styles/workflows 方向相反，见第 0.3 节对照表）。

### 6.4 Demo 示例

**`.qoder/commands/run-tests.md`**

```markdown
---
name: run-tests
description: 运行项目全部测试并结构化汇报结果。提交前快速验证时使用。
---

你是测试协调员。请依次执行：

1. 运行项目全部测试（以 AGENTS.md 中的测试命令为准）
2. 汇总输出：
   - 通过数 / 总数
   - 失败项的具体原因与涉及文件
   - 结论：是否达到可提交状态

注意：若有失败，先定位到具体文件与行号再给结论，禁止只报「测试失败」。
```

### 6.5 Demo 解读

把「跑测试 + 汇总格式 + 禁止模糊结论」固化成一道命令。以后提交前输入 `/run-tests`，AI 按固定标准执行并汇报，省去每次重复交代。

---

## 7. .qoder/agents/ —— 自定义子智能体

### 7.1 它是干嘛的

创建拥有**独立上下文窗口、独立工具权限、独立系统提示词**的专家 Agent。主对话遇到匹配任务时自动委派给它，或 `/智能体名` 手动调用。适合把「代码审查员」「安全审计员」这类角色固化下来——它在自己的上下文里干活，只把结论返回主会话，不污染主线上下文。

### 7.2 怎么使用

1. 创建 `.qoder/agents/<智能体名>.md`（项目级，团队共享；用户级为 `~/.qoder/agents/`）
2. 推荐用内置技能 `/create-agent <诉求>` 交互式生成，自动放到正确位置
3. 触发：自然语言描述任务（模型按 description 自动选择）或 `/智能体名`

### 7.3 应该怎么写、需要包含什么

Markdown = frontmatter + 系统提示词正文：

| 字段 | 必填 | 说明 |
|---|---|---|
| `name` | ✅ | 智能体唯一标识名 |
| `description` | ✅ | 功能与专长简述，供模型自动选择 |
| `model` | ❌ | 指定运行模型，不设则跟随对话模型 |
| `tools` | ❌ | 允许的工具列表，逗号分隔（Bash/Edit/Write/Glob/Grep/Read/WebFetch/WebSearch） |
| `skills` | ❌ | 允许调用的技能列表 |
| `mcpServers` | ❌ | 允许使用的 MCP 服务列表 |

### 7.4 Demo 示例

**`.qoder/agents/code-review.md`**

```markdown
---
name: code-review
description: 代码审查专家，检查代码质量与安全性。当用户要求审查代码、检查改动、做质量或安全核查时使用。
tools: Read, Grep, Glob, Bash
---

你是一位资深代码审查员，负责确保代码质量。

审查清单：
1. 代码可读性与命名规范
2. 错误处理是否完备（有无吞异常、有无降级）
3. 安全性（注入、越权、密钥硬编码）
4. 测试覆盖是否跟上改动

输出：按「阻塞 / 建议 / 风格」三档列出问题，每条给出文件、行号与修改建议，
最后给一句总体结论（可合 / 需改）。只报真实问题，不堆砌套话。
```

### 7.5 Demo 解读

`tools` 只给了只读类工具（Read/Grep/Glob/Bash）——审查员能看能跑测试，但不能改文件，从权限上杜绝「审查顺手改代码」。以后说「帮我审查这个接口」，模型自动委派给 `code-review`，它在独立上下文里查完只回结论。

---

## 8. .qoder/workflows/ —— 动态工作流

### 8.1 它是干嘛的

用一段 **JavaScript 编排脚本**在后台调度多个子 Agent，做分阶段、可并行、可交叉验证的大任务（仓库审计、深度研究、迁移规划、发版检查）。当任务明显大于「一次 Agent 调用」时用它——这是四类扩展能力里最重的一档。

### 8.2 怎么使用

1. 创建 `.qoder/workflows/<名称>.js`（项目级，团队共享；用户级为 `~/.qoder/workflows/`）
2. 触发：自然语言（如「用 repo-audit 工作流审计 auth 模块」）或内置的 `/deep-research` 等
3. 查看：TUI 中 `/workflows` 打开任务面板看阶段/日志/输出
4. 优先级：同名时**项目级 > 插件级 > 内置**

### 8.3 应该怎么写、需要包含什么

脚本以 `export const meta = {...}` 开头声明元信息，正文用官方辅助函数编排：

| meta 字段 | 说明 |
|---|---|
| `name` | 工作流名称（调用用） |
| `description` | 做什么 |
| `whenToUse` | 何时该用（供模型判断） |
| `phases` | 阶段数组，每项 `{ title, detail }` |

可用辅助函数：`agent()`、`parallel()`、`pipeline()`、`phase()`、`log()`、`workflow()`、`args`（接收每次运行不同的入参）。

**安全模型**：脚本本身**不能**直接访问 shell/文件系统/网络/Node API/MCP——所有副作用都通过它启动的子 Agent 发生，而子 Agent 仍受 settings 的权限、Hooks、沙箱约束。

### 8.4 Demo 示例

**`.qoder/workflows/repo-audit.js`**

```javascript
export const meta = {
  name: "repo-audit",
  description: "审计仓库某个区域并汇总风险",
  whenToUse: "当用户要求对某个模块做结构化安全/质量审计时使用",
  phases: [
    { title: "Scan", detail: "定位相关文件与区域" },
    { title: "Analyze", detail: "并行运行聚焦分析子 Agent" },
    { title: "Summarize", detail: "合并发现生成最终报告" }
  ]
};

// 正文用 agent()/parallel()/phase()/args 编排：
// 1) phase("Scan") 找出 args.target 模块下的关键文件
// 2) phase("Analyze") 对每个文件 parallel() 启动子 Agent 做安全/质量分析
// 3) phase("Summarize") 合并各子 Agent 结论，输出分级风险报告
// 运行时：脚本、日志、输出写入 .qoder/sessions 下当前会话目录
```

### 8.5 Demo 解读

`args.target` 让同一个脚本复用于不同模块（`用 repo-audit 审计 authentication`）。三阶段在 `/workflows` 面板里可视化推进；因为副作用全走子 Agent，团队用 settings.json 的权限/Hooks 就能统一管住这些并行 Agent 能干什么。

---

## 9. .mcp.json —— 外部工具接入（MCP 服务器）

### 9.1 它是干嘛的

声明项目需要哪些 MCP 服务器，让 AI 能调用外部能力（抓网页、查数据库、调第三方 API）。放项目根目录，提交后队友拉代码即获得相同工具环境。两种传输类型：

- **STDIO**（本地进程）：`command` + `args`，适合本地工具
- **SSE / Streamable HTTP**（远程服务）：`type: "sse"` + `url`，适合托管服务（对初学者最友好）

### 9.2 怎么使用

1. 项目根目录创建 `.mcp.json`
2. **安全机制**：项目级 MCP 首次使用需批准——逐个批准后记入 `mcp.enabledProjectMcpServers`；团队信任场景可设 `mcp.enableAllProjectMcpServers: true` 自动放行
3. 验证：让 AI 调用对应工具，展开详情看工具列表

### 9.3 应该怎么写、需要包含什么

- 顶层 `mcpServers` 对象，每个服务器一个键
- STDIO：`command`、`args` 数组、`env`（注入的环境变量）
- SSE：`type: "sse"`、`url`
- **密钥纪律**：token 通过 `env` 注入，仓库中只放占位符（如 `${GITHUB_TOKEN}`），真实值放 `.env`（不提交）

### 9.4 Demo 示例

```json
{
  "mcpServers": {
    "fetch": {
      "type": "sse",
      "url": "https://mcp.api-inference.modelscope.net/mcp/fetch/sse"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### 9.5 Demo 解读

`fetch`（SSE 远程）让 AI 抓网页转 Markdown；`github`（STDIO 本地）示例了 env 注入密钥的正确姿势——占位符进仓库，真值进 `.env`。

---

## 10. .qoder/output-styles/ —— 输出风格

### 10.1 它是干嘛的

调整 AI 回复的**语气、详略、组织方式**（更简洁 / 更偏教学 / 固定结构），只在系统提示之上**叠加表达偏好**，不改变 Qoder 的核心身份与安全约束。与 rules 的区别：rules 管「代码怎么写」，output-style 管「话怎么说」；与 language 的区别：language 定语种，output-style 定表达风格，二者可叠加。

### 10.2 怎么使用

1. 创建 `.qoder/output-styles/<风格名>.md`（项目级，团队共享；用户级为 `~/.qoder/output-styles/`）
2. 在 settings 里用顶层键 `outputStyle: "<风格名>"` 引用启用（也兼容 `general.outputStyle`，两处都写以顶层为准）
3. **重启生效**；只想临时用可启动时加 `--output-style <名>`（仅本次会话，优先级更高，免重启）

### 10.3 应该怎么写、需要包含什么

一个 `.md` 文件 = 可选 frontmatter + 正文，**正文就是叠加进系统提示的风格说明**：

| 字段 | 必填 | 说明 |
|---|---|---|
| `name` | ❌ | 风格名；缺省用文件名（去 .md） |
| `description` | ❌ | 描述；缺省取正文首个非标题行 |

优先级（后者覆盖前者）：内置 < 插件 < 用户级 < **项目级**。编写建议：只描述表达层偏好（语气/详略/结构），保持精简，别试图覆盖安全约束。

### 10.4 Demo 示例

**`.qoder/output-styles/concise-cn.md`**

```markdown
---
name: concise-cn
description: 简洁的中文回复风格
---

回复尽量简短，先给结论再给理由。
避免重复用户已知的信息，代码示例只保留关键部分。
```

在 `.qoder/settings.json` 中启用：

```json
{ "outputStyle": "concise-cn" }
```

### 10.5 Demo 解读

把「先结论后理由、不啰嗦」固化成团队默认表达风格，省去每次对话重复交代。它只影响怎么说，不影响做什么——安全红线仍由 rules/settings 把关。

---

## 11. Plugins（插件）—— 把上述能力打包分发【高级·可选】

### 11.1 它是干嘛的

插件是把 **Skills / MCP / Agents / Commands / Rules / Hooks / 输出风格 / 工作流** 的任意组合打包成一个可安装、可共享的目录。它不是「日常开发必须准备的文件」，而是当你想把一套能力**整体分发**给团队/社区，或**整体安装**别人做好的能力包时才用。

### 11.2 怎么使用（两种角色）

- **消费方（最常见）**：插件市场安装，或 `/plugins install <插件>`（别名 `/plugin`）；安装时选生效范围 **User 级 / Project 级**。项目级安装由 Qoder 记录在插件注册表（带 `projectPath`），**无需手写文件**。
- **作者方（进阶）**：用内置 `plugin-creator` 技能引导生成，或手动建插件目录；本地成品可「导入」。校验用 `/plugins validate <path>`。

### 11.3 应该怎么写、需要包含什么（作者方）

manifest 放在 **`.qoder-plugin/plugin.json`**（不在插件根目录），仅 `name`（kebab-case）必填，其余可省略；未声明组件时按约定目录自动发现：

```
plugin-name/
├── .qoder-plugin/
│   └── plugin.json      # manifest（可省略，仅 name 必填）
├── commands/            # 命令（.md，支持子目录）
├── agents/              # 子智能体（.md）
├── skills/              # 技能（skill-name/SKILL.md）
├── hooks/hooks.json     # Hook 配置
├── output-styles/       # 输出风格
├── workflows/           # 工作流
├── bin/                 # 可执行文件（加入 PATH）
└── .mcp.json            # MCP 配置
```

**安全开关**（写在项目 `.qoder/settings.json` 的 `security` 分组，改后需重启）：

```json
{
  "security": {
    "blockGitExtensions": true,
    "allowedExtensions": ["^https://github\\.com/my-org/"]
  }
}
```

### 11.4 Demo 场景

团队把「代码审查 Agent + 提交命令 + Python 规范 Rule + 测试 Hook」打成一个 `team-standards` 插件发布到企业市场；新项目里 `/plugins install team-standards` 并选 **Project 级**，一次性获得全套约定，无需逐个复制文件。

### 11.5 Demo 解读

插件解决的是「能力的打包与复用」，前述 1–10 项解决的是「单点能力怎么写」。日常开发按 1–10 项准备即可；当约定多到需整体分发时，再升级成插件。安装的整体性也意味着：禁用插件会同时停用其全部组件，不能单独关其中一个。

---

## 12. 收尾清单

### 12.1 .gitignore 最终版

```gitignore
# 个人本地配置，不提交
.qoder/settings.local.json

# 密钥与环境
.env

# IDE / 缓存（按需）
.idea/
__pycache__/
*.pyc

# 可选：仅本地使用的规则不共享时
# .qoder/rules/
```

### 12.2 新项目上手顺序

1. ☐ `AGENTS.md` —— 10 分钟见效，最先写（可先用 `/init` 生成再改）
2. ☐ `.qoder/rules/` 一条「始终生效」编码规范 —— 第二个写
3. ☐ `requirements.txt` / `.env.example` / `tests/` —— 让 AI 能自验证
4. ☐ `.qoder/settings.json` 权限白名单 —— 团队协作时加
5. ☐ `.qoder/commands/` —— 出现高频固定任务时提炼
6. ☐ `.qoder/skills/` —— 出现重复性专业流程时提炼
7. ☐ `.qoder/agents/` —— 需要独立上下文的专家角色时加
8. ☐ `.qoder/workflows/` —— 出现多阶段/并行大任务时加
9. ☐ `.mcp.json` —— 需要连外部系统时加
10. ☐ `.qoder/output-styles/` —— 想统一团队回复风格时加
11. ☐ 插件（Plugins）—— 约定多到需整体分发/安装时再升级

### 12.3 已知硬性限制

- rules 全部活跃文件合计 ≤ 100,000 字符，超出截断
- rules 仅支持自然语言，不支持图片/链接
- SKILL.md 的 `name` ≤64 字符、`description` ≤1024 字符
- workflows 脚本不能直接访问 shell/fs/网络/MCP，副作用只能经子 Agent
- output-styles 只叠加表达偏好，不能替换/绕过 Qoder 身份与安全约束；改 `outputStyle` 需重启
- 插件按整体安装/禁用，不能单独启停其中某个组件

---

## 13. 完整性边界说明（诚实声明）

1. **版本边界**：「最全」基于 2026-09 核验的官方文档。Qoder 迭代快，新版本可能新增项目级能力，届时以 docs.qoder.com 为准并修订本教程。
2. **rules frontmatter 取值**：`trigger` 经多源核验为 `always_on` / `manual` / `model_decision` / `glob`（下划线写法）；个别版本或 UI 入口可能不同，稳妥做法是先用 UI 建一条再照抄格式。
3. **skills 同名覆盖方向**：官方 IDE 文档与 CLI 文档对「用户级 vs 项目级同名 Skill 谁优先」表述相反，实践中避免同名即可。
4. **自动生成项**：`worktrees/`、`scheduled_tasks.json`（定时任务经 `/loop` 或自然语言创建、循环任务 7 天自动过期）、`sessions/`、自动记忆目录、项目级插件安装记录（注册表，带 `projectPath`）均为运行时生成/维护，无需手动准备。
5. **MCP 字段两套命名**：IDE 侧用 `enableAllProjectMcpServers`/`enabledProjectMcpServers`（批准制），CLI 侧用顶层 `mcpServers` + `mcp.allowed`/`mcp.excluded`（白名单制）；`.mcp.json` 定义服务器本身，两者通用，以你使用的入口为准。
6. **本教程修订记录**：初版 7 项 → 补入 agents、workflows 并修正 `always_on`，成 9 项 → 补入 output-styles（第 10 项）与 Plugins（第 11 节），成「10 项 + 插件」 → 本轮对照《配置作用范围》《配置文件与生效顺序》《命令》《定时任务参考》复核：确认清单完整、`scheduled_tasks.json` 正确排除；新增文件夹信任前提与「命名资产覆盖方向对照表」，校正 mcp/security 字段。
