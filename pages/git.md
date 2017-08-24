---
layout: default
title: Git 基础操作
permalink: /pages/git
---
## 协作原则 

```bash
1] 如果一个分支已经公开，就不应该重写、修改该分支的任何部分
2] 已经发布出去的代码不应该再执行 rebase
3] 建议使用 fork + pr(pull request) 协作开发
```

## 1) 初始化 git 版本库
```bash
git init
```

## 2) 添加文件到版本库(staged)
```bash
# single file
git add file

# current directory and sub directories
git add .
```

## 3) 显示当前状态
```bash
git status
```

## 4) 配置邮箱地址和用户名
```bash
git config user.name "xxx"
git config user.email "xxx@xxx.xxx"
```

## 5) 提交变更
```bash
# need git add operation before this
git commit

# contains git add operation
git commit file
```

## 6) 查看提交历史
```bash
# all commits
git log

# show special commit details
git show [commitid]

# show branch simple commits history
git show-branch --more=10

# show only one record details
git log -1 -p

# show graph
git log --graph  
```  

## 7) 查看提交差异
```bash
git diff [commit1] [commit2]
```

## 8) 删除和重命名
```bash
# need commit before this, will delete from index and current directory
git rm file

# only delete from index
git rm --cached file

git mv file1 file2
```

## 9) 创建版本库副本
```bash
git clone repo

# bare repo, only for clone, fetch and push, no commits
# 权威版本库
git clone --bare repo
```

## 10) 查看删除当前 Git 配置
```bash
# list
git config -l

# unset
git config --unset --global user.email
```

## 11) 查看所有文件的 SHA1
```bash
git ls-files -s

# only stage files
git ls-files --stage
```

## 12) .gitignore 规则
```
字面置文件名匹配任何目录中的同名文件
目录名由末尾的反斜线(/)标记，匹配同名的目录和子目录，不匹配文件和符号链接
一个星号只能匹配一个文件或者目录名
```

## 13) 特殊符号引用
```
HEAD 当前分支最近提交
ORIG_HEAD 原始 HEAD
FETCH_HEAD 最近 fetch 的分支 HEAD 的简写
MERGE_HEAD 正在进行合并进 HEAD 的提交 
```

## 14) 查看文件中指定行是哪些提交修改的
```bash
git blame -L [start,end] file
```

## 15) 创建分支
```bash
# will not change current branch
git branch branchName

# create branch from special commit
git branch branchName [commitId]
```

## 16) 查看分支
```bash
# list local branches
git branch

# list remote branches
git branch -r

# list all
git branch -a
```

## 17) 检出分支
```bash
# only checkout exist branch
git checkout branch

# create and chekcout new branch
git branch -b branchName

# Error: Your local changes to the following files would be overwritten by checkout.
# some solutions
1) git checkout -m branch and resolve confilts
```

## 18) 删除分支
```bash
git branch -d branchName

# force delete
git branch -D branchName
```

## 19) diff 差异比较
```bash
# differences between current work dir and index
git diff 

# differences between current work dir and special commit
git diff commit

# differences between index and special commit
git diff --cached commit

# differences between two commits
git diff commit1 commit2

# differences between staged and not staged
git diff HEAD

# differences in special dir or files
git diff  [dir or files]

# -w : ignore blank charactors
# --stat: simpe differences
# --color: pretty
```

## 20) 合并分支
```bash
git merge branch

# if exist conflicts
1) resolve conflicts manually
2) git add
3) git commit
```

## 21) 中止或者重新启动合并
```bash
# reset current work dir and index to the version before merge
git reset --hard HEAD

# reset after a new merge commit
git reset --hard ORIG_HEAD
```

## 22) reset 重置(危险操作)
```bash
# --soft: only HEAD
# --mixed: HEAD and index, no work dir
# --hard: HEAD and index and work dir

git reset --soft commitId
```

## 23) 在当前分支引入指定 commit
```bash
git cherry-pick commitId

git cherry-pick startcommit..endcommit
```

## 24) 撤销某个提交
```bash
git revert commitId
```

## 25) 修改最新提交(使用修改后的 commit 替换最新的 commit)
```bash
git commit --amend
```

## 26) rebase 变基操作
```bash
git rebase branchName
```

## 27) rebase 交互式编辑 rebase 顺序
```bash
# edit last 3 commit
git rebase -i branch~3
```

## 28) stash 储藏(暂时保存)
```bash
git stash save [message]

git stash pop
```

## 29) 远程版本库操作
```bash
git remote -v 

git remote add origin xxxxx

git remote remove origin

# fetch meta data, not merge
git fetch [origin] [branch]

# fetch meta data, andmerge
git pull [origin] [branch]

git push [origin] [branch]
```

## 30) 添加和删除远程分支
```bash
# add
git push [localbranch] [repo]:[remotebranch]

# delete
git push [repo] --delete [remotebranch]
```

## 31) 清理操作
```bash
git clean 
```