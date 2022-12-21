# 将 Dockerized Flask 应用程序从 Python 2.7 升级到 Python 3.7+

> 原文：<https://dev.to/nickjj/upgrading-a-dockerized-flask-app-from-python-2-7-to-python-3-7-13l>

* *本文原载于 2019 年 8 月 27 日:[https://nickjanetakis . com/blog/upgrading-a-dockerized-flask-app-from-python-2-7-to-python-3-7](https://nickjanetakis.com/blog/upgrading-a-dockerized-flask-app-from-python-2-7-to-python-3-7)T3】*

* * *

2020 年 1 月 1 日即将到来，那时 Python 2.7.x 将最终被视为寿终正寝，所以如果您还没有升级到 Python 3.7+的话，现在是开始升级的好时机。

在下面列出的视频中，我们将一个真实的 Flask 应用程序从使用 Python 2.7.16 更新到 3.7.4。它涵盖了如何使用 Docker 实现这一点，但是同样的策略也可以在没有 Docker 的情况下使用。

##### 接通我们覆盖的视频:

*   选择要升级到的 Python 版本
*   查看哪些第三方包版本仅适用于特定版本的 Python
*   如何使用 Docker 以生产就绪(安全)的方式执行升级
*   在升级过程中，您会遇到并修复一系列错误
*   如何编写应用程序以同时支持两个版本，直到您淘汰 Python 2.7.x

我们在视频中介绍的演示应用是我的[用 Flask 课程](https://buildasaasappwithflask.com/?utm_source=nj&utm_medium=devto&utm_campaign=upgrading-flask-app)构建 SAAS 应用中的应用。它最初被编码为与 Python 2.7.x 和 Python 3.x 的早期版本一起工作。随着时间的推移，我已经对它进行了更新，以使它保持最新，并将继续更新它(yay 为终身免费更新)。

### 将 Python 从 2.7+更新到 3.7+视频

[https://www.youtube.com/embed/Kq_khHWovl4](https://www.youtube.com/embed/Kq_khHWovl4)

#### 带时间戳的目录

*   0:33 -为什么要费心从 Python 2.7 更新到 3.7+？
*   0:48 -以生产就绪的方式升级
*   1:29 -这段视频将涵盖哪些内容？
*   2:34 -下载样本 Flask 项目进行升级
*   3:29 -使用 Docker 构建项目，以便我们可以运行它
*   4:46 -计算 Python 2.7 的最新 Docker 映像
*   6:20 -观看 Docker 构建 Python 2.7.16
*   6:49 -降级 pytest 库以使用 Python 2.7
*   9:33 -在我们修复 pytest 之后，看着 Docker 再次构建
*   10:08 -运行示例使用 Flask 项目构建 SAAS 应用程序
*   10:56 -确保我们的测试套件通过 Python 2.7
*   12:04 -使用 Docker 升级 Python 版本的好处
*   13:04 -如何将 Dockerized Flask 应用程序从 Python 2.7 升级到 3.7+
*   14:26 -观看 Docker 升级到 Python 3.7.4 后再次构建
*   14:52 -遇到并修复芹菜节拍时间表错误
*   15:43 -升级后遇到并修复 JSON 可序列化类型错误
*   19:53 -使用 Docker 的另一个好处是回滚到 2.7 很容易
*   21:33 -查看 Python 3.7 中更多与 Flask 相关的包
*   25:54 -使用 Flask-Limiter 对特定 URL 端点进行速率限制
*   26:55 -滚动浏览主 Flask 项目中使用的所有包
*   27:19 -编写您的应用程序，使其同时适用于 Python 2.7.x 和 Python 3.7.x

#### 参考链接

*   [https://github.com/nickjj/build-a-saas-app-with-flask](https://github.com/nickjj/build-a-saas-app-with-flask)
*   [https://buildasaasappwithflask.com](https://buildasaasappwithflask.com/?utm_source=nj&utm_medium=devto&utm_campaign=upgrading-flask-app)
*   [https://nickjanetakis.com/blog/](https://nickjanetakis.com/blog/)
*   [https://hub . docker . com/_/python](https://hub.docker.com/_/python)
*   [https://pypi.org/project/pytest](https://pypi.org/project/pytest)
*   [https://github.com/nickjj/dotfiles](https://github.com/nickjj/dotfiles)

为了将 Python 升级到 3.7+，你必须做些什么？下面让我知道。