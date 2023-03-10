# 保持干净你的 git repos！

> 原文：<https://dev.to/jahboo/keep-clean-your-git-repos-52o9>

许多开发人员没有保持他们的本地存储库干净。不过，有一种方法可以让它自动化。让我们来看看几个有用的命令:

*清除远程中不存在的分支引用:*

```
$ git fetch --prune 
```

Enter fullscreen mode Exit fullscreen mode

在获取之前，删除遥控器上不再存在的任何远程跟踪引用。

*估算有多少家分行合并到 dev:*

```
$ git branch --merged dev | wc -l 
```

Enter fullscreen mode Exit fullscreen mode

`--merged`选项可以将列表过滤到你已经合并到给定分支的分支中。挤压和重定基础合并通常不会被`--merged`检测到。

*并入 dev 的分行列表:*

```
$ git branch --merged dev 
```

Enter fullscreen mode Exit fullscreen mode

*并入 dev 的远程分支机构列表:*

```
$ git branch --merged dev --remote 
```

Enter fullscreen mode Exit fullscreen mode

*如果你很勇敢那么:*

```
$ git branch --merged dev | egrep -v "(^\*|master|dev)" | xargs git branch -d 
```

Enter fullscreen mode Exit fullscreen mode

它会删除合并到 dev 中的所有本地分支(除了 dev 和 master)。
**这是一个潜在的破坏性操作。它可以删除实际需要的分支。**
所以如果你使用不同的方法来处理 Git，你可以手动删除一些分支。我希望你不要储存所有的旧树枝，是吗？