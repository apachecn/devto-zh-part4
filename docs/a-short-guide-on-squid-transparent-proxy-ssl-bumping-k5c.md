# 关于 Squid 透明代理和 SSL 碰撞的简短指南

> 原文：<https://dev.to/suntong/a-short-guide-on-squid-transparent-proxy-ssl-bumping-k5c>

### 前缀

这是我的第二个收集事实的博客，是关于 [squid 缓存服务器](http://www.squid-cache.org/)系列的。再说一次，他们中的大多数将从网上收集并重新发布在这里，当然，给予信用是由于信用。

### 关于

*   Squid 是一个非常强大的代理服务器应用程序，文档非常少，非常糟糕。
*   谷歌可能不是你的朋友，因为，注意:一些配置指令可能已经过时了。

### 安装

只需通过您的软件包管理器安装即可。检查您的版本，例如`3.5.4`。用`systemctl enable --now squid`使能。检查它是否因`systemctl status squid`而崩溃，因为它不会在命令行显示任何信息。检查`/var/log/squid`中的日志——它们只在 squid 启动后出现。

您的神奇命令将是`squid -k reconfigure`，它会在任何更改后重新加载配置文件。

### 设置

关键是获得正确的配置文件`/etc/squid/squid.conf`。就像反馈很少的黑魔法。

配置文件由指令组成。他们做三件事:

1.  谁可以、如何访问代理，以及他们可以访问什么:
    *   `acl`:定义访问控制列表(源、目的地、协议等)。),
    *   `http_access`:控制 ACL 的访问(按顺序检查，第一次拒绝拒绝请求)
2.  如何到达代理以及它对传入请求的实际操作:
    *   `http_port, https_port`:代理监听的地方
    *   `ssl_bump`:拼接、窥视和撞击(拦截/检查)一些 SSL 连接
    *   `cache_peer`:将一些请求转发给另一个(缓存)代理
3.  杂项输入/输出、缓存和调试材料:
    *   `logformat, access_log`:指定记录
    *   `refresh_pattern, cache_dir`:配置缓存
    *   `debug_options`:附加调试记录

### 使用方式

*   普通转发代理:客户端通过它连接到互联网。鱿鱼:`http_port`
*   反向/加速代理:位于服务器前端，用于缓存和路由数据。鱿鱼:`http_port accel`
*   透明/拦截代理:在客户端不知道的情况下，请求通过防火墙/ iptables 路由到这个代理。鱿鱼:`http_port intercept, https_port ssl_bump intercept`

### 获取 SSL 密钥

安装`openssl`。然后:

```
mkdir -p /etc/squid/cert/
cd /etc/squid/cert/
# This puts the private key and the self-signed certificate in the same file
openssl req -new -newkey rsa:4096 -sha256 -days 365 -nodes -x509 -keyout myCA.pem -out myCA.pem
# This can be added to browsers
openssl x509 -in myCA.pem -outform DER -out myCA.der 
```

Enter fullscreen mode Exit fullscreen mode

然后将上述文件和文件夹分配给 squid 用户。

### 初始化 SSL 数据库

使用下面的配置，Squid 将为每个碰撞的 SSL 连接生成一个新的“假”自签名证书(这是客户端讨厌的)。这些将被缓存在一个文件夹中。

在 Fedora 29 上，可以通过以下方式完成:

```
sudo -u squid /usr/lib64/squid/security_file_certgen -c -s /var/spool/squid/ssl_db -M 4MB 
```

Enter fullscreen mode Exit fullscreen mode

(这是默认目录。如果你试图在没有初始化这个文件夹的情况下用 SSL 签名启动 Squid，它会崩溃，你可以用`systemctl status squid`得到一些指导

### 修复客户端

客户讨厌自签名证书是有原因的。

*   浏览器:使用`myCA.der`文件导入证书。
*   Linux:将证书文件复制到`/etc/pki/ca-trust/source/anchors`中，然后运行`update-ca-trust`或您的发行版中的任何东西，YMMV。
*   Node.js: Node 不喜欢自签名证书，您必须修改默认的 HTTPS 代理(本地或全局)或每个请求的选项。你有两个选择:
    1.  读取 PEM 文件并将`Buffer`添加到`ca`选项中。
    2.  将`rejectUnauthorized`设置为`false` -这是不安全的，允许中间人攻击。
    3.  如果使用`request`库，将`strictSSL`设置为`false`。
*   无头 Chrome(木偶师):用`--ignore-certificate-errors`启动 Chrome，用`ignoreHTTPSErrors`设置为`true`启动木偶师。

### 使用 iptables 将流量转移到透明代理

从其他计算机，我们使用`PREROUTING`链，用`-s` :
指定源

```
iptables -t nat -A PREROUTING -s 192.168.0.0/2 -p tcp --dport 80 -j REDIRECT --to-port 3129
iptables -t nat -A PREROUTING -s 192.168.0.0/2 -p tcp --dport 443 -j REDIRECT --to-port 3130 
```

Enter fullscreen mode Exit fullscreen mode

这是一个更棘手的问题，因为我们想避免转发循环(数据包被转移到 Squid，但当 Squid 完成它的工作时，它应该被发送到互联网)。幸运的是`iptables`可以区分数据包所有者用户。我们需要将输出链用于本地生成的数据包。因此，我们允许`root`和`squid`发送的数据包通过，并将其他所有数据包转发给 Squid。

```
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 80 -m owner --uid-owner root -j RETURN
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 80 -m owner --uid-owner squid -j RETURN
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 3129
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 443 -m owner --uid-owner root -j RETURN
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 443 -m owner --uid-owner squid -j RETURN
iptables -t nat -A OUTPUT -p tcp -m tcp --dport 443 -j REDIRECT --to-ports 3130 
```

Enter fullscreen mode Exit fullscreen mode

### 调试

在配置中，设置`debug_options`。例如，一些常规调试日志和详细的 ACL 信息:

```
debug_options ALL,2 28,9 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
链接

这里有很多好的链接:

*省略。见原文*

### 学分

*   这篇博客大部分摘自 [here](https://www.reddit.com/r/sysadmin/comments/a67hly/squid_proxy_a_short_guide_forward_transparent/) ，在 CC-BY-SA 许可下于 2019 年 1 月发表，有许多浓缩和我自己的轻微编辑。
*   封面图片取自[squid-cache.org](http://www.squid-cache.org/)