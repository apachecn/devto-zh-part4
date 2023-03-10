# Nmap -简介(第 1 部分)

> 原文：<https://dev.to/kavishgour/nmap-introduction-part-1-48dn>

在这篇文章中，我将解释什么是 Nmap，以及一个基本的演示。我正在开展我的网络安全职业生涯，我希望这个系列能够让 InfoSec 爱好者受益。玩得开心。

#### 注意:如果你不知道如何安装 Nmap，点击[这里](https://nmap.org/book/install.html)。

### 简介

Nmap 或网络映射器是一种网络扫描工具，旨在审核一系列主机(大型网络)或单个主机(计算机)。Nmap 向目标主机发送 TCP、UDP、SCTP 和 ICMP 数据包，并通过将结果与其数据库(始终使用最新版本)进行比较来检查响应，以确定正在运行的服务、安装的操作系统版本、防火墙类型以及其他有用的东西。

Nmap 是一个非常强大、成熟的网络扫描器，它将帮助您看到您需要在网络上看到的一切。为了让你兴奋，这里有一个类比:你即将开始一次公路旅行，所以基本上你需要某种地图或应用程序来规划你的路线，并了解你周围的情况，如路况等。嗯，把 Nmap 想象成你的网络地图。

#### Nmap 语法

语法非常简单，但是提供的选项数量惊人。始终以特权用户或 root 用户身份运行 nmap(在 windows 上:以管理员身份打开命令提示符)。

语法:nmap[扫描类型][[选项]{目标规格}

在这篇文章中，为了简单起见，我只使用 nmap 和一个目标。目标可以是主机名、IP、网络范围等。

<u>免责声明</u>:在您自己的网络上使用 Nmap。当在没有事先授权的情况下在网络上使用时，并且您的意图只是执行扫描，您将看起来像是恶意类型。

#### 我们开始吧

让我们运行“nmap localhost”:

```
MacBook-Pro:~ kavish$ nmap localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2019-08-28 11:52 +04
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00036s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 993 closed ports
PORT    STATE SERVICE
88/tcp  open  kerberos-sec
110/tcp open  pop3
143/tcp open  imap
445/tcp open  microsoft-ds
548/tcp open  afp
993/tcp open  imaps
995/tcp open  pop3s

Nmap done: 1 IP address (1 host up) scanned in 6.83 seconds 
```

Enter fullscreen mode Exit fullscreen mode

扫描大约需要 6 秒钟(取决于您如何运行服务)。

你的输出会不一样。这里的关键是，Nmap 可以向您显示网络上您不知道的东西。或者您可能不知道您的机器上正在运行什么服务。

从上面的输出中，我可以看到 kerberos、afp 和 microsoft-ds 正在运行。我没有启动这些服务，我也不知道是什么启动了它们。

我在谷歌上快速搜索了一下，发现这三个服务负责文件共享。我禁用了文件共享，瞧:

```
MacBook-Pro:~ kavish$ nmap localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2019-08-28 11:54 +04
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00038s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 995 closed ports
PORT    STATE SERVICE
110/tcp open  pop3
143/tcp open  imap
631/tcp open  ipp
993/tcp open  imaps
995/tcp open  pop3s

Nmap done: 1 IP address (1 host up) scanned in 7.22 seconds 
```

Enter fullscreen mode Exit fullscreen mode

像这样的简单扫描无法获取足够的信息。但这足以让你熟悉 Nmap。

请继续关注第 2 部分，在那里我将解释如何运行更高级的命令，以及如何使用 [Python-Nmap](https://pypi.org/project/python-nmap/) 处理您的结果。