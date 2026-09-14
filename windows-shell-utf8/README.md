# windows-shell-utf8 —— 让 Qoder 在 Windows 跑 shell 时不乱码（Skill）

一个 Qoder **技能（Skill）**。装到项目后，Qoder 执行 shell / 终端 / PowerShell / git 命令时会先把编码切到 UTF-8，避免中文输出乱码（mojibake）。

## 解决什么

Windows PowerShell 5.1 默认 GBK(936) / gb2312 编码，git 等工具的 UTF-8 中文输出、以及 PowerShell 对 stderr 的中文渲染会变成乱码。本 skill 让 Qoder 在跑命令前统一切 UTF-8。

## 文件夹内容

| 文件 | 作用 |
|---|---|
| `SKILL.md` | 技能主文件：根因 + 设置片段 + 兜底前缀 + 验证 |
| `README.md` | 本使用文档 |

## 安装（复制到你的项目即可用）

把**整个 `windows-shell-utf8/` 文件夹**复制到目标项目的技能目录：

```
<你的项目>/.qoder/skills/windows-shell-utf8/
```

Windows PowerShell（在本仓库根目录执行，替换目标路径）：

```powershell
New-Item -ItemType Directory -Force "<你的项目>\.qoder\skills" | Out-Null
Copy-Item -Recurse ".\windows-shell-utf8" "<你的项目>\.qoder\skills\windows-shell-utf8"
```

想让**所有项目**都可用：复制到用户级 `~/.qoder/skills/windows-shell-utf8/`。复制后**重启会话**（或 CLI `/skills reload`）加载。

## 使用

装好后通常**无需手动调用**——当 Qoder 要执行 shell 命令、或输出出现乱码时，会依据 description 自动加载并按其中做法切 UTF-8。也可主动说：

> 用 windows-shell-utf8，接下来跑命令都用 UTF-8 别乱码

## 说明与边界

- 技能靠 description 触发，属「尽力而为」。若要**强制每条命令都生效**，可另配一条 `trigger: always_on` 的规则，正文放 SKILL.md 里那 4 行设置片段——rule 每次会话必被注入，比 skill 更硬。
- 只影响显示 / 传输编码，不改变命令行为与结果。
- PowerShell 7 / Core 默认已 UTF-8，一般无需本 skill。

## 卸载

删除 `<你的项目>/.qoder/skills/windows-shell-utf8/` 文件夹，重启会话即可。
