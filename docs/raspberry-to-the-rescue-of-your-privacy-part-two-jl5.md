# 覆盆子拯救你的隐私-第二部分

> 原文：<https://dev.to/dieg0la/raspberry-to-the-rescue-of-your-privacy-part-two-jl5>

在第一部分中，我们设置了端口转发、SSH 服务器，并安装了包含所有安全内容的 web 服务器。

现在一切都准备好了，可以继续安装其他服务了

# π-孔

我在我的浏览器上使用了一些隐私扩展，例如[隐私獾](https://www.eff.org/privacybadger)、 [HTTPS Everywhere](https://www.eff.org/https-everywhere) 、[脸书容器](https://www.mozilla.org/en-US/firefox/facebookcontainer/)(这是一种很酷的将你的网站分开的方法，它在 Firefox 上是默认构建的)、纯 URL 以及最后的[子块](https://www.ublock.org/)。

我是最后一个的忠实粉丝，因为正如我们所知，我们访问的页面上有很多广告(是的，是的，我知道很多页面因为广告和点击而被保留)，但很多时候它们很烦人。

但是这些是我安装在我的浏览器电脑上的插件，我的本地网络设备呢？例如我的 smarphone，我父母、兄弟或客人的智能手机，我知道我可以安装并拥有这些应用程序，但我不会对每个来我家的客人都这样做。

为此，有一项服务被称为“互联网广告黑洞”*。*

这项服务将帮助我们屏蔽所有本地网络设备中的广告。

在官方网站上是一步一步安装的。

### 两台 web 服务器住在一起

正如我们之前提到的，我们使用 Apache 作为默认的 web 服务器，但是为了安装 Pi-hole，我们将安装另一个名为 Lighttpd 的 web 服务器。Pi-hole 有一个 web 界面仪表板来查看统计数据，更改设置和配置我们的 Pi-hole 的某些方面。

此服务不会公开，它只在我们的本地网络中提供服务，但是要查看 web 界面仪表板，我们需要更改 Lighttp 的默认端口，因为它将会与在端口 80 上提供服务的 Apache 发生问题。

当我们安装 Lighttp 时，服务会自动启动，所以我们要在安装 Pi-hole 之前停止 apache

`# systemctl stop apache`

一旦你安装了 Pi-hole，你需要在`/etc/lighttpd/lighttpd.conf`改变 Lighttp 端口，改变`server.port`行，放置你喜欢的端口

[![lighttpd](img/c963f5d7a61bddacfcaeb34bedad1ea2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ixb5PKLQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/qkRD1hO.png)

这样做之后，您需要重新启动服务，如果一切正常，您将能够在您指定的端口中看到您的 Pi-hole 仪表板。

在我的例子中，端口是 8080，我的 Raspberry 的本地 ip 是 192.168.1.110，所以您的仪表板应该是这样的。

[![dashboard](img/f54271d5b7d4ec21c385a998b5869e1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uXCE1NyK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cQbhXld.png)

### 在您的家庭路由器上设置服务

要正确使用此服务，需要如下所示更改我们路由器的 DNS 地址。

[![DNS](img/ee030cf4038cca942e90960e40f2cbd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GmFAF7rc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gJwcSpS.png)

你需要添加树莓的本地地址，因此，任何连接到你的网络的设备都将有 Pi-hole 的好处。

总结:

*   停止 Apache 服务。
*   安装 Pi-hole 和 Lighttpd。
*   更改 Lighttpd 端口。
*   在您的家庭路由器中将 Raspberry IP 地址设置为 DNS。

# 您的私有云

我不是那种偏执狂，但我认为拥有自己的东西和基础设施很酷。

在这一节中，我将展示我如何安装 [ownCloud](https://owncloud.org/) *一个文件共享服务器，将你自己数据的控制权和安全性放回你手中。*

我知道还有其他服务，比如 NextCloud，但这次我打算使用这项服务。

### 安装

安装非常简单，我们只需要下载一个压缩包，并将其解压缩到我们的 web 服务器文件夹中。

[手动安装](https://doc.owncloud.org/server/10.2/admin_manual/installation/)

我在启动 ownCloud 时遇到了一些问题，因为服务器需要一些没有安装的 php 模块，但安装它们非常容易，只需做`apt search <php-module>`来搜索你需要的模块。

安装完所有的软件包后，你自己的云就准备好了。

[![owncloud login](img/7b9bc8f250b4772f6d5a11369cba227b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GsEzP-iz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hJrP2UA.png)

*注意:我遵循 owncloud 文档的推荐配置，在[文档根](https://doc.owncloud.org/server/10.2/admin_manual/installation/manual_installation.html#install-owncloud)T3】之外设置 ownCloud*

这样做之后，我们已经能够上传所有我们想要的东西，甚至通过我们的手机使用 ownCloud 应用程序。

[![owcloud app](img/51bf9f15e89de41b800b8279f1a634a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ZJ0Kr0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/v47KyBy.jpg)

总结:

*   下载 ownCloud 的压缩包并解压
*   安装必要的 php 模块
*   配置 Apache
*   下载手机应用程序 _ 可选)

# 您自己的 VPN 服务

也许你会说在我的小 Rasp 上有很多服务在运行，但是看一下性能就知道了。

我在 Raspi 上得到的最后一样东西是我的 VPN 服务。我真的很喜欢这项服务，因为当我在外面并连接到一些公共 WiFi(显然是检查公共 WiFi 的登录要求)时，它是一个非常棒的工具。

同样，使用 [pivpn](http://www.pivpn.io/) 很容易安装。

注:我知道这种安装方法是自动的，一切都很容易和快速。有一次我手动设置了一个 VPN，对我来说很难，配置安全，密钥等，但最终这东西工作，你学到了很多东西。这种 pivpn 方法简化了许多事情和步骤。

下一步将在我们的家庭路由器中打开一个新端口，以便在外部进行访问，我们需要打开您在安装中指定的端口。

安装 pivpn 后，您需要添加客户端并生成私钥和 ovpn 文件，它们将用于连接到 vpn。现在，我们可以使用电脑中的 OpenVPN 应用程序或手机中的应用程序连接到我们的 VPN，当我们在公共场所连接时，如果我们想保护隐私，这将非常有用。

[![vpnapp](img/ff72ca47d898c36702439e9ed5d5fc10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lwd6nM4F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hN2vIo8.jpg)

总结:

*   遵循 pivpn 的指南
*   打开我们家用路由器的端口
*   生成密钥和配置文件
*   将它们复制到我们的手机或电脑上
*   使用 openvpn 应用程序连接到我们的 vpn

# 失败 2 班

在这一点上，你将有一个设备是暴露在互联网上，也许在一天内，如果不是更少，你会看到很多登录尝试在您的安全日志，这是其他原因，我已经暴露了 Raspi，以了解更多关于安全，阅读日志，配置的东西，等等。

我安装了一个服务调用 fail2ban，用于减轻用户和机器人的暴力攻击。

[fail2ban](https://linuxhandbook.com/fail2ban-basic/)

# 最后的想法

我放在 Rasp 上的第一个服务是一个中间 Tor 中继，我取消了它，因为我没有看到它有很多流量，但使用 Tor 和 raspberry 也是一个很好的选择。另外，我想把 rasp 作为一个蜜罐来继续学习更多的安全知识。

正如我在开头所说的，我敢于写这个，首先是因为我相信分享知识是很棒的，我很喜欢自由软件哲学，我认为我正在运行的一套服务，它们对我很有用，也许对你也有用。

非常感谢阅读，欢迎任何建设性的批评。