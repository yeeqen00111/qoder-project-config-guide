# Qoder 项目级配置指南

一份完整的《Qoder 项目级配置完全教程》，外加**可直接复制使用**的配套工具：一个配置向导 skill、10 篇「用自然语言创建」教学、一个 Windows shell UTF-8 防乱码 skill。

## 仓库结构

```
qoder-project-config-guide/
├── Qoder项目级配置完全教程.md      # 主教程：10 项配置 + 插件，逐项讲解与 Demo
├── qoder-config-init/              # 交付物1：配置向导 skill（复制到 .qoder/skills/ 即用）
│   ├── SKILL.md                    #   向导流程
│   ├── REFERENCE.md                #   10 项权威格式速查（知识底座）
│   └── README.md                   #   使用文档
├── natural-language-guides/        # 交付物2：10 篇「用自然语言创建 X」教学
│   ├── README.md                   #   索引
│   └── 01~10-*.md                  #   每项一篇
└── windows-shell-utf8/             # 交付物3：Windows shell UTF-8 防乱码 skill
    ├── SKILL.md                    #   设置片段 + 验证
    └── README.md                   #   使用文档
```

## 三个交付物

### 1. qoder-config-init —— 配置向导 Skill
唤起后按「访谈 → 起步三件套 → 按需增量」引导你在**自己的项目**里创建 10 项配置，全程照权威格式生成、不编造字段。
- **装**：把整个 `qoder-config-init/` 复制到 `<你的项目>/.qoder/skills/`
- **用**：`/qoder-config-init`，或说「帮我初始化 Qoder 项目配置」
- 详见 [qoder-config-init/README.md](./qoder-config-init/README.md)

### 2. natural-language-guides —— 10 篇自然语言创建教学
每项一篇，给「复制即用」的提示词 + Qoder 会做什么 + 生成在哪 + 怎么验证 + 常见坑。适合「我知道要建哪一项，给我那句话」的场景。
- 索引见 [natural-language-guides/README.md](./natural-language-guides/README.md)

### 3. windows-shell-utf8 —— Windows 防乱码 Skill
让 Qoder 在 Windows 跑 shell / git 命令时先切 UTF-8，避免中文输出乱码。
- **装**：把整个 `windows-shell-utf8/` 复制到 `<你的项目>/.qoder/skills/`
- **用**：装好后跑命令时自动生效
- 详见 [windows-shell-utf8/README.md](./windows-shell-utf8/README.md)

## 主教程覆盖的 10 项（+ 插件）

1. `AGENTS.md` —— 项目说明书
2. `.qoder/rules/` —— 编码规则（4 种触发类型）
3. `.qoder/settings.json` —— 团队共享设置（权限 / Hooks / 模型 / 输出风格 / 插件安全）
4. `.qoder/settings.local.json` —— 个人本地覆盖
5. `.qoder/skills/` —— 可复用的专业技能
6. `.qoder/commands/` —— 自定义斜杠命令
7. `.qoder/agents/` —— 自定义子智能体
8. `.qoder/workflows/` —— 动态工作流
9. `.mcp.json` —— 外部工具接入（MCP 服务器）
10. `.qoder/output-styles/` —— 输出风格
11. Plugins（插件）—— 把上述能力打包分发【高级·可选】

## 核验口径

- 内容逐项对照 docs.qoder.com 官方文档核验（规则、配置作用范围、配置项参考、Skills、命令、Hooks、MCP、Subagent、Workflows、输出风格、插件 共十一篇）。
- 版本差异等不确定项在教程第 13 节「完整性边界说明」如实标注，不混入正文。

## 维护约定

- Qoder 版本迭代可能改变目录/字段约定：发现不符时以官方文档为准，并修订教程顶部的「核验时间」。
- 两个 skill 文件夹本身即「复制品」——整个文件夹拷到目标项目的 `.qoder/skills/` 即可用；它们在本仓库里是**惰性素材**，不会作用于本仓库自身。
