# Qoder 参考资料投喂教程

> **场景**：你手上有一批**不属于本项目、也不想提交进仓库**的外部资料——需求文档（PRD）、原型图 / UI 稿、竞品截图、接口文档、设计图……你想让 Qoder 读它们、分析出需求，再据此开发（vibe coding）。
> 本教程讲清楚三件事：**放哪、怎么喂、各格式的坑**。

---

## 0. 一句话结论

- **纯外部参考、不入库** → 放进项目里一个被 `.gitignore` 忽略的文件夹（推荐 `_reference/`）。Qoder 照样能读，但永不提交、永不推送。
- **只想看一眼、一次性** → 直接拖 / 粘贴进对话框，零足迹。
- **属于项目、要团队共享的文档** → 那才放 `docs/` 并提交（见第 6 节对比，本教程不展开）。

---

## 1. 核心原理：为什么 gitignored 文件夹能被 Qoder 读到

Qoder 的文件工具读的是**磁盘上的工作区文件**，跟这个文件有没有被 git 跟踪**完全无关**。

于是：

- 资料放进 `_reference/`，只要它在项目目录里，Qoder 的读取工具就能打开（**包括图片**）。
- 同时 `_reference/` 被 `.gitignore` 忽略 → `git add .` / `commit` / `push` 都碰不到它 → 它**不进入版本库、不属于项目**。

这就是「**能被 AI 读，但对 git 隐形**」——正好满足「只是参考、不属于项目」的诉求。

---

## 2. 三种投喂方式（按「跟项目沾多少」排序）

| 方式 | 放哪 | 持久性 | 足迹 | 适合 |
|---|---|---|---|---|
| ① 贴进对话 | 聊天框 | 仅本次对话 | 零 | 一次性「看这张图分析下」 |
| ② gitignored 暂存夹（**推荐**） | 项目内 `_reference/` | 跨会话持久 | 本机有、仓库无 | 反复参考、又不入库 |
| ③ 项目外目录 | 如 `D:\qoder-refs\` | 跨会话持久 | 完全在项目外 | 一点都不想留在项目目录里 |

**方式①：直接贴进对话。** 图片拖 / 粘贴到聊天框，文档用附件。只活在这次对话，关掉就没，适合临时一次性。

**方式③：项目外目录。** 放项目外任意位置，在对话里直接给 Qoder **绝对路径**，它用读取工具打开。缺点：不在工作区 → 不被索引、`@` 选不到，只能每次手动给绝对路径。

**方式②** 是推荐做法，下一节完整落地。

---

## 3. 方式② 完整落地（推荐）

### 3.1 建文件夹

在项目根建 `_reference/`（名字随意，`_` 前缀表示「内部 / 非交付物」）：

```powershell
mkdir _reference
```

### 3.2 加进 `.gitignore`

```gitignore
# 外部参考资料（文档/图片）：仅供本地喂给 Qoder 分析需求，不属于本项目、不提交
_reference/
```

> 这行注释会随 `.gitignore` 一起提交，等于把「这个文件夹是干嘛的」写进仓库，团队成员一看就懂。

### 3.3 验证已被忽略

```powershell
# 命中规则即被忽略（对文件夹本身、以及里面的文件都验证一遍）
git check-ignore -v _reference
git check-ignore -v "_reference/prd.md"
git check-ignore -v "_reference/home.png"

# git status 里应该只看到 .gitignore 改动，看不到 _reference/
git status --short
```

**预期**：三条 `check-ignore` 都输出 `.gitignore:<行号>:_reference/ ...`；`status` 里**不出现** `_reference`。

### 3.4 组织建议（可选子文件夹）

```
_reference/
├── requirements/   # PRD、用户故事（Markdown 最佳）
├── design/         # 原型图、UI 稿、流程图（png/jpg/webp）
└── misc/           # 竞品截图、接口文档、背景资料
```

### 3.5 怎么喂给 Qoder

- **`@` 引用**：对话里 `@_reference/design/home.png`、`@_reference/requirements/prd.md`。
- **给路径**：「读 `_reference/requirements/prd.md` 和 `_reference/design/home.png`，先拆解需求」。
- Qoder 的读取工具**支持直接读 png/jpg/webp 图片**（视觉理解），不用你手动描述图里有什么。

---

## 4. 按文件类型的处理（决定分析质量）

| 类型 | 直接喂 | 建议 |
|---|---|---|
| Markdown / txt | 最佳 | 能读能全文搜索，需求首选写成 md |
| 图片 png / jpg / webp | 支持读图 | 原型图 / 截图直接放，`@` 引用 |
| gif / bmp / svg / tiff | 不稳 | 先转 png / webp |
| PDF / Word / Excel | 解析有限 | **转 Markdown，或截图成 png** 再喂 |

> 经验：越是纯文本 / Markdown，Qoder 理解越准；二进制文档（PDF / Office）先转格式或截图，别指望直接解析。

---

## 5. vibe coding 实操流程

1. **丢素材**：把 PRD、原型图放进 `_reference/`。
2. **先分析、别急着写代码**：
   > 「读 `_reference/requirements/prd.md` 和 `_reference/design/home.png`，先帮我拆解出：功能清单、页面 / 接口、验收标准、以及你不确定的点。**先不要写代码**。」
3. **对齐需求**：你补充 / 纠正 Qoder 的理解。
4. **再开发**：
   > 「需求确认无误，按上面的拆解实现首页，先给我文件改动计划。」
5. **迭代**。

> 关键节奏：**素材 → 拆解需求 → 对齐 → 开发**。让 Qoder 先「读懂参考」再动手，比一句模糊 prompt 稳得多。

---

## 6. 对比：属于项目的文档 vs 不属于项目的参考

| | 属于项目的文档 | 不属于项目的参考 |
|---|---|---|
| 例子 | 项目自己的 README、API 文档、架构说明 | 客户给的 PRD、竞品截图、临时原型 |
| 放哪 | `docs/`（提交） | `_reference/`（gitignore） |
| 是否入库 | 是：提交、团队共享 | 否：只在本机、永不提交 |
| Qoder 能读 | 能 | 能（读磁盘，与 git 无关） |

**别把外部参考塞进 `docs/` 提交上去**——那会让「不属于项目」的东西污染仓库，甚至泄露敏感资料。

---

## 7. 常见坑 / FAQ

- **空的 `_reference/` clone 到别的机器不存在**：git 不跟踪空文件夹，加上它被忽略——这正是「不属于项目」的体现。换机器时自己再建一个即可。
- **`.gitignore` 对「已提交」的文件无效**：如果某个参考文件**之前已经被提交**，再加 gitignore 不会移除它，需要 `git rm --cached <file>` 才能停止跟踪（本地文件保留）。所以参考资料**从一开始就放 `_reference/`**，别先提交再想忽略。
- **敏感资料隔离**：客户 NDA 文档、含密钥 / 个人信息的截图，务必放 `_reference/`（gitignored），避免误 push 到远程泄露。
- **多项目复用同一批参考**：可放项目外（方式③）给绝对路径，或每个项目各建一个 `_reference/`。

---

## 8. 命令速查

```powershell
# 建文件夹
mkdir _reference

# 忽略它（PowerShell 追加一行到 .gitignore）
Add-Content .gitignore "`n_reference/"

# 验证被忽略
git check-ignore -v _reference
git status --short              # 不应出现 _reference

# 万一之前误提交了：停止跟踪但保留本地文件
git rm -r --cached _reference
```
