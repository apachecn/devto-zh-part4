# 忘记了最近使用的 git 分支的名称？逮到你了。

> 原文：<https://dev.to/ivarojha/forgot-the-name-of-your-recently-used-git-branch-gotcha-fam-161c>

将此别名添加到终端的 rc 或配置文件中。对于大多数用户来说，是主目录中的`.bashrc`或`.bash_profile`。启动一个新的终端标签，`cd`到任何 git 仓库，并尝试命令`gitrecent`。

```
alias gitrecent="git for-each-ref --sort=committerdate refs/heads/ --format='%(HEAD) %(color:yellow)%(refname:short)%(color:reset) - %(color:red)%(objectname:short)%(color:reset) - %(contents:subject) - %(authorname) (%(color:green)%(committerdate:relative)%(color:reset))'" 
```

来源:本 [stackoverflow](https://stackoverflow.com/a/5188364/1756957) 答案