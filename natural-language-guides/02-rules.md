# 用自然语言创建 ②rules（编码规则）

## 这项是干嘛的
约束 AI 生成代码的风格与行为，相当于团队编码规范。Markdown + YAML frontmatter，放 `.qoder/rules/`，随 Git 共享。

## 直接对 Qoder 说（复制即用）
始终生效的编码规范：
> 帮我在 .qoder/rules/ 建一条 always_on 的 Python 编码规则，要求：遵循 PEP8、缩进 4 空格禁 Tab、公开函数带类型注解和 docstring、异常必须处理禁裸 except、新增依赖先说明理由。frontmatter 用 `trigger: always_on`。

按文件类型生效：
> 建一条 glob 规则，只在编辑 `**/*.json` 时生效，内容是「数据文件是唯一事实源、改动需向后兼容」；frontmatter 用 `trigger: glob` 加 `glob: **/*.json`。

## Qoder 会做什么
1. 在 `.qoder/rules/<名>.md` 写文件
2. 按你要的触发类型填 frontmatter（四种见下）
3. 正文逐条列出「要 / 不要」，可配一正一反示例

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/rules/<名>.md`
- 验证：新会话让 AI 写一段相关代码，看是否遵守规则。

## 四种触发类型怎么说
- 始终生效：「trigger 用 `always_on`」
- 场景触发：「trigger 用 `model_decision`，description 写清什么任务时启用」
- 文件专属：「trigger 用 `glob`，glob 填 `**/*.ts`」
- 手动引用：「trigger 用 `manual`，我会话里用 `@规则名` 引用」

## 常见坑
- 取值是**下划线**写法：`always_on`、`model_decision`（不是 alwaysApply 之类）。
- 不确定格式时，先用 IDE 设置 UI 建一条同类规则，打开文件照抄。
- 所有活跃规则合计 ≤ 100,000 字符；仅自然语言，不支持图片/链接。
