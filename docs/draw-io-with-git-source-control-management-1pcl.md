# 带 Git 源码控制管理的 draw.io？

> 原文：<https://dev.to/dietertroy/draw-io-with-git-source-control-management-1pcl>

我是 [draw.io](https://www.draw.io) 的超级粉丝——一个用来绘制网络拓扑、UML、架构&的工具。我们的团队大量使用这个工具，尽管彼此独立，不一定共享。

我有一个想法..您可以导出为. xml。每个用户编辑他们的图表，导出为。 *xml* (到设备)并添加到 SCM 存储库。如果另一个用户想要编辑它，他们可以签出该分支..进行编辑并检入/添加拉取请求等。

快速图解使用 draw.io:
[![diagram](img/e9b937f208cb892e1f0d383faee3476e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TDRJF1nz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.troydieter.com/static/edit_drawio_process.png)

draw.io + SCM = **成功**？

其他人用过这种组合吗？