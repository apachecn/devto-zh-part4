# Slowloris(计算机安全)无聊的服务器死亡-缓慢的洛里斯攻击

> 原文：<https://dev.to/kalkwst/boring-a-server-to-death-the-slow-loris-attack-2do>

> 封面图片是一只真实生活中的懒猴。虽然它是一只超级可爱的动物，但请不要把它当成宠物来买

Slow loris 是一种拒绝服务攻击，它可以破坏不受保护的基于线程的 web 服务器，如 Apache，它是由一个叫 RSnake 的人在 2009 年创建的(也是我最喜欢的 DoS 攻击之一😆)

为了理解这样的攻击是如何工作的，让我们刷新一些基础知识([太无聊了，现在给我看看](#how-the-slow-loris-attack-works))

### DoS 和 DDoS 攻击

`denial-of-service`或`DoS`攻击是指攻击者试图通过暂时或无限期地中断主机服务来使机器或网络对其目标用户不可用的攻击。这种攻击背后的想法是，通过发送尽可能多的请求，您试图使这种攻击的业务端的机器崩溃。因为机器的资源是有限的，如果你发送的请求超过了它的处理能力，服务器就会关闭。

一次`distributed denial-of-service`或`DDoS`攻击，就是下一关。这里不是一台计算机，而是利用一堆计算机同时向另一端的机器发送尽可能多的请求。一些现代攻击使用诸如`DNS amplification`之类的技术，以便最大限度地扩大攻击的影响。

但是攻击背后的主要思想是一样的。同时处理尽可能多的请求。如果你设法发送足够的信息，机器就会停止工作。如果你不这样做，它只会当面嘲笑你。

这种攻击很难防范，尤其是对于基础设施有限的小型组织。由于这种攻击使用合法的请求，传统的防火墙过滤规则不能很好地发挥作用。

### 服务器-客户端用 HTTP/1.1 通信

现在让我们快速回顾一下 HTTP/1.1 GET 请求是如何工作的。

假设我们要连接到`dev.to`。我们将向网站发送一个`GET`请求，我们说“嘿，我想要`index.html`”。服务器会把我们送回`index.html`。然后我们会发送一个新的`GET`请求，我们会说“嘿，我现在就需要`logo.png`”。服务器会把我们要求的文件发给我们。这种对话将会反复进行，直到我们加载完页面及其所有资产。

一个示例 HTTP/1.1 GET 请求头应该是这样的:

```
HTTP/1.1
Host: dev.to
Accept-Language: en
(empty line) 
```

Enter fullscreen mode Exit fullscreen mode

注意最后一个空行。这标志着头的结束，允许服务器在收到这个空行时处理请求。这将是非常重要的一段时间。

## 懒猴的攻击方式

懒猴攻击的想法很简单。我能不能发送请求太慢，让他们烦死了？答案是，是的，你可以！

懒猴是 2009 年 RSnake 发明的一种*慢速低*攻击。它不是尽可能快地发送请求，而是尽可能慢地发送请求。攻击者将 HTTP GET 请求分成尽可能多的数据包，并尽可能缓慢地发送它们。

现在 Jimmy 你可能会说，“好的，我知道服务器对每个请求都有一个内置的超时。这东西肯定行不通！”。嗯，这是真的。服务器对每个请求都有内置的超时时间。假设我们正在用手机浏览一个网站，由于某种原因我们失去了互联网连接。服务器会等待一段时间，然后释放连接。这将释放该连接的资源，以便为其他人服务。

问题不在于我们不发送数据，而在于我们发送了一些数据，但是非常慢。这就是懒猴的工作方式。它向主机发送请求，但不完成请求，然后等待。当服务器由于超时即将关闭连接时，它发送它需要的文件的第一个字符。这就像说“嘿，我还在这里，但是我的网络连接很差！”。然后它一次又一次地这样做，并尽可能长时间地保持这种连接。

然后对它能打开的尽可能多的连接进行同样的处理，本质上是用极其缓慢的`HTTP GET`请求霸占所有到服务器的可用连接。

例如，这种攻击很难被防火墙检测到，因为所有这些都是有效的 HTTP 请求，但是它们非常慢。用户可能有一个非常糟糕的互联网连接。

## 为什么基于线程的 web 服务器如此容易受到这种攻击？

当用于 Apache 服务器或任何其他基于线程的 web 服务器(如 Microsoft IIS)时，这种攻击非常有效。原因是在 Apache 服务器上工作的人们决定，当一个新的连接到来时，它将由一个新的线程来服务。这是一个不错的方法。如果连接按预期来来去去，线程将服务于连接，然后离开。但是，如果连接开始停留的时间比预期的长，将会达到服务器的连接限制。其他服务器也是如此。

现在，假设我们有一个相对较小的网站，服务器允许多达 200 个并发连接，因为超过这个限制，会有太多的线程，整个事情会陷入停顿。该攻击将获取所有可用的连接，并且当一个新的连接被释放时，该攻击也将获取该连接。最后，它将结束所有可用的服务器连接，使网站无法访问。web 服务器会认为有 200 个人在浏览网站，而实际上只有一个人在缓慢地浏览网站 200 次。

## 减缓洛里斯的攻击

有几种技术可以防止和缓解慢速洛里斯攻击。有些涉及使用非基于线程的服务器作为代理。这些服务器不会受到这种攻击。

### 使用 mod_reqtimeout

可以将 web 服务器配置为允许客户端在设定的时间内开始发送报头数据，并设置最小传输速率。下面的示例配置就是这样做的

```
<IfModule mod_reqtimeout.c>
  RequestReadTimeout header=20-40,MinRate=500 body=20-40,MinRate=500
</IfModule> 
```

Enter fullscreen mode Exit fullscreen mode

它给客户端最多 20 秒的时间开始发送报头数据。然后，客户端必须以每秒 500 字节的最小速率发送数据，并且最多可以发送 40 秒。

### 使用 mod_qos

也可以将服务器配置为跟踪连接的 IP，并设置一个 IP 可以拥有的连接数限制:

```
<IfModule mod_qos.c>
  # handle connections from up to 100000 different IPs
  QS_ClientEntries 100000
  # allow only 50 connections per IP
  QS_SrvMaxConnPerIP 50
  # limit the maximum number of active TCP connections to 256
  MaxClients 256
  # disables keep-alive when 180 (70%) TCP connections are occupied
  QS_SrvMaxConnClose 180
  # minimum request/response speed
  # (deny clients that keep connections open without requesting anything)
  QS_SrvMinDataRate 150 1200
</IfModule> 
```

Enter fullscreen mode Exit fullscreen mode

你有什么要补充的吗？在下面留下评论，感谢阅读！