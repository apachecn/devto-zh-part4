# 使用 Dnsmasq 为 Linux 上的项目设置一个本地 DNS 服务器

> 原文：<https://dev.to/brpaz/setup-a-local-dns-server-for-your-projects-on-linux-with-dnsmasq-blm>

在本文中，我将解释如何在 Linux 上设置一个本地 DNS 服务器。

我将使用 Dnsmasq，一个预装在 Ubuntu 或 Fedora 等操作系统中的轻量级 DNS 服务器。

本文中的所有说明都基于 Fedora 30 安装。对于任何其他基于*Nix 的操作系统，这些概念都是相同的，但安装命令或某些文件的位置可能略有不同。

## 为什么要设置自己的 DNS 服务器？

有很多原因可以让你设置你的本地 DNS 服务器
比如缓存，但是对我来说，最有用的原因之一是自动解析本地开发域到你的本地主机。

对于简单的项目，使用“localhost”和“port”可能就足够了，但是对于需要同时运行多个应用程序的更复杂的项目(例如:面向微服务的项目)，记住所有的端口并避免冲突将变得更加复杂，因此基于域的方法更容易。

还有一些情况下，您的业务逻辑依赖于应用程序的域。

一个应用程序，其中每个用户通过一个子域登录，或者当每个国家有不同的域时，内容基于该域加载。

在这些情况下，在您的本地环境中有一个“真正的”域来模拟生产中的预期行为是非常重要的。

一种常见的方法是将您的域添加到系统的`hosts`文件中。
虽然这样做很好，但它的可扩展性不是很好，因为你需要手动将每个新域添加到文件中，而且它不支持通配符子域。

本地 DNS 服务器可以进行一次性设置。

## 安装和配置 Dnsmasq

Dnsmasq 预装在 Ubuntu 和 Fedora 中。在 Ubuntu 中并不是真正的`dnsmasq`而是`dnsmasq-base`，一个与系统网络管理器紧密集成的 DNSmasq 的精简版本。这对我们的需要来说足够了。

对于其他操作系统，您可能需要查看相应的操作系统文档，但是如果尚未安装，您应该能够使用操作系统默认的软件包管理器来安装它。

安装 Dnsmasq 后，您需要启用“网络管理器”以将其用作默认 DNS 服务器。

新建一个文件`/etc/NetworkManager/conf.d/dnsmasq.conf`并添加以下内容:

```
[main]
dns=dnsmasq 
```

Enter fullscreen mode Exit fullscreen mode

重新启动“网络管理器”服务以保存您的更改。

```
sudo systemctl restart NetworkManager 
```

Enter fullscreen mode Exit fullscreen mode

现在检查`/etc/resolv.conf`并检查是否有名称服务器`127.0.0.1`的条目。

## 将您的本地域添加到 Dnsmasq

下一步是为您的域配置 DNS 区域。

您可以指定任意多的域，但就个人而言，我对所有本地域都使用“lh”(本地主机)TLD。

转到`/etc/NetworkManager/dnsmasq.d`，用你的 TLD 的名字创建一个新文件，在我的例子中是`lh.conf`，内容如下:

```
address=/lh/127.0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您已经安装了完整的 Dnsmasq，并且没有使用 NetworkManager 附带的版本，那么应该将配置添加到`/etc/dnsmasq.d/`中。

这指示 Dnsmasq 将对`lh` TLD 的任何查询解析到您的本地机器。
你不必总是指向`127.0.0.1`强硬。例如，如果你正在使用虚拟机，你不能指向你的虚拟机 IP 地址。

这种用例的一个很好的例子是使用 [minikube](https://github.com/kubernetes/minikube) 时。

我在 Dnsmasq 中创建了一个`mkube`条目来解析 Minikube 虚拟机的 IP 地址，因此我可以创建一个`app1.mkube`、`app2.mkube`，它们将自动到达 Minikube。

最后，您需要重启服务来重新加载您的更改:

```
sudo systemctl restart dnsmasq NetworkManager 
```

Enter fullscreen mode Exit fullscreen mode

要检查一切是否正常，请打开您的终端并键入`dig test.lh`。

您应该看到它解析为`127.0.0.1`。您可以测试任何其他子域，它们都应该解析到您的机器。

瞧，你已经设置了一个非常简单的 DNS 服务器。无需再担心端口或主机文件。任何*。lh 请求将解析到您的机器。

## 下一步

接下来的步骤是在您的本地主机上设置一个 HTTP 服务器来处理您的请求，并根据域名代理到正确的应用程序。

如果你在用 Docker，我推荐试试 [Traefik](https://traefik.io/) 。

在监听端口 80 的机器上安装 Traefik。Traefik 将侦听 Docker 事件，并在容器状态改变时自动重新加载其配置。

您可以为应用程序设置一个主机名，Traefik 将进行必要的映射，因此，例如，`my-app.docker.lh`将被自动代理到容器的正确内部端口。

您可能需要在容器上设置一些标签，仅此而已。Traefik 和 Dnsmasq 会处理剩下的。

这种设置超出了本文的范围。我可能会写另一篇关于这个的文章。

## Ubuntu 用户注意

如果你使用的是 Ubuntu，请检查[这个](https://askubuntu.com/questions/1029882/how-can-i-set-up-local-wildcard-127-0-0-1-domain-resolution-on-18-04?rq=1)关于堆栈溢出的问题。

## 结论

对于较小的或单一的应用程序项目，基于端口的方法非常好，没有理由不坚持使用它。

但是，如果您同时处理许多项目，具有复杂微服务架构的项目，或者当您的应用程序具有基于域或子域的逻辑时，Dnsmasq 提供了一种非常简单的方法来管理您的域，而不必手动处理`hosts`文件。

## 引用

*   [使用网络管理器的 DNSMasq 插件- Fedora Magazine](https://fedoramagazine.org/using-the-networkmanagers-dnsmasq-plugin/)