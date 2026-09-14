# 用自然语言创建 ⑨.mcp.json（外部工具接入）

## 这项是干嘛的
声明项目需要哪些 MCP 服务器，让 AI 调用外部能力（抓网页、查数据库、调第三方 API）。放**项目根目录**，提交后队友拉代码即获得相同工具环境。

## 直接对 Qoder 说（复制即用）
> 帮我在项目根建 .mcp.json，接入两个 MCP：一个 SSE 远程的 fetch（`type: sse` + url），一个 STDIO 本地的 github（`command: npx`, args 装 @modelcontextprotocol/server-github），token 用 `${GITHUB_TOKEN}` 占位、真值我放 .env。

## Qoder 会做什么
1. 建 `<项目根>/.mcp.json`
2. 顶层 `mcpServers`，每服务器一键；SSE 用 `type`+`url`，STDIO 用 `command`+`args`+`env`
3. 密钥用 `${ENV}` 占位，不写真值

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.mcp.json`
- 验证：让 AI 调用对应工具；首次使用会要求**批准**（批准后记入 settings 的 `mcp.enabledProjectMcpServers`）。

## 常见坑
- token **绝不硬编码**进 .mcp.json，用 `${ENV_VAR}` 占位，真值放 `.env`（gitignore）。
- 团队信任场景可在 settings 设 `mcp.enableAllProjectMcpServers: true` 免逐个批准。
- 字段两套命名：IDE 侧 `enableAllProjectMcpServers`/`enabledProjectMcpServers`；CLI 侧顶层 `mcpServers` + `mcp.allowed`/`mcp.excluded`。
