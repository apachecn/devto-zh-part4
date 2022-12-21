# OpenSpeedtest.com 进行的 1Gbps 互联网速度测试

> 原文：<https://dev.to/openspeedtest/1gbps-internet-speedtest-by-openspeedtest-com-19hf>

安装 Docker 并运行以下命令！

1)docker run-restart = until-stopped-name = openspeedtest-d-p 80:8080 openspeedtest/latest

现在访问 [http://localhost/](http://localhost/) 或 [http://YOUR-IP/](http://YOUR-IP/)

这个可以离线运行！所以你可以用它来测试你的局域网或者把它放在一个服务器上来测试你到那个服务器的线路速度。目前经过优化，测试速度高达 1~2 Gbps。您可以更改这个 docker 中的值和文件，甚至可以测试更快的连接。