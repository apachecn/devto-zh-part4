# 当前趋势中的 Squid 代理

> 原文：<https://dev.to/suntong/squid-proxy-in-current-trend-177k>

这开始了我的第二部分收集事实的博客 [squid 缓存服务器](http://www.squid-cache.org/)系列。同样，他们中的大多数将从网上收集并重新发布在这里，并贷记回原来的网址/作者。

在本系列的第一部分中，当谈到安装和配置时，省略了细节。原因是，实际上，像这样使用`squid`已经是过去的十年了！

现在的趋势是把一切都包在`docker`里。引自[此处](https://www.blaize.net/2017/02/docker-proxy-server-with-content-filter-built-on-squid-and-squidguard/):

> Docker 引发了平台即服务的革命。许多应用程序已经被移植到 Docker 容器来运行应用程序加载，甚至 Docker 正在取代一些传统上为基础设施保留的组件。现在可以在容器中运行 Web 应用程序防火墙，甚至是发送代理服务器。

是的，docker 引发了一场系统服务的革命，尤其是那些出于安全考虑而与外部连接的服务。鱿鱼完全属于这一类。然而，`squid`并不是唯一符合这种趋势的东西。这里还有更多，只是为了说明这种趋势无处不在:

*   [对 apt-cache-ng 服务进行注册](https://docs.docker.com/engine/examples/apt-cacher-ng/)
*   [使用 Docker 部署 DNS 服务器](http://www.damagehead.com/blog/2015/04/28/deploying-a-dns-server-using-docker/)
*   [Docker 代理和 DNS 容器](https://wp.sjkp.dk/docker-proxies-and-dns-containers/)
*   [使用 Squid 和 Docker-Compose 设置 Web 代理](https://hydrasky.com/linux/administration/setting-up-web-proxy-using-squid-and-docker-compose/)
*   [Docker 代理服务器，带有基于 Squid 和 Squidguard 的内容过滤器](https://www.blaize.net/2017/02/docker-proxy-server-with-content-filter-built-on-squid-and-squidguard/)
*   [用 Squid 和 Docker 在 Windows 10 上进行 HTTPS 滤波](https://docs.diladele.com/docker/docker_windows_10/index.html)
*   [Docker 反向代理](https://blog.confirm.ch/docker-reverse-proxy/)
*   [在代理后面使用 Docker](https://blog.codeship.com/using-docker-behind-a-proxy/)
*   最后，Kubernetes 集群上的 Docker-[在 Kubernetes 集群上构建并安装 Docker Squid 开放代理映像](http://www.pc-freak.net/blog/build-install-docker-squid-open-proxy-image-kubernetes-cluster/)

好吧，也许我没有列出太多种类，因为我毕竟主要是在搜索*鱿鱼码头*。关键是，已经有许多现成的 squid docker 图像，您可以直接抓取并使用。那么，为什么要为安装和配置细节而烦恼呢，:-)这里是我找到的一些 squid docker 映像的列表，没有特定的顺序:

*   Dockerfile 为 Squid 代理服务器创建 Docker 容器映像，[https://github.com/sameersbn/docker-squid](https://github.com/sameersbn/docker-squid)
*   Dockerized Squid 转发代理，[https://github.com/wernight/docker-squid](https://github.com/wernight/docker-squid)
*   鱿鱼码头工人图像，[https://github.com/jacobalberty/squid-docker](https://github.com/jacobalberty/squid-docker)
*   Squid 代理可配置容器，[https://hub.docker.com/r/datadog/squid](https://hub.docker.com/r/datadog/squid)
*   码头工人-鱿鱼-阿尔卑斯，[https://gitlab.com/andrewheberle/docker-squid-alpine](https://gitlab.com/andrewheberle/docker-squid-alpine)

仅举几个例子。好吧，已经有这么多可以选择的了，那选择哪一个呢？所以现在是配置细节的时候了，:-)。好的，*我个人会选择什么？* -

敬请期待...