---
title: git使用方法
tags:
  - 知识
modify: 2026-04-11 15:56:27
type: 知识
date created: 2026-04-11 04:55:15
---
# Git 使用笔记

## 1. Git 的基本流程

Git 用来管理本地项目版本，GitHub 用来保存远程仓库。  
平时最常用的流程就是：

**修改文件 → 暂存改动 → 提交版本 → 推送到远程仓库**

常用命令：

git status  
git add .  
git commit -m "本次修改说明"  
git push

含义：

- `git status` ：查看当前仓库状态
- `git add .` ：把当前目录下改动加入暂存区
- `git commit -m "说明"` ：提交一次本地版本
- `git push` ：把本地提交推送到 GitHub

如果是第一次上传项目，一般流程是：

git init  
git add .  
git commit -m "初始化项目"  
git branch -M main  
git remote add origin 仓库地址  
git push -u origin main

以后日常更新通常只需要：

git add .  
git commit -m "更新说明"  
git push

---

## 2. 这次遇到的问题

### 问题 1：没有 `add` 就直接 `commit`

报错现象：

- `Untracked files`
- `nothing added to commit but untracked files present`

原因：  
文件只是存在，但还没有加入暂存区，所以不能直接提交。

解决方法：

git add .  
git commit -m "初始化项目"

---

### 问题 2： `push` 被拒绝

报错现象：

- `rejected`
- `non-fast-forward`
- `Updates were rejected because the tip of your current branch is behind its remote counterpart`

原因：  
远程仓库比本地更新，本地分支落后于远程分支，不能直接推送。

解决方法：

git pull origin main --allow-unrelated-histories  
git push origin main

---

### 问题 3： `pull` 时提示本地修改会被覆盖

报错现象：

- `Your local changes to the following files would be overwritten by merge`

原因：  
本地文件已经改过，远程对应文件也有变化，直接拉取会覆盖本地内容，所以 Git 拒绝合并。

解决方法：

git add .  
git commit -m "本地修改先保存"  
git pull origin main --allow-unrelated-histories --no-rebase  
git push origin main

---

### 问题 4：文件删除或重命名后， `git status` 显示删除和新增

现象：

- 原文件显示 `deleted`
- 新文件显示 `Untracked files`

原因：  
Git 检测到旧文件没了，同时又出现了新文件。  
这通常是删除、重命名，或者编辑器重新生成文件导致的。

解决方法：  
如果这些变化本来就是想保留的，就执行：

git add -A  
git commit -m "update posts"  
git push origin main

说明：  
`git add -A` 比 `git add .` 更适合处理“新增 + 删除 + 修改”同时存在的情况。

如果是误删文件，可以恢复：

git restore "source/_posts/2026-04-10 Friday. md"

---

### 问题 5：Git Bash 中文文件名显示乱码

现象：  
文件名显示成类似这种形式：

"source/_posts/Zotero\350\256\347\275\256. md"

原因：  
这是 Git Bash 对中文路径显示不友好，不一定是文件有问题。

结论：  
一般不会影响提交和推送，只是终端显示不直观。

---

### 问题 6：GitHub 仓库改名后， `push` 出错

现象：

- `This repository moved. Please use the new location`

原因：  
GitHub 上仓库改名了，但本地仓库的远程地址还是旧的。

解决方法：

git remote set-url origin 新仓库地址  
git remote -v

例如：

git remote set-url origin https://github.com/nnoon010911/NNOON-Blog.git  
git remote -v

---

### 问题 7：仓库改名后，远程分支状态不一致， `push` 仍失败

报错现象：

- `remote rejected`
- `cannot lock ref 'refs/heads/main'`
- `is at ... but expected ...`

原因：  
本地记录的远程分支状态和 GitHub 当前状态不一致，所以 Git 拒绝直接推送。

解决方法：

git remote set-url origin 新仓库地址  
git fetch origin  
git pull --rebase origin main  
git push origin main

如果 `pull --rebase` 过程中冲突，需要先解决冲突，再执行：

git add .  
git rebase --continue  
git push origin main

---

## 3. 这次学到的几点

1. Git 不能跳步骤，必须先 `add` 再 `commit`
2. `push` 失败很多时候是因为本地落后于远程
3. `pull` 前如果本地有改动，最好先提交保存
4. 文件重命名时，Git 有时会显示成“删除旧文件 + 新增新文件”
5. 中文文件名在 Git Bash 里显示奇怪，不一定真有问题
6. GitHub 仓库改名后，本地 `origin` 也要跟着改
7. 远程状态不一致时，要先 `fetch` 和 `pull --rebase`

---

## 4. 日常最稳的使用流程

平时更新项目时，比较稳妥的流程是：

git status  
git add .  
git commit -m "修改说明"  
git pull --rebase origin main  
git push origin main

如果涉及删除、重命名，建议用：

git add -A

如果怀疑仓库地址不对，先检查：

git remote -v

---

## 5. 一句总结

Git 的核心不是“改完就上传”，而是：

**先看状态，再暂存，再提交，再同步远程，最后推送。**