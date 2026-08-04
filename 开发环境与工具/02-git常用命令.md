# Git 常用命令大全

> Git 用于记录代码变化、创建分支、与 GitHub 同步。本笔记以 `learn-nanobot` 和 `nanobot` 为例。

## 先理解四个概念

- **工作区（working tree）**：你正在编辑的文件。
- **暂存区（staging area）**：准备提交的改动清单。
- **提交（commit）**：一次可追溯的版本快照。
- **分支（branch）**：从某个版本分出的独立开发线路。

常见流程：

```text
修改文件 → git add → git commit → git push
工作区      暂存区      本地仓库      GitHub
```

## 初次检查仓库

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git status` | 查看当前分支、修改和暂存状态。 | 每次操作前后优先运行。 |
| `git status --short --branch` | 用紧凑格式显示分支与改动。 | 快速检查工作区。 |
| `git log --oneline -10` | 查看最近 10 次提交。 | 了解近期历史。 |
| `git show HEAD` | 查看当前提交内容。 | 检查最近一次提交改了什么。 |
| `git remote -v` | 查看远程仓库地址。 | 确认 push 会发往哪里。 |

示例：检查 Nanobot 当前是否处于教学分支：

```bash
cd /Users/qzh/AI-Agent/nanobot
git status --short --branch
git log --oneline -5
```

## 查看文件改动

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git diff` | 查看尚未暂存的改动。 | 编辑后先审阅。 |
| `git diff --staged` | 查看已经暂存、但尚未提交的改动。 | 提交前最后确认。 |
| `git diff HEAD -- 文件名` | 查看某文件相对当前提交的所有改动。 | `git diff HEAD -- README.md` |
| `git blame 文件名` | 显示每一行最后由哪个提交修改。 | 理解一段代码来源。 |

## 提交自己的改动

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git add 文件名` | 将指定文件放入暂存区。 | `git add README.md` |
| `git add .` | 暂存当前目录及子目录的所有改动。 | 确认 `git status` 后再用。 |
| `git restore --staged 文件名` | 将文件移出暂存区，但保留工作区改动。 | 误执行 `git add` 后使用。 |
| `git commit -m "说明"` | 创建一次本地提交。 | `git commit -m "docs: add uv notes"` |
| `git commit --amend` | 修改最近一次提交。 | 仅在提交尚未推送或确认可改时使用。 |

安全的提交流程：

```bash
git status
git diff
git add 开发环境与工具/01-uv常用命令.md
git diff --staged
git commit -m "docs: add uv command notes"
```

## 分支操作

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git branch` | 列出本地分支，`*` 表示当前分支。 | `git branch` |
| `git switch 分支名` | 切换到已有分支。 | `git switch main` |
| `git switch -c 新分支名` | 创建并切换到新分支。 | `git switch -c docs/add-git-notes` |
| `git branch -d 分支名` | 删除已合并的本地分支。 | `git branch -d docs/add-git-notes` |
| `git branch -D 分支名` | 强制删除未合并分支。 | 使用前确认分支内容不再需要。 |

示例：回到 Nanobot 最新主线：

```bash
cd /Users/qzh/AI-Agent/nanobot
git switch main
```

示例：回到教程兼容版本：

```bash
git switch tutorial-compatible-2026-04-02
```

> 分支切换前先运行 `git status`。如果有未提交改动，Git 可能拒绝切换，防止你丢失文件。

## 与 GitHub 同步

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git fetch origin` | 下载远程更新，但不改当前文件。 | 安全地先查看远程变化。 |
| `git pull` | 下载并合并当前分支的远程更新。 | 开始工作前同步。 |
| `git push` | 将本地提交上传到对应远程分支。 | 提交后备份或协作。 |
| `git push -u origin 新分支名` | 首次把新分支推送到 GitHub 并建立跟踪关系。 | 新建分支首次上传。 |
| `git branch -vv` | 查看本地分支跟踪哪个远程分支。 | 排查 push/pull 目标。 |

典型协作流程：

```bash
git pull
# 编辑文件
git add .
git commit -m "docs: improve setup guide"
git push
```

> `git pull` 可能带来他人的改动；在你有重要未提交内容时，先提交或使用 `git stash`。

## 临时保存改动

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git stash` | 暂时收起未提交改动，使工作区恢复干净。 | 临时切换分支前使用。 |
| `git stash push -m "说明"` | 带说明地保存改动。 | `git stash push -m "draft docs"` |
| `git stash list` | 查看所有临时保存。 | 找回某次暂存。 |
| `git stash pop` | 恢复最新 stash，并删除该 stash。 | 回到原工作后使用。 |
| `git stash apply` | 恢复 stash，但保留 stash 记录。 | 想保留备份时使用。 |

示例：临时切换分支而不提交半成品：

```bash
git stash push -m "正在写 uv 笔记"
git switch main
# 完成别的工作后
git switch docs/add-uv-notes
git stash pop
```

## 恢复与撤销：先理解再执行

| 命令 | 效果 | 风险 |
| --- | --- | --- |
| `git restore 文件名` | 丢弃该文件尚未暂存的改动。 | 会丢失工作区中的修改。 |
| `git restore --staged 文件名` | 取消暂存，保留文件修改。 | 安全。 |
| `git revert 提交ID` | 创建一个“反向提交”来撤销某次已提交改动。 | 协作中推荐，历史保留。 |
| `git reset --soft HEAD~1` | 撤销最近一次提交，保留改动并保持暂存。 | 本地提交整理时使用。 |
| `git reset --hard HEAD~1` | 删除最近提交及工作区改动。 | 高风险，除非确定否则不要用。 |

推荐原则：已经推送或可能被他人使用的提交，优先选择 `git revert`；不确定时先用 `git status`、`git log` 和 `git diff` 看清状态。

## 克隆、初始化与忽略文件

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `git clone 仓库地址` | 下载一个已有 Git 仓库。 | `git clone git@github.com:HKUDS/nanobot.git` |
| `git init` | 在当前目录创建一个新的 Git 仓库。 | 自己新建项目时使用。 |
| `git clone --branch 分支名 仓库地址` | 克隆指定分支。 | 获取一个固定教学分支。 |
| 编辑 `.gitignore` | 让 Git 忽略不应提交的文件。 | 忽略 `.venv/`、`.env`、缓存。 |

Python 项目常见 `.gitignore` 内容：

```gitignore
.venv/
__pycache__/
.pytest_cache/
.env
```

> `.env` 常放 API Key，绝对不要提交到 GitHub。

## 学习 Nanobot 时最常用的命令

```bash
# 教程项目：查看你写的笔记是否有改动
cd /Users/qzh/AI-Agent/learn-nanobot
git status

# Nanobot：确认使用教学兼容分支
cd /Users/qzh/AI-Agent/nanobot
git status --short --branch

# 查看当前分支最近的提交
git log --oneline -5
```

## 提交前检查清单

- 先运行 `git status`，确认没有把无关文件一起加入。
- 用 `git diff --staged` 检查将要提交的内容。
- 不提交 `.venv/`、`.env`、API Key、日志和大型生成文件。
- 提交信息写清楚“做了什么”，例如 `docs: add git command notes`。
- 不确定某条恢复命令的影响时，先停止并查看 `git status`。
