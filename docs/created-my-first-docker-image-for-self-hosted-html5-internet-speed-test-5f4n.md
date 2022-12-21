# 创造了我的第一个码头工人形象！对于自托管 HTML5 互联网速度测试

> 原文：<https://dev.to/openspeedtest/created-my-first-docker-image-for-self-hosted-html5-internet-speed-test-5f4n>

这个 docker 映像包含在[http://openspeedtest.com](http://openspeedtest.com)运行的相同应用程序。这个可以离线运行！所以你可以用它来测试你的局域网或者把它放在一个服务器上来测试你到那个服务器的线路速度。目前经过优化，测试速度高达 1~2 Gbps。您可以更改这个 docker 中的值和文件，甚至可以测试更快的连接。

默认情况下将使用 Index.html。(可以在没有互联网连接的情况下运行)

如果你需要，你可以使用 hosted.html，它只能通过互联网连接运行，并将结果保存到我们的数据库中。

你可以在这里注册查看结果[http://openspeedtest.com/admin](http://openspeedtest.com/admin)

下载 HTML5 互联网速度测试应用程序(家庭/办公室局域网速度测试！【离线运行！])

安装 Docker 并运行以下命令！

1)docker run-restart = until-stopped-name = openspeedtest-d-p 80:8080 openspeedtest/latest

现在访问 [http://localhost/](http://localhost/) 或 [http://YOUR-IP/](http://YOUR-IP/)

测试您的 WiFi 速度，您可以决定您需要放置路由器的位置！或者你需要在哪个方向加一个中继器！