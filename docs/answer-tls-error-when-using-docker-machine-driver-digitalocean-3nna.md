# 答:使用 docker-machine 驱动程序 digitalocean 时出现 TLS 错误

> 原文：<https://dev.to/espoir/answer-tls-error-when-using-docker-machine-driver-digitalocean-3nna>

我只是在尝试他那令人敬畏的功能是如何工作的！

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 回答回复:使用 docker-machine 驱动时出现 TLS 错误数字海洋](https://stackoverflow.com/questions/36975960/tls-error-when-using-docker-machine-driver-digitalocean/57745455#57745455)

Sep 1 '19[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/36975960/tls-error-when-using-docker-machine-driver-digitalocean/57745455#57745455) </header>

看起来这可能与你的网络有关，

docker-remote 机器已创建，但您无法通过 TCP 协议连接到它。

*   尝试通过 ssh `docker-machine -D ssh docker-machine-name`连接到它，以确认假设。

如果你能 ssh，这意味着它是一个网络问题。

改变…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/36975960/tls-error-when-using-docker-machine-driver-digitalocean/57745455#57745455)</button>