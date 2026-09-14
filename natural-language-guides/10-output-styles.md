# 用自然语言创建 ⑩output-styles（回复风格）

## 这项是干嘛的
调整 AI 回复的**语气、详略、组织方式**（更简洁 / 更偏教学 / 固定结构），只在系统提示之上**叠加表达偏好**，不改变核心身份与安全约束。rules 管「代码怎么写」，output-style 管「话怎么说」。

## 直接对 Qoder 说（复制即用）
> 帮我建 `.qoder/output-styles/concise-cn.md`，风格是简洁中文：先给结论再给理由，不复述已知信息，代码只留关键部分。再在 .qoder/settings.json 里用顶层 `outputStyle` 引用它。

## Qoder 会做什么
1. 建 `.qoder/output-styles/<名>.md`（可选 frontmatter name/description，正文即风格说明）
2. 在 settings.json 写顶层 `"outputStyle": "<名>"`
3. 提示重启生效

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/output-styles/<名>.md`
- 验证：**重启会话**看回复风格是否变化；临时试可用启动参数 `--output-style <名>`（免重启，仅本次）。

## 常见坑
- 改 `outputStyle` **需重启**（临时用 `--output-style` 免重启）。
- 只写表达层偏好，别试图用它绕过安全约束（无效）。
- 同名时项目级 > 用户级 > 插件 > 内置；language 定语种、output-style 定风格，二者可叠加。
