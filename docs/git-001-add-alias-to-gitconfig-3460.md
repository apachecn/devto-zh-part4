# Git 001 ~添加别名。gitconfig~

> 原文：<https://dev.to/0xkoji/git-001-add-alias-to-gitconfig-3460>

我使用`iTerm`和`Github Desktop`来使用 Git 和 Github，因为有时我不想键入任何命令来使用 Github。

但是，有时候更喜欢输入命令而不是点击 lol。

举个例子，

当我想重命名一个分支时，我需要点击`branch` > `rename`，重命名该分支，然后点击`Rename`。另一方面，使用`iTerm`,我只需要输入以下内容。实际上，重命名有捷径，但是我不记得了😝

```
$ git branch -m <new_name> 
```

Enter fullscreen mode Exit fullscreen mode

这个命令并不长，但是软件工程师、开发人员、web 开发人员基本上都很懒。(我的第一任 CTO 告诉我的`engineers/developers should be lazy`)

所以我们需要做的是尽可能缩短命令😂

就 git 命令而言，我们可以做以下事情。

1.  `git`->-`g`
2.  为`branch` - > `b`添加别名到`.gitconfig`

其实你可以把`gb`加为`git branch`，但是我感觉在用。gitconfig 可以更好

将以下内容添加到您的。zshrc，。巴沙尔等。

```
alias g='git 
```

Enter fullscreen mode Exit fullscreen mode

第二步
添加以下内容到`.gitconfig`文件在你的主文件夹

```
[alias]
    # basic commands
    b = branch
    p = push
    c = commit
    cm = commit -m
    f = fetch
    s = status
    st = stash
    rh = reset --hard
# update
    m = merge
    l = log
    last = log -3 HEAD --decorate 
```

Enter fullscreen mode Exit fullscreen mode

### 最后一个来自 [@vlasales](https://dev.to/vlasales) ，谢谢大家的评论！

现在我们可以输入下面的

```
$ g b -m <new_name> 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt Text](img/d256c792bea26b922d6d4470f14ecca8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1cYt7JNz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p2cxvhdsw8hlf1cvlzce.gif)

[@rmnvsl](https://dev.to/rmnvsl) 谢谢大家的建议！