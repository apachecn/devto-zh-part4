# 清理本地 GIT 分支

> 原文：<https://dev.to/msurguy/clean-local-git-branches-4fc7>

要清理除当前的`HEAD`、`master`和`staging`之外的所有本地分支，您可以使用这个脚本:

```
git branch | grep -v "master\|staging" | xargs git branch -D 
```

您可以将更多的分支添加到您想要跳过的分支列表中，例如，如果除了当前分支、主分支和暂存分支:
之外，您还想要保留`dev`分支

```
"master\|staging\|dev" 
```

为了进一步实现自动化，您可以通过修改您的`~/.bash_profile`文件:
来为您的终端添加一个别名

```
...
alias clean-branches="git branch | grep -v \"master\|staging\" | xargs git branch -D"
... 
```

现在，当您在项目目录中时，您可以使用`clean-branches`命令来自动执行这个修剪。

尽情享受吧！