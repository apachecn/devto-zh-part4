# 使用 iptables 防火墙规则保护您的 Linux 桌面

> 原文：<https://dev.to/dandyvica/securing-your-linux-desktop-using-iptables-firewall-rules-3p86>

今天，我将讨论的不是一个具体的开发主题，而是一种保护您的 Linux 桌面访问互联网的方法。我使用的是 Linux Mint 19.1 版本，但是一旦安装了 *iptables* 包，它在所有 Linux 发行版中都应该是一样的。

*iptables* 是一个管理防火墙规则的 Linux 命令。它基本上过滤来自或去往外部世界的网络包。

有很多很棒的资源描述了 iptables 的具体细节，我不打算在这里详述。我更关注的是如何建立简单的规则来保护你的桌面互联网连接。这是对其他安全技术的补充，例如使用隐私意识的浏览器扩展，如 *uBlock origin* 或 *uMatrix* ，或使用 *AppArmor* 限制框架等等。

处理 *iptables* 时首先要理解的是链。链就像网络包交换的通道(不说 *NAT* 和 *MANGLE* 表，不是这里的主题)，在 *FILTER* 表中。一个链包含一个规则列表，这些规则按照创建的顺序依次应用。如果数据包不符合规则，它将被拒绝、接受或丢弃。

要定义一个规则，您可以使用 *iptables* 命令(需要 *root* 访问权限)。或者创建一个规则文件，并使用 *iptables-restore* 命令导入所有规则。

有三条链:

*   输入链用于来自外界的所有输入数据包
*   当主机被视为向另一台主机转发数据包的网关时，使用转发链
*   输出链是针对从我的 Linux 桌面发出的所有数据包的

防火墙强化你的桌面的策略是拒绝一切，然后只接受你想要的包。

我的设置如下:

```
# load firewall config with iptables-restore < iptables.rules

*filter

# 1: set default DROP policy
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT DROP [0:0]

# 2: accept any related or established connection
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 3: allow all traffic on the loopback interface
-A INPUT -i lo -j ACCEPT
-A OUTPUT -o lo -j ACCEPT

# 4: allow outbound DHCP requests
-A OUTPUT -p udp --dport 67:68 --sport 67:68 -j ACCEPT

# 5: allow outbound DNS lookups
-A OUTPUT -p udp -m udp --dport 53 -j ACCEPT

# 6: allow outbound ping requests
-A OUTPUT -p icmp -j ACCEPT

# 7: allow outbound NTP requests
-A OUTPUT -p udp --dport 123 --sport 123 -j ACCEPT

# 8: allow outbound http/https requests
-A OUTPUT -p tcp -m tcp --dport 80 -m state --state NEW -j ACCEPT
-A OUTPUT -p tcp -m tcp --dport 443 -m state --state NEW -j ACCEPT

# 9: allow SMTP
-A OUTPUT -p tcp --dport 25 -m state --state NEW -j ACCEPT

# 10: allow incoming IMAP/IMAPS
-A OUTPUT -p tcp --dport 143 -m state --state NEW -j ACCEPT
-A OUTPUT -p tcp --dport 993 -m state --state NEW -j ACCEPT

# 11: access SSH server
-A OUTPUT -p tcp -m tcp --dport 22 -j ACCEPT

# commit changes
COMMIT 
```

Enter fullscreen mode Exit fullscreen mode

前面的规则基本上是传递给 *iptables* 命令的所有参数。主要是:

*   -A:追加到链名
*   -p:协议名称
*   -m:传递给一个 *iptables* 模块的附加选项
*   - sport:源端口
*   - dport:目的端口
*   -j:每当这样的数据包被匹配时的动作
*   - state:连接状态模块选项

让我们来分解这些规则:

1.  默认情况下，丢弃所有链的所有数据包
2.  接受任何状态为已建立(已知并跟踪连接)或相关(从已建立的连接发起)的数据包，无论是传入还是传出
3.  环回接口为 *127.0.0.1* 或*本地主机*。这是本地的，所以接受所有的联系
4.  允许从 tcp 源端口 67 或 68 到目的端口 67 或 68 的 dhcp 请求
5.  允许 DNS 请求到端口 53
6.  如果您想 ping 一个地址，您需要这个规则
7.  如果你使用时间协议来设置你的时钟，你需要这个规则
8.  核心一条:只允许 *http* 或 *https* 协议从你的盒子里发出，那些发起连接的( *-状态新*)
9.  如果你使用像雷鸟这样的邮件软件来阅读和发送邮件，这就为 SMTP 服务器增加了一条规则
10.  IMAP/IMAPS 服务器也是如此
11.  如果您必须访问远程 SSH 服务器(例如您的 AWS ionstance ),请添加这条规则

您还可以微调这些规则。例如，您可以使用 *-s* 参数列出几个 ip 地址或 URL 来限制目的地址。您还可以为特定的接口名称定义一个规则，比如 *-i eht0* 。

然后只需通过:
加载这些规则

```
$  sudo iptables-restore < iptables.rules 
```

Enter fullscreen mode Exit fullscreen mode

这些将在下次重新启动时被删除。为了使它们永久化，安装*iptables-persistent*packed:

```
$ sudo apt-get install iptables-persistent 
```

Enter fullscreen mode Exit fullscreen mode

希望这有所帮助！

> 安迪·阿特在 Unsplash 上拍摄的照片