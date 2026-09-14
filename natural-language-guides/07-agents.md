# 用自然语言创建 ⑦agents（自定义子智能体）

## 这项是干嘛的
拥有**独立上下文窗口、独立工具权限、独立系统提示词**的专家 Agent。主对话遇到匹配任务时自动委派，或 `/智能体名` 调用，只把结论返回主线，不污染主上下文。

## 直接对 Qoder 说（复制即用）
> 帮我创建一个子智能体 `.qoder/agents/code-review.md`：name 用 code-review，description 写「代码审查专家，何时委派」，tools 只给 `Read, Grep, Glob, Bash`（只读，不许改文件）。系统提示词给审查清单和「阻塞 / 建议 / 风格」三档输出格式。

或用内置：
> /create-agent 我要一个只做代码审查、不能改文件的专家

## Qoder 会做什么
1. 建 `.qoder/agents/<名>.md`
2. frontmatter：`name`/`description` 必填，`model`/`tools`/`skills`/`mcpServers` 可选
3. 正文写角色 / 清单 / 输出格式

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/agents/<名>.md`
- 验证：说「帮我审查这段代码」看是否自动委派；或 `/<智能体名>`。

## 常见坑
- tools 只给需要的（审查员给只读工具，杜绝顺手改代码）；不给则继承全部。
- tools 取值限：`Bash`/`Edit`/`Write`/`Glob`/`Grep`/`Read`/`WebFetch`/`WebSearch`。
- 推荐用 `/create-agent` 交互生成，自动放到正确位置。
