---
title: git使用
date: 2017-02-21 11:23:51
categories: 运维 #文章分类
tags: [git] #文章标签，可以一次添加多个标签
---

git使用...

<!-- more -->

## github

- gists
- Octokit

## git bash

### git reflog

reflog 就是强力后悔药。它记录了所有的 commit 操作记录，便于错误操作后找回记录。

```sh
git reflog
// 拿到要回滚的错误commitHash
git reset --hard commitHash
```

### git cherry-pick

- 复制多个

```sh
git cherry-pick commit1 commit2
```

上面的命令将 commit1 和 commit2 两个提交应用到当前分支。

- 复制区间

```sh
git cherry-pick commit1^..commit2
```

上面的命令将 commit1 到 commit2 这个区间的 commit 都应用到当前分支（包含 commit1、commit2），commit1 是最早的提交。

### git grep <关键词> ：搜索含有关键词的文件

### git blame <文件名>：查看指定文件每一行的提交人和提交时间

### git log -p <文件名>：查看指定文件的每一次提交和改动

### git 提交规范插件 commitizen,然后用 git-cz

```sh
npm install commitizen -g
```

### git tag

```sh

# 为当前分支所在的提交记录打上轻量标签。
git tag <lightweght_name>
# 为某次具体的提交记录打上轻量标签。
git tag <lightweght_name> <commit SHA-1 value>
# 为当前分支所在的提交记录打上附注标签。
git tag -a <anotated_name> -m <tag_message>

# 列出所有的标签名。
git tag
# 删除某个标签，本质上就是移除 .git/refs/tags/ 中对应的文件。
git tag -d <tag_name>
# 显示标签对应提交记录的具体信息。
git show <tag_name>
# 推送某个标签到远程仓库。
git push <remote> <tag_name>
git push -u origin master <tag_name>
# 推送所有标签到远程仓库。
git push <remote> --tags
# 删除远程仓库中的某个标签
git push <remote> --delete <tag_name>
```

### git 提交规范(参考 vue 规范)

feature 增加新功能
fixbug 修复问题/BUG
style 不影响程序逻辑的代码修改(修改空白字符，格式缩进，补全缺失的分号等，没有改变代码逻辑)
perf 优化/性能提升
refactor 重构
revert 撤销修改
docs 文档/注释
chore 依赖更新/脚手架配置修改等

```
feat: 添加了xxx功能
fix: 修复了xxx功能
```

### git 修改 author

```sh
# 修改上次commit的Author信息
git commit --amend --reset-author
# 修改以前的commit的Author信息
git rebase -i HEAD~3
# 进入vim，将要修改的commit 1开头的pick改成edit,保存退出
git commit --amend --reset-author
gut rebase --continue
```

### git 想amend 上上个commit

如果你想要修改上上个 commit，可以使用 `git rebase -i HEAD~2` 命令来进入交互式 rebase 模式。然后，在编辑器中将要修改的 commit 的行的开头的 `pick` 改为 `edit`，保存并退出编辑器。接着，使用 `git commit --amend` 命令来修改该 commit，并使用 `git rebase --continue` 命令继续 rebase 过程。最后，使用 `git push --force` 命令来强制推送修改后的提交。需要注意的是，强制推送可能会覆盖其他人的提交，因此请确保你知道自己在做什么。

### git checkout commitId [files]

回滚部分文件到指定版本

### gists

(gists)[https://docs.github.com/en/get-started/writing-on-github/editing-and-sharing-content-with-gists/creating-gists]

### git branch

```shell
git branch -a
git branch -vv

git branch localbranch
git branch --set-upstream-to=origin/V5.3.9_SZBANK V5.3.9_SZBANK
```

### gerrit

```shell

git push -u origin V5.3.10_powerjob:refs/for/V5.3.10_powerjob

```