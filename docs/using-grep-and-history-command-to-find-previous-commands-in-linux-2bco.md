# 使用 grep 和 history 命令查找 Linux 中以前的命令

> 原文：<https://dev.to/asimdahall/using-grep-and-history-command-to-find-previous-commands-in-linux-2bco>

你在你的 linux 终端输入命令，它很长而且很难记住。需要再次向终端输入命令。您会发现很难搜索到所需的命令。你不断按下向上箭头，直到找到所需的命令。在这个过程中时间被浪费了。

这就是`history`命令的用武之地。转到您的 linux 终端，键入命令历史。您会发现您以前使用的命令列表，如下所示。
[![Use of history command](img/331b7fb19fbdabb0aec5748bb815ba84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vC18iX67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/NTRz40S.png)

在上图中，您可以看到您之前使用过的所有命令以及它们各自的编号。现在让我们假设你必须使用命令`git clone https://github.com/asimdahall/wethanks/`，它是数字`56`。现在你可以通过输入
来执行这个命令

```
asims-MacBook-Pro:~ !56 
```

Enter fullscreen mode Exit fullscreen mode

它将执行 56 号上的命令。现在，假设我们必须搜索之前使用过的命令。为此，我们可以将 grep 与 history 命令结合使用。这里举个例子。我必须搜索命令

`xcrun simctl shutdown all`

之前用过但是不记得完整命令只记得`xcrun`。所以为了搜索命令，我们可以像这样组合`history`和`grep`命令:

```
asims-MacBook-Pro:~ asim$ history|grep xcrun 
```

Enter fullscreen mode Exit fullscreen mode

之后，您将获得以下输出

```
 422  xcrun simctl shutdown all 
  452  xcrun simctl shutdown all 
  454  xcrun simctl shutdown all 
  502  history|grep xcrun 
```

Enter fullscreen mode Exit fullscreen mode

您将获得所有使用了`xcrun`命令的命令及其各自的编号。现在您可以像这样执行命令:

```
asims-MacBook-Pro:~ asim$ !422 
```

Enter fullscreen mode Exit fullscreen mode

然后执行所需的命令。

我为我糟糕的语法感到抱歉。欢迎反馈和建议。谢谢你。

你可以在推特上关注我