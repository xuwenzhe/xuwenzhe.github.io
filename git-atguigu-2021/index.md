# Git/Github笔记


## 1. Git概述
Git这种分布式版本控制工具，客户端提取的不是最新版本的文件快照，而是把代码仓库完整地镜像下来（本地库）。这样任何一处协同工作用的文件发生故障，事后都可以用其他客户端的本地仓库进行恢复。因为每个客户端的每一次文件提取操作，实际上都是一次对整个文件仓库的完整备份。

## 2. Git常用命令

* 设置用户签名 `git config --global user.name 用户名`
* 设置用户签名 `git config --global user.email 邮箱`

注意：这里设置用户签名和将来登陆Github的账号没有任何关系。

* 初始化本地库 `git init`
* 查看本地库状态 `git status`
* 将工作区的文件添加到暂存区 `git add 文件名`
* 将暂存区的文件提交到本地库 `git commit -m "日志信息" 文件名`
* 查看版本信息（HEAD经过的历史记录） `git reflog`
* 查看版本详细信息 `git log`
* 版本穿梭 `git reset --hard 版本号` （版本号可以通过git reflog查询）

## 3. Git分支操作
## 4. Git团队协作机制
## 5. Github操作
## 6. IDEA集成Git
## 7. IDEA集成Github
