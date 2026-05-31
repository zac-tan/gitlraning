# VS Code + Git + GitHub 多人协作快速入门

> **来源**：[CSDN博客 - 崔立坤ustc](https://blog.csdn.net/2301_79448707/article/details/158977213)（2026年3月）

---

## 1. 环境准备

### 1.1 下载安装

| 工具 | 下载地址 |
|------|----------|
| VS Code | [code.visualstudio.com/Download](https://code.visualstudio.com/Download) |
| Git (Windows) | [git-scm.cn/install/windows](https://git-scm.cn/install/windows) |

安装 Git 时全程默认选项，一路点"下一步"即可。完成后验证：

```
Win+R → 输入 cmd → 回车 → 输入 git --version
```

如果显示版本号（如 `git version 2.54.0`），说明安装成功。

### 1.2 告诉 Git 你是谁

每次提交都会记录你的名字和邮箱，这在团队协作中用来识别代码是谁写的：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

> 在 VS Code 中按 **Ctrl+~**（波浪号）可以打开内置终端，直接在上面执行命令。

---

## 2. 配置 GitHub 免密登录（SSH 密钥）

每次 push 都输密码很烦，配置 SSH 密钥一劳永逸。

### 2.1 生成密钥

```bash
ssh-keygen -t ed25519 -C "你的邮箱@example.com"
```

连续按**三次回车**，不要设密码。

### 2.2 复制公钥（⚠️ 只复制公钥！）

| 方法 | 操作 |
|------|------|
| 新手推荐 | 打开 `C:\Users\你的用户名\.ssh\` → 用记事本打开 `id_ed25519.pub` → 全选复制 |
| 最快 | Windows: `clip < ~/.ssh/id_ed25519.pub`   Mac: `pbcopy < ~/.ssh/id_ed25519.pub` |
| 手动 | `cat ~/.ssh/id_ed25519.pub` → 手动选中复制 |

> ⚠️ **私钥（`id_ed25519`，没有 `.pub` 后缀）相当于银行卡密码，打死也不能发给别人或填到任何网页上！**

### 2.3 添加到 GitHub

GitHub → 右上角头像 → Settings → SSH and GPG keys → New SSH key → 粘贴 → 保存。

---

## 3. 在 VS Code 中打开项目

### 场景 A：本地已有文件夹 → 初始化为 Git 仓库

1. VS Code 打开项目文件夹
2. 按 **Ctrl+Shift+G** 打开源代码管理面板
3. 点击 **"初始化存储库"（Initialize Repository）**

### 场景 B：克隆 GitHub 上的仓库到本地

1. 按 **F1**（或 Ctrl+Shift+P）打开命令面板
2. 输入 `Git: Clone` → 粘贴仓库地址

| 链接类型 | 适用场景 | 示例 |
|----------|----------|------|
| **HTTPS**（推荐新手） | 首次克隆时会弹出浏览器授权，之后免密 | `https://github.com/用户名/仓库名.git` |
| **SSH**（企业标准） | 配置了第 2 节的 SSH 密钥后用这个 | `git@github.com:用户名/仓库名.git` |

---

## 4. 配置 .gitignore

很多文件不需要上传：依赖包（`node_modules/`）、编译产物（`dist/`）、IDE 配置（`.vscode/`）、系统文件（`.DS_Store`）等。在项目根目录创建 `.gitignore`，Git 会自动忽略它们：

```gitignore
node_modules/
dist/
.vscode/
.DS_Store
*.log
.env
```

---

## 5. 核心三板斧：暂存 → 提交 → 推送

### 5.1 理解三个区域

```
工作区（修改文件）  →  暂存区（git add）  →  本地仓库（git commit）  →  远程仓库（git push）
```

### 5.2 VS Code 界面操作

1. 修改代码后，左侧源代码管理图标出现数字角标
2. 鼠标悬停在更改的文件上，点 **`+`** 号 → 文件进入"暂存的更改"
3. 顶部输入提交说明（建议用 `feat: xxx` / `fix: xxx` 格式）
4. 点击 **"提交"（Commit）** → 左下角点 **"同步更改"** 推送到 GitHub

### 5.3 命令行等效（了解即可）

```bash
git status                    # 查看改了哪些文件
git add .                     # 暂存所有改动
git commit -m "提交说明"       # 提交到本地仓库
git push origin main          # 推送到 GitHub
```

---

## 6. 多人协作核心：拉取与推送

### 6.1 协作黄金法则

> ⚡ **每天开始写代码前，先拉取（Pull）！否则很容易冲突。**

### 6.2 操作方式

| 操作 | VS Code | 命令行 |
|------|---------|--------|
| 拉取最新代码 | `...` 菜单 → 拉取 | `git pull` |
| 推送本地代码 | 左下角同步按钮 | `git push` |

### 6.3 首次推送（关联远程仓库）

如果是本地新建的项目，首次推送需要先关联 GitHub：

```bash
git remote add origin 你的仓库地址
git push -u origin main
```

---

## 7. 分支管理 —— 多人协作的关键

### 7.1 为什么需要分支？

多人同时改代码，**永远不要直接在 main 分支上开发**。每个人在自己的分支上干活，改完再合并。

### 7.2 常用操作

```bash
git branch                              # 查看所有分支（当前分支前有 * 号）
git checkout -b feature-xxx             # 创建并切换到新分支
git checkout main                       # 切回 main 分支
git merge feature-xxx                   # 把 feature-xxx 合并到当前分支
git branch -d feature-xxx               # 删除已合并的分支
```

### 7.3 多人协作标准流程

```
1. git pull                    # 拉取最新代码
2. git checkout -b my-feature  # 创建自己的分支
3. 写代码、提交、推送...
4. git push -u origin my-feature  # 推送分支到 GitHub
5. 在 GitHub 上创建 Pull Request（PR）
6. 队友 Review 代码后合并到 main
7. git checkout main && git pull  # 切回 main 并拉取最新
```

---

## 8. 解决冲突

### 8.1 什么时候会冲突？

**两个人修改了同一个文件的同一行代码**，Git 无法自动判断该保留谁的。

### 8.2 VS Code 冲突解决界面

冲突发生时，VS Code 会高亮冲突区域，并提供四个选项：

| 选项 | 含义 |
|------|------|
| **采用当前更改** | 保留自己的代码 |
| **采用传入更改** | 保留别人的代码 |
| **保留双方更改** | 两段都保留，手动调整 |
| **比较变更** | 左右分屏详细对比 |

### 8.3 重要！

解决完冲突后**必须重新暂存（+）并提交**，否则冲突标记不会消失。

---

## 9. 后悔药：撤销与回退

| 场景 | VS Code 操作 | 命令行 |
|------|-------------|--------|
| 改坏了想还原（未 add） | 源代码管理面板 → 文件旁的回退箭头（丢弃更改） | `git restore <文件名>` |
| 不小心点了 + 暂存（未 commit） | 暂存区文件旁点 **`-`** 号 | `git restore --staged <文件名>` |
| 提交错了，想撤销但保留代码 | `...` → 提交 → **撤销上次提交** | `git reset --soft HEAD^` |
| 彻底丢弃上次提交（代码和记录都丢） | 无界面操作 | `git reset --hard HEAD^` |
| 强制覆盖远程 | 无界面操作 | `git push origin main -f` |

> ⚠️ **`-f`（--force）是核弹级操作**：团队协作中**绝对不要用**，会把队友的代码覆盖掉。只在个人项目中使用。

---

## 10. 推荐插件

| 插件 | 为什么装 |
|------|----------|
| **GitLens** | 神器！光标停在任意一行代码上，能看见谁写的、什么时候写的、为什么写的。出问题知道找谁，学代码知道问谁。 |
| **Git Graph** | 可视化分支演变路线图，分支多了以后一眼看懂合并历史。 |
| **Git History** | 右键文件 → `Git: View File History` → 查看单个文件的完整修改时间线。 |

---

## 速查表

| 操作 | 命令 |
|------|------|
| 查看状态 | `git status` |
| 暂存所有 | `git add .` |
| 提交 | `git commit -m "说明"` |
| 推送 | `git push origin main` |
| 拉取 | `git pull` |
| 创建分支 | `git checkout -b 分支名` |
| 切换分支 | `git checkout 分支名` |
| 丢弃修改 | `git restore 文件名` |
| 撤销暂存 | `git restore --staged 文件名` |
| 撤销提交 | `git reset --soft HEAD^` |

---

> **核心就三点**：**暂存(+) → 提交(Commit) → 推送(Push)**，掌握这三板斧就能应付 80% 的日常工作。分支 + PR 是多人协作的关键，记住"每天先 Pull，在分支上开发，通过 PR 合并"。
