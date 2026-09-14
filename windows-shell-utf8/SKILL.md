---
name: windows-shell-utf8
description: 在 Windows 上执行 shell / 终端 / PowerShell / git 等命令时，确保使用 UTF-8 编码，避免中文等非 ASCII 输出变成乱码（mojibake）。当即将运行任何终端/shell 命令、或命令输出出现乱码方块/问号/锟斤拷、或用户环境是 Windows PowerShell 时触发。
---

# Windows Shell UTF-8

让 Qoder 在 Windows 上跑 shell 命令时统一用 UTF-8，杜绝中文乱码。

## 问题根因

Windows PowerShell 5.1 默认代码页是 GBK(936)、控制台输出编码 gb2312、管道编码 us-ascii。当 git 等工具输出 UTF-8 中文、或 PowerShell 用中文渲染原生命令的 stderr 错误记录时，经这套非 UTF-8 管道被错误解码 → 乱码。（PowerShell 7 / Core 默认已是 UTF-8，通常不受影响。）

## 核心做法：执行 shell 前先切 UTF-8

在会话**首次执行 shell 命令前**，先运行一次下面的设置片段（幂等，可重复运行）：

```powershell
chcp 65001 > $null
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
[Console]::InputEncoding  = [System.Text.Encoding]::UTF8
$OutputEncoding           = [System.Text.Encoding]::UTF8
```

Qoder 的终端会话通常**复用**同一 shell，设置一次即对本会话后续命令持续生效；新会话需重设。不确定是否已设时，直接重跑（无副作用）。

## 兜底：给单条命令加前缀

若无法确保已设置（例如每条命令都是全新 shell），把设置与目标命令写在同一行：

```powershell
$OutputEncoding=[Console]::OutputEncoding=[Text.Encoding]::UTF8; <你的命令>
```

## 验证

```powershell
chcp                               # 期望 Active code page: 65001
[Console]::OutputEncoding.WebName  # 期望 utf-8
"中文测试"                          # 期望正常显示，不是乱码
```

## 配套建议

- git 把中文文件名显示成 `\346\226\207` 转义时：`git config --global core.quotepath false`
- 尽量少对原生命令用 `2>&1`——PowerShell 会把 stderr 包成错误记录并按错误编码重渲染，是乱码高发点；确需合并时先把编码设好。
- 想对本机所有交互式 PowerShell 一劳永逸：把上面 4 行写进 `$PROFILE`；但注意 Qoder 跑的是**非交互 shell、可能不加载 `$PROFILE`**，故本 skill 采用「会话内设置」这一更可靠路径。

## 边界

只改显示 / 传输编码，不改变任何命令的行为与结果；仅作用于 Windows PowerShell 环境。
