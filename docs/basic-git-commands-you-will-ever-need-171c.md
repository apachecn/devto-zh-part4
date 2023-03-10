# 您需要的基本 git 命令

> 原文：<https://dev.to/vickylance/basic-git-commands-you-will-ever-need-171c>

## 初始化 git 回购

初始化 git 项目

```
cd <path to root folder of project>
git init 
```

## 检查已更改文件的状态

检查文件状态的变化

```
git status 
```

## 添加文件进行提交

添加所有已更改的文件

```
git add -A 
```

添加当前目录中的所有更改

```
git add . 
```

添加特定文件

```
git add <file path> 
```

添加多个文件

```
git add <file1 path> <file2 path> 
```

## 将文件提交给 git

使用提交消息
进行提交

```
git commit -m "commit message" 
```

## 管理远程回购

添加远程 URL

```
git remote add origin <repo URL> 
```

添加多个远程 URL

```
git remote add <any key for remote URL> <repo URL> 
```

将代码推送到远程回购

```
git push 
```

从远程回购中提取代码

```
git pull 
```

## 操纵 git 分支

以当前分支为基础创建新分支

```
git checkout -b <new branch name> 
```

签出到现有分支

```
git checkout <branch name> 
```

将新分支推送到远程回购

```
git push -u <remote key> <branch name> 
```

将分支代码合并到当前分支

```
git checkout <current branch>
git merge <other branch> 
```

## 贮藏

隐藏代码而不提交

```
git stash 
```

取出隐藏的代码

```
git stash pop 
```