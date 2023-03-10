# DNS 记录解释

> 原文：<https://dev.to/andeshchogle/dns-records-explained-2h8a>

*交叉发布自:[https://www . andeshchogle . com/blog/post/DNS-records-explained/](https://www.andeshchogle.com/blog/post/dns-records-explained/)T3】*

#### 什么是 DNS

当您在浏览器的地址栏中输入一个网站 URL 时，您会看到来自该网站的页面。你有没有想过那些书页是从哪里来的？它们来自互联网上某个地方的服务器。当你在浏览器的地址栏中输入一个链接时，你实际上是在请求服务器给你发送这些页面。

互联网上运行着数百万台这样的服务器。那么，您的浏览器如何知道将请求发送到哪个服务器呢？这就是 DNS 发挥作用的地方。DNS 代表 **D** 域名 **N** 名称 **S** 系统。

#### 域名系统

互联网上的每台服务器都有一个特定的数字串，称为服务器的 IP 地址。但是这些 IP 地址只是看似随机的字符串或数字，很难记忆。因此，有一个系统可以将这些 IP 地址映射成容易记忆的名称。这样的名字叫做*域名*(如【www.google.com】)。这些映射存储在被称为*域名服务器*的特殊服务器上。

因此，每当你请求一个网站使用它的域名时，你的浏览器就会联系域名服务器并获取映射到该域名的 IP 地址。然后，它将网页请求发送到该 IP 地址。

#### DNS 记录

我们上面看到的域名和 IP 地址之间的映射称为 **A 记录(或地址记录)**

有许多种映射不仅仅是在域名和 ip 地址之间建立关系。我们将在下面几节中探讨它们

#### 1。记录(地址记录)

正如我们上面看到的，A 记录是域名和 IP 地址之间的映射；

举个例子，

| 域名 | IP 地址；网络地址 |
| --- | --- |
| [www.google.com](http://www.google.com) | 216.58.203.164 |
| [www.gmail.com](http://www.gmail.com) | 172.217.163.37 |
| [www.facebook.com](http://www.facebook.com) | 157.240.24.35 |

#### 2。AAAA 记录(IPv6 地址记录)

IP 地址有两种类型:IPv4 和 IPv6。IPv4 是服务器地址的 32 位(4 字节)表示。但是你可以想象，仅仅 4 个字节代表的数字不足以寻址互联网上的所有服务器——这个数字还在不断增加。IPv6 通过用 128 位(16 字节)表示地址解决了这个问题。

AAAA 记录是域名与其 IPv6 地址之间的映射。

#### 3。CNAME 记录(规范名称记录)

CNAME 记录代表两个域名之间的映射。这就是说，一个域名只是另一个域名的别名(规范名称)。因此，当域名服务器收到将域名解析为 IP 地址的请求时，如果该域名是映射到另一个域名的 CNAME 记录，DNS 系统将从第二个规范域继续 IP 解析流程。

| 域名 | 留档活字 | 绘图 |
| --- | --- | --- |
| bar.example.com | CNAME | foo.example.com |
| foo.example.com | A | 216.58.203.164 |

现在，当您请求 bar.example.com 的 IP 地址(即 A 记录)时，DNS 系统会看到有一个值为 foo.example.com 的 CNAME 记录与之相关联。所以它继续搜索来自 foo.example.com 的 IP 地址，并返回 216.58.203.164

#### 4。MX 记录(邮件交换记录)

MX 记录指定哪些邮件服务器将接受/处理发送给该域名用户的电子邮件

例如(为便于理解而简化)，

| 域名 | 留档活字 | 绘图 |
| --- | --- | --- |
| example.com | 麦克斯韦(maxwellˌ磁通量单位)ˌ中性(Middlesex) | server1.mymailservers.com |
| example.com | 麦克斯韦(maxwellˌ磁通量单位)ˌ中性(Middlesex) | server2.mymailservers.com |

在上面的例子中，您指定有两个邮件服务器(即*server1.mymailservers.com*和*server2.mymailservers.com*)将代表*example.com*域接受电子邮件。因此，如果我从[cooluser@gmail.com](mailto:cooluser@gmail.com)给[contact@example.com](mailto:contact@example.com)发一封电子邮件，Gmail 的邮件服务器会对 example.com 的 MX 记录进行 DNS 查询，找到*server1.mymailservers.com*和*server2.mymailservers.com*，然后把邮件发给他们。

#### 5。其他类型的记录

有许多其他类型的 DNS 记录用于许多不同的场景- NS，PTR，SOA，SRV，TXT，URI 等。

#### 参考文献

1:[https://en.m.wikipedia.org/wiki/List_of_DNS_record_types](https://en.m.wikipedia.org/wiki/List_of_DNS_record_types)

2:[https://tools.ietf.org/html/rfc1034](https://tools.ietf.org/html/rfc1034)