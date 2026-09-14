# 用自然语言创建 ⑧workflows（动态工作流）

## 这项是干嘛的
用一段 **JavaScript 编排脚本**在后台调度多个子 Agent，做分阶段、可并行、可交叉验证的大任务（仓库审计、深度研究、迁移规划、发版检查）。四类扩展里最重的一档——任务明显大于「一次 Agent 调用」时才用。

## 直接对 Qoder 说（复制即用）
> 帮我写一个 `.qoder/workflows/repo-audit.js` 工作流：`export const meta` 里 name=repo-audit，description / whenToUse 写清用途，phases 分 Scan / Analyze / Summarize 三阶段。正文用 phase()/parallel()/agent()/args 编排，对 args.target 模块并行起子 Agent 分析，再汇总成分级风险报告。

## Qoder 会做什么
1. 建 `.qoder/workflows/<名>.js`
2. 以 `export const meta = {...}` 声明 name / description / whenToUse / phases
3. 正文用辅助函数 `agent()`/`parallel()`/`pipeline()`/`phase()`/`log()`/`args` 编排

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/workflows/<名>.js`
- 验证：自然语言「用 repo-audit 审计 auth 模块」；TUI `/workflows` 看阶段 / 日志面板。

## 常见坑
- 脚本本身**不能**直接碰 shell / fs / 网络 / MCP，副作用只能经它启动的子 Agent。
- 运行时脚本 / 日志 / 输出写入 `.qoder/sessions/`（自动生成，别手动建）。
- 简单任务别用 workflow，用 command 或 skill 就够；同名时项目级 > 插件级 > 内置。
