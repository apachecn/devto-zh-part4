# 重试失败的 HTTP 请求

> 原文：<https://dev.to/kreuzerk/retry-failed-http-requests-in-angular-1o2h>

#### 立即重试、延迟重试、带退避重试

[![](img/0d6dcce8523ccfc62e97d0493f81aecd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--25BUGFy5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2600/1%2AdMn_iEu-GH54L18HjQW8qA.png)

从后端访问数据几乎是每个页面应用程序的支柱。所有动态内容都是从服务器加载的。

在大多数情况下，这些 HTTP 请求运行良好，并返回期望的结果。但是，有些情况下请求可能会出错。

想象一下，有人在以 200 公里/小时的速度穿越全国的火车上通过热点使用你的网站。🚅嗯，网络可能很慢，但 HTTP 请求仍然会返回期望的结果。

但是如果火车进入隧道呢？用户很有可能失去连接，您的 web 应用程序无法访问服务器。在这种情况下，一旦用户退出隧道并重新联机，他将被迫刷新您的应用程序。

刷新应用程序会对应用程序的当前状态产生影响。例如，用户可能会丢失他在表单中输入的数据。

与其让请求立即失败，不如重试几次，并向用户显示相应的消息。使用这种方法，可以防止刷新。

[了解更多信息...](https://blog.angularindepth.com/retry-failed-http-requests-in-angular-f5959d486294)