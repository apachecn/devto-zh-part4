# windows 的 Bash 控制台:Hyper vs Gitbash(我目前的经验)

> 原文：<https://dev.to/nanythery/bash-consoles-for-windows-hyper-vs-gitbash-my-experience-so-far-555l>

你好。

一个月前，我开始使用命令行。我是 windows 用户(我不想在 Win/iOS 上引起争论)，所以我选择了我能找到的两个最相关的选项:

*   [GitBash cmd](https://gitforwindows.org/)
*   [超级](https://hyper.is/)

优点:

*   GitBash:整体简单。它允许所有 UNIX 命令，包括拖放文件夹，直接捕获 URL。
*   超级:高度可定制的格式，字体颜色，字体大小等相对容易的方式。它是跨平台的，所以它允许你在任何操作系统下工作。它还支持多标签结构，所以你不必打开新的应用程序。它也有几个插件。

缺点:

*   GitBash:它确实允许[一些定制](https://alanbarber.com/post/how-to-customize-the-git-for-windows-bash-shell-prompt/),尽管不像 Hyper 那么容易。它不支持多标签，因此，每当你运行服务器，需要继续工作，你需要再次启动应用程序。
*   Hyper:对我来说，它一直在突然关闭。当您需要在关闭这样一个 mongod 之前退出进程时，这有点棘手。它迫使你寻找一个解决方法来关闭服务器，等等。另一方面，我发现 hyper 即使在休息时也更消耗内存。Hyper 也能工作，并且需要 gitbash，所以你最终会同时安装这两个应用。为了使它作为 bash 控制台工作，而不仅仅是作为 Windows cmd 工作，需要预先对它进行配置。

结论:

我相信还有更多的事情要做。到目前为止，我对游戏机的使用还是相当基础的。然而，我发现 Hyper 的自我关闭问题非常令人讨厌，这使我更喜欢与 gitbash 合作。至少在我的电脑上，它消耗的内存更少，也更稳定。然而，我必须承认我太喜欢 Hyper 了！它灵活、可定制，比 gitbash 更具视觉吸引力。但是现在我选择更多地使用 gitbash。
如果你没有经历过这些自我封闭的问题，也不急于记忆，那么我肯定会推荐 Hyper。

请随意分享您使用这些应用程序的经验，如果您知道其他可以用作 Windows bash 控制台的应用程序，也可以分享。

感谢您的阅读！