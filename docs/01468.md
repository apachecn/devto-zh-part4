# [Git]删除[Local | Remote]合并分支

> 原文：<https://dev.to/n350071/git-show-and-delete-merged-branch-even-if-it-s-squashed-58il>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## n350071 Git 票据

### n350071🇯🇵9 月 25 日 193 分钟阅读

#git](/n350071/my-git-note-5259)

## 删除本地分支

## 👍常规方法

`git branch --merged`列出合并到当前分支的分支。
因此，这将删除合并的分支。

```
git branch --merged | grep -v master | xargs git branch -d 
```

## 🦄删除压缩的合并分支

只有 3 步。

```
# show local branches
$ git branch
  * master
    branch1
    branch2
    branch3

# grep them
$ git log | grep 'branch1\|branch2\|branch3'
   Merge branch 'branch2' into 'master'

# force delete it
$ git branch -D branch2 
```

## 👍删除远程分支

```
# delete remote branches with some conditions
git branch --remote | grep -v "upstream" | egrep -v "origin/master|origin/deployment" | xargs git branch -dr 
```