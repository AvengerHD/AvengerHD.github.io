---
layout: default
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

3) 添加文件到版本库(staged)

# single file
git add file

# current directory and sub directories
git add .

4) 显示当前状态

git status

5) 配置邮箱地址和用户名

git config user.name "xxx"
git config user.email "xxx@xxx.xxx"

6) 提交变更

# need git add operation before this
git commit

# contains git add operation
git commit file

7) 查看提交历史

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

8) 查看提交差异

git diff [commit1] [commit2]

9) 删除和重命名

# need commit before this, will delete from index and current directory
git rm file

# only delete from index
git rm --cached file

git mv file1 file2

10) 创建版本库副本

git clone repo

# bare repo, only for clone, fetch and push, no commits
# 权威版本库
git clone --bare repo

11) 查看删除当前 Git 配置
 
# list
git config -l

# unset
git config --unset --global user.email

12) 查看所有文件的 SHA1

git ls-files -s

# only stage files
git ls-files --stage



13) .gitignore 规则

字面置文件名匹配任何目录中的同名文件
目录名由末尾的反斜线(/)标记，匹配同名的目录和子目录，不匹配文件和符号链接
一个星号只能匹配一个文件或者目录名

14) 特殊符号引用

HEAD 当前分支最近提交
ORIG_HEAD 原始 HEAD
FETCH_HEAD 最近 fetch 的分支 HEAD 的简写
MERGE_HEAD 正在进行合并进 HEAD 的提交 

15) 查看文件中指定行是哪些提交修改的

git blame -L [start,end] file

16) 创建分支

# will not change current branch
git branch branchName

# create branch from special commit
git branch branchName [commitId]

17) 查看分支

# list local branches
git branch

# list remote branches
git branch -r

# list all
git branch -a

18) 检出分支

# only checkout exist branch
git checkout branch

# create and chekcout new branch
git branch -b branchName

# Error: Your local changes to the following files would be overwritten by checkout.
# some solutions
1) git checkout -m branch and resolve confilts
2) 

19) 删除分支

git branch -d branchName

# force delete
git branch -D branchName

20) diff 差异比较

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

21) 合并分支

git merge branch

# if exist conflicts
1) resolve conflicts manually
2) git add
3) git commit

22) 中止或者重新启动合并

# reset current work dir and index to the version before merge
git reset --hard HEAD

# reset after a new merge commit
git reset --hard ORIG_HEAD

23) reset 重置(危险操作)

# --soft: only HEAD
# --mixed: HEAD and index, no work dir
# --hard: HEAD and index and work dir

git reset --soft commitId

24) 在当前分支引入指定 commit

git cherry-pick commitId

git cherry-pick startcommit..endcommit

25) 撤销某个提交

git revert commitId

26) 修改最新提交(使用修改后的 commit 替换最新的 commit)

git commit --amend

27) rebase 变基操作

git rebase branchName

28) rebase 交互式编辑 rebase 顺序

# edit last 3 commit
git rebase -i branch~3

29) stash 储藏(暂时保存)

git stash save [message]

git stash pop

30) 远程版本库操作

git remote -v 

git remote add origin xxxxx

git remote remove origin

# fetch meta data, not merge
git fetch [origin] [branch]

# fetch meta data, andmerge
git pull [origin] [branch]

git push [origin] [branch]

31) 添加和删除远程分支

# add
git push [localbranch] [repo]:[remotebranch]

# delete
git push [repo] --delete [remotebranch]


32) 清理操作

git clean 



Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](another-page).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### [](#header-4)Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
