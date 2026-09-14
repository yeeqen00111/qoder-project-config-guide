# 用自然语言创建 ⑥commands（自定义斜杠命令）

## 这项是干嘛的
把一段预设提示词注册成 `/命令名`，一键唤起固定任务。**只能手动 `/` 触发**（skill 可被模型自动调用），适合需要明确触发的固定任务。

## 直接对 Qoder 说（复制即用）
> 帮我在 .qoder/commands/run-tests.md 建一个命令，description 写「运行全部测试并结构化汇报」。正文让它：按 AGENTS.md 的测试命令跑测试，输出通过数 / 总数、失败项的文件与原因、是否可提交的结论，失败要先定位到文件行号再说结论。

## Qoder 会做什么
1. 建 `.qoder/commands/<名>.md`
2. frontmatter 写 `description`（**必填**）；`name` 可选（仅 TUI 展示，调用名由文件路径决定）
3. 正文写命令触发时注入的系统提示词

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/commands/<名>.md`
- 验证：会话输入 `/<命令名>`；CLI 中 `/commands` 重载。

## 常见坑
- 子目录 = 命名空间：`commands/git/commit.md` → `/git:commit`。
- 同名时**用户级覆盖项目级**（与 output-styles / workflows 方向相反）。
- description 必填，否则命令清单里看不出用途。
