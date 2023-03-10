# 面向 Web 开发人员的 DNS 101

> 原文：<https://dev.to/joelvarty/dns-101-for-web-developers-5a4f>

作为 web 开发人员，DNS 是互联网最不为人所知的方面之一。这也是最重要的一点，因为它是连接你的域名(yourwebsite.com)和你的网站的粘合剂。

### DNS 代表域名服务

我们在这里讨论的是，浏览你的网址的人能够被引导到托管你的网站的服务器的 IP 地址。它将域名连接到 IP 地址。IP 地址是 0 到 255 之间的一系列 4 个数字，每个网络主机应该能够给你。

有几种不同的方法可以实现这一点。

### 《一》记录

这是最基本的记录类型。它说将这个地址(yoursite.com)指向这个 IP 地址(1.1.1.1)。

对于每个顶级域名，你应该有一个记录。这应该是用来连接您的顶级网站地址到您的虚拟主机 IP 地址。

yoursite.com·1.1.1.1

抵制诱惑，为你所有的子域名创建一个记录([www.yoursite.com](http://www.yoursite.com)，files.yoursite.com，files.yoursite.com)。这条路是疯狂的！为什么？如果你曾经移动网络主机，你不想改变所有这些记录！

我们可以用不同的方法。

### “CName”记录

CName 将一个网址([www.yoursite.com](http://www.yoursite.com))指向另一个网址(yoursite.com)。最常见的用法是将流量从您的子域名(例如:【www.yoursite.com】)发送到您的顶级域名(yoursite.com)。

www mysite.com。

许多工具使用@符号来表示您的顶级域名。

www @

在其他情况下，您需要将一个子域(例如:files.yoursite.com)指向由在线服务(如 Netlify、Cloudflare 或 Azure CDN)提供给您的另一个地址。

www xyz.netlify.com。

请注意，一些 DNS 提供商*要求*您添加点(。)后的地址，你是指其他，否则他们将创建一个子域名之间的链接(【www.yoursite.com】->)这不是你想要的。

### “TXT”记录

通常，TXT 记录用于验证您是否拥有一个域名。您将被要求把一个唯一的标识符，你的 TXT 记录是由服务提供的。

### 其他 DNS 记录类型

还有许多其他 DNS 记录类型(MX，SPF 等。)作为一名 web 开发人员，你可能不需要经常使用它们。如果有，请在评论中告诉我。

## 名称服务器

DNS 记录可以与您的实际 DNS 名称分开存放。它们由您的名称服务器托管。通常有 2 个，有时有 3 个，以提供冗余。当你设置使用一些主机服务时，他们会要求你指向他们的域名服务器。这样，他们可以帮助您对他们的服务进行通用设置。

如果您在一个地方设置了所有的 DNS 记录，但是在实际的 DNS 主机上指示指向名称服务器，这可能会变得令人困惑——所以一定要在某个地方记录下来。

## 诊断

这已经超出了本文的范围，但是您可以使用一些查看工具来检查特定域名的 DNS 设置。

使用 ping 检查记录(IP 地址)或 CName

```
ping www.yoursite.com 
```

Enter fullscreen mode Exit fullscreen mode

在线工具，如[dnschecker.org](https://dnschecker.org)也可以告诉你你的 DNS 设置已经传播到世界各地。

* * *

希望这对你有帮助！