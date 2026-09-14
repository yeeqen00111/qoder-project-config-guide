# 用自然语言创建 ④settings.local.json（个人本地覆盖）

## 这项是干嘛的
和 settings.json 完全同构、字段相同，但**只在本机生效**且**优先级最高**。适合放个人临时开关、本地放宽的权限。**不提交 Git**。

## 直接对 Qoder 说（复制即用）
> 帮我建 .qoder/settings.local.json，只覆盖 permissions.allow 放开 `Bash(python *)` 方便我本地调试；并确认它已被 .gitignore 忽略。

## Qoder 会做什么
1. 创建 `.qoder/settings.local.json`，**只写你要覆盖的字段**
2. 检查 / 追加 `.gitignore` 里的 `.qoder/settings.local.json`
3. 提示重启会话生效

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/settings.local.json`
- 验证：`git status` 应**看不到**该文件（已被忽略）；重启会话后本地权限生效。

## 常见坑
- **一定 gitignore**，否则个人配置会污染团队仓库。
- 只写要覆盖的字段，不必全量复制 settings.json（逐层深度合并）。
- 优先级：本地级 > 项目级 > 用户级。
