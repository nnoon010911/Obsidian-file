---
title: git使用方法
type: 知识
tags:
  - 知识
date created: 2026-04-11 04:55:15
modify: 2026-04-11 04:55:47
---
## 一、基本使用方法

Git 主要用于管理本地项目的版本，GitHub 主要用于将本地项目同步到远程仓库，实现备份、托管和后续更新。  
在实际使用过程中，最核心的操作流程是：

**修改本地文件 → 添加到暂存区 → 提交本地版本 → 推送到 GitHub**

常用命令如下：

git status  
git add .  
git commit -m "本次修改说明"  
git push

其中：

- `git status` ：查看当前仓库状态，判断是否有文件被修改、是否有未跟踪文件
- `git add .` ：将当前目录下所有改动加入暂存区
- `git commit -m "说明"` ：提交一次本地版本记录
- `git push` ：将本地提交上传到 GitHub 远程仓库

如果是第一次上传项目到 GitHub，通常还需要先完成仓库初始化、关联远程仓库等操作，典型流程如下：

git init  
git add .  
git commit -m "初始化项目"  
git branch -M main   
git remote add origin 仓库地址  
git push -u origin main

以后项目已经建立好后，日常更新一般只需要：

git add .  
git commit -m "更新说明"  
git push

---

## 二、这次实际操作中的主要问题

### 1. 直接 `git commit` ，但文件没有先 `add`

一开始执行 `git commit -m "初始化项目"` 时，Git 提示：

- `Untracked files`
- `nothing added to commit but untracked files present`

这说明文件虽然存在，但还只是“未跟踪状态”，没有加入暂存区，因此无法提交。  
这个问题反映出 Git 的提交流程不能跳步，必须先 `add` 再 `commit` 。

解决方法是先执行：

git add .

然后再执行：

git commit -m "初始化项目"

---

### 2. 本地推送到 GitHub 时被拒绝

后续执行 `git push origin main` 时，出现了：

- `rejected`
- `non-fast-forward`
- `Updates were rejected because the tip of your current branch is behind its remote counterpart`

这说明远程仓库中已经存在比本地更新的提交记录，本地分支落后于远程分支，因此 Git 不允许直接推送。

本质原因是：

- GitHub 远程仓库里已经有内容
- 本地仓库历史与远程仓库历史不一致
- 需要先把远程内容拉下来再推送

原本应先执行：

git pull origin main --allow-unrelated-histories

再执行：

git push origin main

---

### 3. 拉取远程内容时，本地修改会被覆盖

在执行 `git pull` 时，又出现了新的报错：

- `Your local changes to the following files would be overwritten by merge`

这说明本地文件已经修改过，而远程仓库也对相同文件有内容，Git 为了防止本地修改被覆盖，拒绝直接合并。

这类问题的本质是：

- 本地有未处理的改动
- 远程也有对应文件
- 合并前必须先保存本地修改

解决方法是先把本地改动提交：

git add .  
git commit -m "本地修改先保存"

再继续执行：

git pull origin main --allow-unrelated-histories --no-rebase

最后再：

git push origin main