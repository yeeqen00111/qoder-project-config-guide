# 用自然语言创建 Qoder 配置 —— 10 篇速查教学

不想手写 frontmatter？**直接对 Qoder 说话，让它帮你生成。** 本目录每项一篇，统一给出：可复制的提示词 → Qoder 会做什么 → 生成在哪 → 怎么验证 → 常见坑。

## 目录

| # | 配置项 | 教学文件 | 一句话 |
|---|---|---|---|
| 1 | AGENTS.md | [01-AGENTS.md](01-AGENTS.md) | 项目说明书 |
| 2 | rules | [02-rules.md](02-rules.md) | 编码规则 |
| 3 | settings.json | [03-settings.md](03-settings.md) | 团队权限 / 钩子 |
| 4 | settings.local.json | [04-settings-local.md](04-settings-local.md) | 个人本地覆盖 |
| 5 | skills | [05-skills.md](05-skills.md) | 可复用技能 |
| 6 | commands | [06-commands.md](06-commands.md) | 斜杠命令 |
| 7 | agents | [07-agents.md](07-agents.md) | 子智能体 |
| 8 | workflows | [08-workflows.md](08-workflows.md) | 动态工作流 |
| 9 | .mcp.json | [09-mcp.md](09-mcp.md) | 外部工具接入 |
| 10 | output-styles | [10-output-styles.md](10-output-styles.md) | 回复风格 |

## 通用技巧

- 每篇的「提示词」都可直接复制，把 `<占位>` 换成你的实际内容。
- 让 Qoder 生成后，**务必打开文件核对 frontmatter，再重启会话验证**是否生效。
- 想一次性搭建全部配置、被一步步引导，用配套的 [`qoder-config-init`](../qoder-config-init/) skill。
- 完整原理与字段详解见根目录的《Qoder项目级配置完全教程.md》。
