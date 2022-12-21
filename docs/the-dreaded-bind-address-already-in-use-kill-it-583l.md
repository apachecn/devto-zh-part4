# 可怕的端口错误“绑定:地址已被使用”，杀死它！

> 原文：<https://dev.to/dechamp/the-dreaded-bind-address-already-in-use-kill-it-583l>

TL；dr: `lsof -i :<PORT>`，然后获取 PID 并使用它运行下一个命令(小心运行这个命令)`kill <PID>`。如果你想要一个一体机，你可以用这个！`kill $(lsof -i :<PORT> | awk 'NR>1 {print $2}')`

所以你在漫游，你开始你的服务，只是发现你有一些东西在你需要的同一个端口上运行。

多。

这里有一个快速的解决方法。只需换成 3000，或者你的端口。

`lsof -i :<port>`

您应该会看到类似这样的内容。如果您得到一个空响应，那么端口上没有运行任何东西。
[![output of lsof command](img/f00dbe4f8765f969de5585632004353d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--4_e56hHX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eltkedfhwyba4pc8yf8a.png)

现在你有了 PID，在我们的例子中是`28913`。

下一部分需要你的注意！我将向您展示如何终止进程，但是只有当您确信您知道您将要终止的进程时，才能这么做。

如果您杀死一个您不知道正在做什么的进程，它可能会对您的系统产生负面影响。

如果你关闭了一个正在保存的应用程序，它可能会破坏你的数据。

所以，在盲目运行下一个命令之前，要聪明一点。

我看到它是`com.docke`所以我知道它是 docker。我发现杀了它。

`kill -9 28913`。

**更新**
所以正如本·辛克莱提到的，避免将-9 和`kill`一起使用，因为这很危险。第一次尝试`kill $PORTNUMBER`

您现在已经释放了端口。如果你运行`man kill`，你会看到不同信号的列表。

```
 1       HUP (hang up)
 2       INT (interrupt)
 3       QUIT (quit)
 6       ABRT (abort)
 9       KILL (non-catchable, non-ignorable kill)
 14      ALRM (alarm clock)
 15      TERM (software termination signal) 
```

Enter fullscreen mode Exit fullscreen mode

-9 强制它无论如何都要退出。

如果你想做一个 one in all，其中它查找端口，然后试图立即杀死它，你可以做这个命令。`kill $(lsof -i :<PORT> | awk 'NR>1 {print $2}')`，如果您得到消息`kill: not enough arguments`，那么这是因为没有为端口找到进程，所以您的端口应该是空闲的。否则，它会终止并在成功时返回一个新的空行。

有更好的解决办法吗？留言评论！

* * *

瓦里马德有限责任公司。

目前的项目有[https://charactergenerator4000.com](https://charactergenerator4000.com)和 [https://coder.exchange](https://coder.exchange) 。请检查它们，并让我们知道你的想法。