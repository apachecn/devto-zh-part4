# SQUID 代理和 SSL 拦截

> 原文：<https://dev.to/suntong/squid-proxy-and-ssl-interception-1oa4>

### 前缀

这将是我第一个收集关于 squid 缓存服务器的事实的博客。他们中的大部分将从网上收集并重新发布在这里，当然，给予信用是由于信用。

### 鱿鱼 SSL 拦截

上次我认真检查时，SQUID 无法透明地拦截 SSL(HTTPS)连接。一些著名的网站/文章说这是不可能的:

[http://www . FAQs . org/docs/Linux-mini/transparent proxy . html # ss 2.3](http://www.faqs.org/docs/Linux-mini/TransparentProxy.html#ss2.3)

然而，随着如今每一个网站都转向 SSL/HTTPS，谷歌惩罚了那些不这样做的网站，如果`squid`只是缓存传统的 HTTP 流量，那么缓存将越来越少，缓存将变得越来越无用，现在大多数网站都被 HTTPS 切断。

从 2011 年开始，事情发生了很大的变化，SQUID 功能的发展使这成为可能。本文探讨了如何在基础水平上设置它。鱿鱼代理基本会充当中间人的角色。这样做的动机是解密 HTTPS 连接，以应用内容过滤等等。

有人担心，透明地拦截 HTTPS 流量是不道德的，可能会导致合法性问题。但对我来说，我正在为我的家设置 squid 代理，以便更快地上网，所以这根本不是我关心的问题。YMMV。

那么，接下来是设置代理的技术细节。从头开始编译 squid 时，`--enable-ssl`开关是 SslBump 的先决条件，SQUID 用它透明地拦截 SSL 流量。

一旦安装了 SQUID，一个非常重要的步骤就是创建 SQUID 将呈现给最终客户端的证书。在测试环境中，您可以使用 OpenSSL 通过以下方式轻松创建自签名证书:

```
openssl req -new -newkey rsa:1024 -days 365 -nodes -x509 -keyout http://www.sample.com.pem  -out http://www.sample.com.pem 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑/etc/squid.conf 文件，将“`cert=`”和“`key=`”改为指向您环境中的正确文件。

为了避免每个用户自己配置他们的代理，配置 iptables 来执行目的地 NAT，基本上是将流量重定向到代理:

```
iptables -t nat -A PREROUTING -i eth0 -p tcp –dport 80 -j DNAT –to-destination 192.9.200.32:3128
iptables -t nat -A PREROUTING -i eth0 -p tcp –dport 443 -j DNAT –to-destination 192.9.200.32:3129 
```

Enter fullscreen mode Exit fullscreen mode

这当然会导致客户端浏览器阻塞并显示一个安全错误。也就是说，除非他们的计算机被预先配置为接受自签名证书(不是由“众所周知的”根 ca 签名的)，否则客户端肯定会知道它。他们需要将 Squid 的上述证书 CA 导入到他们的浏览器中才能继续。

### 学分

*   这篇博客大部分摘自[http://blog . davidvassallo . me/2011/03/22/squid-transparent-SSL-interception/](http://blog.davidvassallo.me/2011/03/22/squid-transparent-ssl-interception/)，有很多我自己的浓缩和轻微的编辑，特别是关于当浏览器窒息并显示安全错误时该怎么办。
*   封面图片取自[squid-cache.org](http://www.squid-cache.org/)