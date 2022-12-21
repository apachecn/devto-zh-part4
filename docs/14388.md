# 在 EC2 Amazon Linux AMI 上运行木偶师/Chrome Headless

> 原文：<https://dev.to/kerion7/run-puppeteer-chrome-headless-on-ec2-amazon-linux-ami-2ae8>

—2019 年 6 月增加

这篇文章紧密基于 MockingBot 的同名文章。最初的文章帮助很大，但是由于新的库和包的版本，有些部分已经过时了。因此，我在这里记下了需要从原始文章中扩充的内容，供我自己和其他遇到同样情况的人参考！

当你读到文章的这一部分时:

> 现在只剩下 6 个了，因为 Amazon Linux 没有内置 gtk，我们需要从其他发行版借用包:

请注意，有些包已经不存在了。

[![](img/3e04753e38cae1b7723abe923845f273.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWbkiZm4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kaktzh7jni9zztct7e7.png)

## 提示💡:转到 URL(直到最后一个结尾斜杠)

例如对于:
[http://mirror . centos . org/centos/7/OS/x86 _ 64/Packages/atk-2 . 22 . 0-3 . el7 . x86 _ 64 . rpm](http://mirror.centos.org/centos/7/os/x86_64/Packages/atk-2.22.0-3.el7.x86_64.rpm)

导航到:
[http://mirror.centos.org/centos/7/os/x86_64/Packages/](http://mirror.centos.org/centos/7/os/x86_64/Packages/)

并搜索 **atk-** :

[![Perhaps by the time you read this article, the version have increased again!](img/e31abd8584f4883c5f6714e96bdf52f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--41Mjltu4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AiW1gnS_tQRgLr5DXhPLnLw.png)

因此，最接近的匹配应该是**atk-2 . 28 . 1–1 . el7 . x86 _ 64 . rpm**。

仔细阅读要用这种技术安装的项目列表，您应该就快完成了！

*差不多。*

[![Unlike what’s mentioned, there are still missing deps after installing the list of packages!](img/4f19a83a6de5d5203924814c43f60a3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gK6aFWga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AFFkWmu90BQoC7CdHbvUQEg.png)

唉，当您运行`ldd chrome | grep not`时，仍然会列出一些缺失的依赖项。在四处搜索之后，[这就敲定了交易](https://github.com/GoogleChrome/puppeteer/issues/391#issuecomment-325420271):

[![The number of emojis speak volumes about this comment!](img/b345c51ab4c8ef4a2589b3c624b6d3f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mGppp517--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A7lxwDWIzaLh4iKp-r3Y_Bg.png)

[![After the long yum install, one last dep!](img/d5d3804d1aa02a8e535b7f1ecd5d5918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E5HQnD6H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArXgvxL5km7vXiHg_6JFODQ.png)

运行长的`yum install`命令后，您将剩下最后一个依赖项: **libpng12.so.0** 。同理，在 Centos 包列表页面中寻找 libpng12 包的 **x86_64** 版本并下载。

[![](img/5ed2bf87ef2edc70a6390f0799abbe48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ezYQByLC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AZXuKZhTpdcDZv42a8MXRJQ.png)

最后，所有的依赖项都已经组装好了。

[![](img/90e9265d2683d6b6cb96c2a8537b789f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uziY-0r9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/f83bcd6317336c7c262fdb76d314e006/tenor.gif%3Fitemid%3D14098175)

## 结尾注

希望上面提到的提示会对你在 AMI 上安装木偶师有所帮助！如果这里有什么不清楚的地方，在原帖中也是不清楚的，一定要大声喊出来。同样，如果你已经尝试过，并注意到我的提示已经过时，也请让我知道。