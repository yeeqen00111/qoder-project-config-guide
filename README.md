# Qoder 项目级配置教程

本仓库的职责：收录并维护一份完整的《Qoder 项目级配置完全教程》。

## 内容

- [Qoder项目级配置完全教程.md](./Qoder项目级配置完全教程.md)

覆盖用 Qoder 开发项目需要准备的全部 10 项项目级配置（+ 插件打包机制），每项包含「是干嘛的 / 怎么使用 / 应该怎么写 / 需要包含什么 / Demo 示例」：

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
