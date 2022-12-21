# tcpdump 备忘单

> 原文：<https://dev.to/dannypsnl/tcpdump-cheat-sheet-1522>

tcpdump？

用于分析网络数据包的命令行工具。

如何获取数据包？

使用`-i`选项`tcpdump -i <device-name>`，您将通过名为。

如何获得`<device-name>`？

使用`ip link`可以得到一些，例如

```
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:01:37:d1:9c:bd brd ff:ff:ff:ff:ff:ff
$ tcpdump -i eth0 
```

Enter fullscreen mode Exit fullscreen mode

现在我知道了如何通过某个网卡获取所有数据包，但是我想分析指定的连接，我该怎么做呢？

表情。

什么表情？

tcpdump 表达式。

什么是 tcpdump 表达式？

一组可组合的规则，例如`src 10.0.0.10`意味着只显示来自`10.0.0.10`的数据包。下面是 tcpdump 表达式列表(不完整)，`#`前导注释:

```
host 10.0.0.10 # from or to 10.0.0.10
port 23 # contains port 23
dst 10.0.0.10 # as src, but for destination
# Protocols
arp # only ARP
icmp
tcp
udp
fddi
ether
# Protocol logic is simple, is not, for all 
```

Enter fullscreen mode Exit fullscreen mode

你说它们是可组合的，它们是如何组合的？

好问题，它们可以与`or`、`and`组合，也可以与龙头`!`(非)组合；`or`是`or`，不是`and`，比如`src 10.0.0.10 or src 10.0.0.11`是针对来自`10.0.0.10`或者`10.0.0.11`的数据包，很明显。

我发现数据包没有显示我想要的所有信息，我该怎么办？

简单来说，tcpdump 有`-v`用于详细输出，`-vv`用于更详细的输出，`-vvv`用于更详细的输出。`-vvvv`为了……不，我开玩笑的。

哈，太搞笑了，时间戳对我来说不可读，怎么让它更友好？

使用`-t`，不开玩笑，使用`-ttttt`最大时间戳，你有`-kt, k <- 1..=5`不同级别的输出。

我发现 IP 被替换为主机名，但我不想要它，如何删除它？

`-n`会告诉`tcpdump`停止转换地址。

如果我想保存我努力工作的成果呢？

`-w <filename>`是你要找的，它会把原始数据包写到名为`<filename>`的文件中，而不是解析&打印。并且记住你可以将数据包记录到一个文件中，并使用 **[Wireshark](https://www.wireshark.org/download.html)** 来分析它！对于复杂的流动分析，我会这样做。现在，最重要的路径已经涵盖，你可以随时从`tcpdump`的`manpage`那里获得更多&更新的信息，祝你好运。

#### 参考文献

*   [wiki:无](https://en.wikipedia.org/wiki/Network_interface_controller)
*   男人:tcpdump