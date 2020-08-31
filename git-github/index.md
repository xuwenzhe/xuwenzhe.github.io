# Git与Github使用


{{< figure src="/git-github/worktree-staging-gitdir.png" class="center" width="800" >}}

The git directory acts as a database for all the changes tracked in Git and the working tree acts as a sandbox where we can edit the current versions of the files.


## 1. 基本操作

```bash
git init # 创建仓库

# 登陆当前操作系统的用户范围 (否则为项目级别)
git config --global user.name "My Name"
git config --global user.email "me@example.com"

git status # 显示 working tree, staging area
# 创建文件new-file.py
git add new-file.py
git commit -m "[message]"
git status
# 修改new-file.py
git commit -a -m "[messsage]" # -a: shortcut of "git add, git commit" for the modified
git log # 查看提交(当前分支)
git log --oneline # 查看提交（简洁显示）
git reflog # 查看HEAD曾经经过的commit

# throws away unstaged changes, younger commits becomes dangling (can be retrieved via reflog if within days)
git reset --hard [commitID] 
git reset --hard HEAD^^^ # back 3 commits
git reset --hard HEAD~[n] # back n commits
```

{{< figure src="/git-github/reflog.gif" class="center" width="1000" >}}

{{< figure src="/git-github/reflog-reset.gif" class="center" width="1000" >}}

{{< figure src="/git-github/hard-reset.gif" class="center" width="1000" >}}

```bash
git diff file.py # compare file.py between work tree and staging area
git diff HEAD file.py # compare between work tree and HEAD commit
git show commitID # show details (+-) for one specific commit
git mv file.py newname.py # rename a file
git rm newname.py # remove a file
```

{{< figure src="https://git-scm.com/book/en/v2/images/lifecycle.png" class="center" width="800" >}}

## 2. 分支合并

```bash
git branch -v # 查看分支
git branch feature # 创建分支feature
git checkout feature # 切换分支feature
git checkout -b feature # 创建并切换分支feature
git branch -d feature # 删除分支feature

# 合并： 切换到接受修改的分支（e.g. master）增加新内容(e.g. feature上的新内容)
git checkout master
git merge feature

# 冲突
# <<<<<<< HEAD
# abc (当前分支修改)
# =======
# def (并入分支修改)
# >>>>>>> feature
git add files
git commit -m "conflict resolved, merging -> merged"
```

## 3. 远程合作

```bash
git remote -v # 查看本地保存的远程库地址
git remote add origin https://github.com/[username]/[reponame].git
git push origin master

git clone https://github.com/[username]/[reponame].git

# pull = fetch + merge
git fetch origin master
git checkout origin/master
git merge origin/master
git pull origin master

# 如果不是基于Github远程库的最新版所做的修改，不能推送，必须先pull
# pull后如果进入冲突状态，则解决冲突后即可再推送(git commit, git push)
```

```bash
# 跨团队协作 1) fork 2) clone 3) modified 4) pull request
```

## 4. 工作流

{{< figure src="/git-github/git-workflow.png" class="center" width="1000" >}}

Steps:

1. (junior)创建分支feature
2. (junior)推送feature分支
3. (senior)切换分支，审查代码
4. (senior)切换master，合并feature
5. (senior)推送master分支

# TODO

## 撤销

```bash
git checkout unstaged.py # revert unstaged changes

git reset HEAD staged.py # revert staged changes
```

```bash
# overwrite the previous commit with staged changes.
# ONLY LOCAL: avoid amending commits that have already been made public
git commit --amend
```

```bash
git revert HEAD # roll back to previous commit

git revert commitID # roll back
```

## 远程
```bash
git push # push changes to remote repo
```

"origin" is the default name for the remote repo

```bash
git remote -v # list remote repos verbosely
git remote show origin # describe a single remote repo
git branch -r # list remote branches
```

What's the main difference between git fetch and git pull? `git fetch` fetches remote updates but doesn't merge; `git pull` fetches remote updates and merges.

```bash
git fetch
git log origin/master
git merge origin/master # merge into local
```

`git remote update` will update all of your branches set to track remote ones, but not merge any changes in.

`git fetch` will update only the branch you're on, but not merge any changes in.

`git pull` will update and merge any remote changes of the current branch you're on. This would be the one you use to update a local branch.

```bash
git log --graph --oneline --all
git log -p origin/master
```

```bash
git push -u origin new-feature # push to "origin" remote repo, with "new-feature" branch
```

Rebasing instead of merging rewrites history and maintains linearity, making for cleaner code.

```bash
git checkout new-feature
git rebase master
git log --graph --oneline
git checkout master
git merge new-feature # fast forward merge, linear history
```

```bash
git push --delete origin new-feature # delete remote branch
git branch -d new-feature # delete local branch
```


```bash
git fetch
git rebase origin/master
git add health_checks.py
git rebase --continue
```


{{< figure src="/git-github/fast-forward.gif" class="center" width="1000" >}}

{{< figure src="/git-github/no-fast-forward.gif" class="center" width="1000" >}}

{{< figure src="/git-github/merge-conflict.gif" class="center" width="1000" >}}

{{< figure src="/git-github/rebase.gif" class="center" width="1000" >}}

{{< figure src="/git-github/rebase-i.gif" class="center" width="1000" >}}

{{< figure src="/git-github/rebase-squash.gif" class="center" width="1000" >}}

{{< figure src="/git-github/soft-reset.gif" class="center" width="1000" >}}

{{< figure src="/git-github/revert.gif" class="center" width="1000" >}}

{{< figure src="/git-github/cherry-pick.gif" class="center" width="1000" >}}

{{< figure src="/git-github/fetch.gif" class="center" width="1000" >}}

{{< figure src="/git-github/pull.gif" class="center" width="1000" >}}



## 参考

[Git scm official](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository)

[Understanding branches in Git](https://blog.thoughtram.io/git/rebase-book/2015/02/10/understanding-branches-in-git.html)

[CS Visualized: Useful Git Commands - Lydia Hallie](https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1)

[What is the difference between 'git remote update', 'git fetch' and 'git pull'?](https://stackoverflow.com/a/17712553)

[Listing and deleting Git commits that are under no branch (dangling?)](https://stackoverflow.com/a/4528593)

[How can I undo git reset --hard HEAD~1?](https://stackoverflow.com/a/21778)

[How do I check out a remote Git branch?](https://stackoverflow.com/questions/1783405/how-do-i-check-out-a-remote-git-branch)


