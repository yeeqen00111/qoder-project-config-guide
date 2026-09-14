# 用自然语言创建 ③settings.json（团队共享设置）

## 这项是干嘛的
JSON 配置，承载团队统一约定：权限（放行 / 询问 / 拒绝）、Hooks、模型、语言。提交 Git 后团队自动生效。

## 直接对 Qoder 说（复制即用）
配权限白名单：
> 帮我在 .qoder/settings.json 配置权限：allow 放行 Read/Glob/Grep 和测试命令 `python -m pytest *`；deny 拒绝 `rm`、`git push --force`、读 `.env`；language 设为 Chinese。

加钩子：
> 在 settings.json 加一个 PreToolUse 钩子，匹配 Bash，检测到 `git push --force` 就 exit 2 阻塞；Windows 下 shell 用 powershell。

## Qoder 会做什么
1. 创建 / 更新 `.qoder/settings.json`
2. 按官方字段写 `permissions.allow/ask/deny`、`hooks`、`language` 等
3. 提示需重启会话生效

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/settings.json`
- 验证：**重启会话**后，触发一条 deny 的操作看是否被拦；allow 的操作看是否不再弹窗。

## 常见坑
- 权限 / MCP 类改动**需重启**会话生效。
- deny 里务必含 `Read(.env)` 防密钥进上下文。
- 团队共享用 settings.json；个人本机覆盖用 settings.local.json（见下一篇）。
