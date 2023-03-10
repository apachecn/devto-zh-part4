# 使用单个别名可以极大地提高 git 的生产率

> 原文：<https://dev.to/xoubaman/a-huge-productivity-boost-in-git-with-a-singe-alias-3pij>

> 小细节可以造成巨大的不同

在您的 shell 中添加一个别名，将`g`用作`git`。一天下来，你将节省大量的打字时间。

```
# in your .bashrc or equivalent
alias g='git' 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你将做`g pull`而不是极其冗长的`git pull`。

作为奖励，我的 Git 配置中的一些内容。以下全部在`~/.gitconfig`中:

*   使用全局`.gitignore`与通常的嫌疑人(。DS_Store，Thumbs.db 等。)

```
[core]
excludesfile = ~/.gitignore-global 
```

Enter fullscreen mode Exit fullscreen mode

*   我一直想`pull --rebase`，所以我默认启用了它:

```
[pull]
    rebase = true 
```

Enter fullscreen mode Exit fullscreen mode

*   Git 附带了一个几乎不为人知的自动更正功能。如果你输入了错误的命令，它会在设定的时间后执行。让它在十分之一秒内完成:

```
[help]
    autocorrect = 5 
```

Enter fullscreen mode Exit fullscreen mode

*   以及我当前的 git 别名列表，以防对某些人有所启发。他们打印了实际的命令“不要忘记我正在做的事情”:

```
[alias]
    s        = !echo 'git status -s' && git status -s
    aa       = !echo 'git add .' && git add .
    c        = !echo 'git commit -m' && git commit -m
    co       = !echo 'git checkout' && git checkout
    amend    = !echo 'git commit --amend --no-edit' && commit --amend --no-edit
    amendall = !echo 'git add . && git commit --amend --no-edit' && git add . && git commit --amend --no-edit
    syncwith = !echo 'git pull --rebase origin' && git pull --rebase origin
    lg       = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)%Creset' --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

最后的建议:试着优化你一天中反复做的事情。