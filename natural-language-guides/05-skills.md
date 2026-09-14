# 用自然语言创建 ⑤skills（可复用技能）

## 这项是干嘛的
把一套专业流程 / 领域知识打包，AI 匹配场景时自动加载，或 `/技能名` 手动触发。rules 是「不许怎样」，skill 是「教你怎么干一件事」。

## 直接对 Qoder 说（复制即用）
> 帮我创建一个项目级 skill：`.qoder/skills/changelog-writer/SKILL.md`，功能是根据 git 提交生成 CHANGELOG。frontmatter 的 name 用 changelog-writer，description 要写清功能 + 何时触发 + 关键词。正文给分步流程、防编造约束和输出格式。

或用内置技能：
> 用 create-skill 帮我做一个「<某类任务>」的技能

## Qoder 会做什么
1. 建 `.qoder/skills/<名>/SKILL.md`（可选加 REFERENCE.md / scripts/ / templates/）
2. frontmatter 填 name（小写连字符 ≤64，与目录同名）+ description（≤1024，含触发关键词）
3. 正文写流程 / 约束 / 输出格式

## 生成在哪 + 怎么验证
- 路径：`<项目根>/.qoder/skills/<名>/SKILL.md`
- 验证：**重启会话**或 CLI `/skills reload`；然后 `/<技能名>` 或描述对应需求看是否触发。

## 常见坑
- description 太模糊 → **永不触发**。必须写「何时用 + 关键词」。
- name 只能小写字母 / 数字 / 连字符，且要和目录名一致。
- 官方对「用户级 vs 项目级同名 skill 谁优先」表述矛盾，实践中避免同名。
