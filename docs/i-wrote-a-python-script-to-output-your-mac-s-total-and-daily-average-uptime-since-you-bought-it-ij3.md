# 我写了一个 python 脚本来输出你的 Mac 自购买以来的总(和日平均)正常运行时间！

> 原文：<https://dev.to/marklambe/i-wrote-a-python-script-to-output-your-mac-s-total-and-daily-average-uptime-since-you-bought-it-ij3>

所以我很好奇我的 MacBook Pro 以每小时多少钱的方式值多少钱，并决定找出自从我买了它以来我用了多少小时。

这被证明是相对简单的，因为可以运行一些命令来获得我们需要的信息。

我们需要做的第一件事是找到在苹果安装时创建的`/private/var/db/.AppleSetupDone`文件的年龄...完成(🔝文件名 tbh)。

然后我们得到操作系统硬盘报告的使用小时数。如果你更换了你的硬盘，这就限制了事情的发展，但是我不知道如何解决这个问题。

从那里开始，这是一些非常简单的数学，我们有了答案！

代码在 Github 上

我还没有花大量的时间在这上面，所以可能有更好的方法来做任何事情，公关的欢迎😊