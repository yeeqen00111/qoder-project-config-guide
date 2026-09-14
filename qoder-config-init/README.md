# qoder-config-init —— Qoder 项目配置初始化向导（Skill）

一个 Qoder **技能（Skill）**。唤起后，它按「访谈 → 起步三件套 → 按需增量 → 收尾校验」引导你在**自己的项目**里创建 10 项 Qoder 项目级配置，全程照 `REFERENCE.md` 的权威格式生成，**不编造字段**。

## 这个文件夹里有什么

| 文件 | 作用 |
|---|---|
| `SKILL.md` | 技能主文件（向导流程 + 触发描述） |
| `REFERENCE.md` | 10 项配置的路径 / frontmatter / 模板速查（技能的知识底座，防止编造字段） |
| `README.md` | 本使用文档 |

覆盖的 10 项：AGENTS.md、rules、settings.json、settings.local.json、skills、commands、agents、workflows、.mcp.json、output-styles。

## 安装（复制到你的项目即可用）

把**整个 `qoder-config-init/` 文件夹**复制到目标项目的技能目录：

```
<你的项目>/.qoder/skills/qoder-config-init/
├── SKILL.md
├── REFERENCE.md
└── README.md
```

Windows PowerShell（在本仓库根目录执行，替换目标路径）：

```powershell
New-Item -ItemType Directory -Force "<你的项目>\.qoder\skills" | Out-Null
Copy-Item -Recurse ".\qoder-config-init" "<你的项目>\.qoder\skills\qoder-config-init"
```

想让**所有项目**都能用：复制到用户级技能目录 `~/.qoder/skills/qoder-config-init/`。

复制后**重启会话**（或在 CLI 中执行 `/skills reload`）加载。

## 使用

- **手动**：会话中输入 `/qoder-config-init`
- **自动**：直接说「帮我初始化 Qoder 项目配置」「这个项目该配哪些 .qoder」「给我加一条编码规则 / 权限白名单」——模型据 description 自动加载

技能会先问几个问题（项目语言、个人 / 团队、最想解决什么），推荐**起步三件套**（AGENTS.md + 一条 `always_on` 规则 + settings 权限白名单），再按需补其余 7 项，每项**先展示内容、确认后写入**。

## 产出

在你的项目里生成对应配置文件，并在收尾时：
- 提醒把 `.qoder/settings.local.json`、`.env` 加入 `.gitignore`
- 提示「文件夹信任」前提（未信任则项目级配置不加载）
- 给出已创建文件清单与验证方式

## 前置条件

- 已安装 Qoder。
- 目标项目目录**已被信任**（否则项目级配置不会加载）。

## 卸载

删除 `<你的项目>/.qoder/skills/qoder-config-init/` 文件夹，重启会话即可。

## 说明

本技能只在**你的项目**里写文件，不会修改它自己所在的目录；`REFERENCE.md` 的格式以 docs.qoder.com（2026-09 核验）为准，Qoder 升级后如有出入，以「先用 IDE 的设置 UI 建一条同类配置、再打开生成的文件照抄格式」为最稳妥做法。
