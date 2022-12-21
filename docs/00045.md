# 基于 Windows 的 Drupal 开发

> 原文：<https://dev.to/arturolinares/drupal-development-on-windows-2m1b>

最近我写了一篇关于我如何用 PC 取代 MacBook Pro，并且在几个 Drupal 环境下开发时仍然高效的文章。而 [Pop_OS！](https://system76.com/pop)是一个很棒的 Linux 发行版，我决定试试 Windows。主要原因还是老一套:硬件。

这些驱动程序经过优化，可以在 Windows 上工作，一些很好的小功能只能在这个有争议的操作系统上工作:用红外摄像头登录( *Windows Hello* )，有几个显示器的稳定工作空间(也许这是最重要的原因)，扬声器发出更好的杜比声音，以及电池优化。其实没什么重要的，但能改善体验的东西。

## 拥有一个富有成效的开发环境意味着什么？

这个:

1.  Bash 命令行。
2.  快速 docker 环境(没有 unison，我已经有足够多的问题了)。
3.  工作 git
4.  能够从命令行快速启动多个开发环境，并使用自定义域名来访问它们及其相关服务。
5.  一个好的编辑器，比如 Sublime Text 或者 Visual Studio 代码。(我知道，我不用 PHP Storm 也不想用)。

因为我以前尝试过 WSL(但失败了)，所以我决定去掉它，直接安装一个 VM。

Ubuntu 服务器会检查上面列表中的前三项，但接下来的两项可能会变得混乱。将源代码文件作为 Samba 共享与 Windows 主机共享违背了初衷，因为它的性能很差，符号链接很奇怪，等等。使用`sshfs`共享它可能是一种选择，但是我如何控制 docker 呢？

## 编辑

解决方案来自 Visual Studio 代码的最近一次迭代:**远程代码扩展**，具体来说，是通过 SSH 的**远程编码**。这个扩展允许我通过 SSH 打开远程目录并使用它们。这不同于在服务器上运行 Dreamweaver 来编辑文件(还记得吗？).**它将编辑器分成两部分，一个客户端(UI)和一个服务器**，在那里运行索引和语言处理扩展。结果是一个反应非常灵敏的编辑器，同时用我通常使用的所有扩展来远程操作*。甚至编辑器中的集成终端也会在服务器中自动打开 bash 提示符，这是一个非常有用的特性。*

Visual Studio 代码中的这个远程特性让我可以直接从编辑器中获得完整的 Linux 体验。

然而，我通常使用自定义网址访问 docker 网站，这是一种很常见的做法。如果使用 [docksal](http://docksal.io) ，例如:`super-awesome-client.docksal`、`db.super-awesome-client.docksal`等。，你明白了。我需要找到一种方法将自定义 URL 重新路由到我的虚拟机。

## 联网

在 Mac 或 Linux 上，安装 docksal(或 lando 或任何您使用的 docker 包装器)后，这将开箱即用。我想让它以同样的方式工作:

*   我不想手动配置每个自定义网站
*   这必须在用 docspal 创建的新网站上运行
*   应该可以在任何网络上工作

为了实现这种透明路由，我需要一个静态 IP 用于虚拟机，这样无论我在家里、咖啡店还是机场，我都可以引用它。我使用的是 Hyper-v，所以我按照下面的说明创建了一个 NAT:

[Windows 10:如何为 Hyper-V 来宾设置 NAT 网络？](https://anandthearchitect.com/2018/01/06/windows-10-how-to-setup-nat-network-for-hyper-v-guests/)

然后在 VM 中，**使用`netplan`配置了静态 IP** 地址，创建了文件`/etc/netplan/01-netcfg.yaml`(我使用了与上面创建 NAT 的向导相同的 IP 地址)。

```
network:
    ethernets:
        eth0:
            dhcp4: no
            dhcp6: no
            addresses: [192.168.200.11/24]
            gateway4: 192.168.200.1
            nameservers:
                addresses: [8.8.8.8] 
```

在虚拟机中启用 ssh 服务器**并配置我的 ssh 密钥后，我能够非常快速地从 Windows 跳转到服务器，甚至配置 [ConEmu](https://conemu.github.io/) 或新的 [Windows 终端](https://github.com/microsoft/terminal)自动连接到 ssh。**

为了访问用 docksal 创建的 docker 容器中运行的 web 服务器，**我安装了一个 DNS 代理** : [丙烯酸 DNS 代理](http://mayakron.altervista.org/wikibase/show.php?id=AcrylicHome)，并使用了这个`hosts`配置:

```
192.168.200.11 *.docksal 
```

## Visual Studio 代码配置

在 Visual Studio 代码中，我为 SSH 安装了[远程开发扩展。配置非常简单，而且运行得非常好(扩展会引导您，直到您成功地连接到 SSH 服务器)。](https://code.visualstudio.com/docs/remote/ssh)

大型 Drupal 项目的索引速度非常快，浏览它们时的性能就像在 Linux 上工作一样。调试是开箱即用的(这要感谢 Docksal 配置，但是使用`docker-compose`应该不成问题)。

我发现的唯一缺点是我不能使用编辑器提交、推送或拉取，这没什么大不了的，因为我可以使用集成的终端。来自[文档](https://code.visualstudio.com/docs/remote/ssh#_known-limitations):

> 如果您使用 SSH 克隆 Git 存储库，并且您的 SSH 密钥有一个密码短语，那么 VS 代码的 pull 和 sync 特性在远程运行时可能会挂起。使用不带密码的 SSH 密钥，使用 HTTPS 克隆，或者从命令行运行 git push 来解决此问题。

我最终在没有密码的情况下为我的回购创建了一个 SSH 密钥。

## 重述

在 Windows 上本地使用 PHP 仍然是不可能的，但使用 VM 和带有远程代码扩展的 Visual Studio 代码效果非常好，代价是更多的内存使用，但内存现在很便宜，这也是我当初离开苹果的原因之一:拥有一台可升级的开发计算机。

一个意想不到的好处是，我可以从挂起中恢复，虚拟机将加载所有正在运行的服务，这在 Linux 中是不会发生的；笔记本电脑醒来后，我不得不再次运行`fin up`或`docker-compose up`。

## 先不说:WSL2

用于 Linux v2 的 Windows 子系统是*即将发布的*，它在一个非常轻量级的 VM 中使用了一个定制的 Linux 内核(它已经在 Windows Internals fast ring 中，但预计要到 2020 年才能达到公开发布)。

从理论上来说，它具有这种虚拟机设置的所有优点，并且似乎是一种用于未来开发环境的很好的设置，原因如下:

*   可以使用 p9 文件系统直接从 Windows 访问 VM 文件系统(仅此一点就很棒)。
*   表现非常接近原生。
*   共享内存和处理器内核。
*   更紧密的集成，可以在虚拟机中运行 windows 程序。

这项技术可以真正改变 Windows 上的开发体验，让我们看看它发布时是否达到预期(因为我不会在我的主要开发计算机上订阅 fast ring)。

## 久违的事物

在设置环境[时，我发现了一个错误](https://dev.to/arturolinares/git-errors-when-using-hyper-v-1ca9-temp-slug-583485)，它不允许我克隆大的回购或推送更改。原来是我 WiFi 驱动里的 bug。因此，如果您发现以下错误，并且有英特尔 WiFi 卡，请更新您的驱动程序。

```
$ git clone git@somrerepo-bla-bla.git
Cloning into 'somedir'...
error: RPC failed; curl 56 GnuTLS recv error (-12): A TLS fatal alert has been received.
fatal: The remote end hung up unexpectedly 
```