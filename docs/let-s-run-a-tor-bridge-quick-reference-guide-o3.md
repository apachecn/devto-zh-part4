# 让我们跑一个 Tor 桥！快速参考指南

> 原文：<https://dev.to/vergeev/let-s-run-a-tor-bridge-quick-reference-guide-o3>

在最近的一封信中，Tor 项目请求帮助建立新的、不受审查的桥梁。因为这是一个为项目做贡献的好方法，所以我就去做了。在这样做的时候，我编辑了这个快速参考指南，关于如何运行桥接中继以及为什么这样做是一个好主意。

# Tor 是什么

[观看 Tor 的视频概述](https://www.youtube.com/watch?v=JWII85UlzKw)

**TL；DR** : Tor 是一个规避审查和在线保护身份的网络。

# Tor 如何工作

[阅读 Tor 内部工作的详细描述](https://2019.www.torproject.org/about/overview.html.en#thesolution)

**TL；DR**:Tor 浏览器连接到公开上市的互联节点网络。他们以一种随机的方式互相发送数据包，匿名发送数据包的人。

# Tor 桥有什么作用

[阅读 Tor 桥梁的文件](https://www.torproject.org/docs/bridges.html)

**TL；博士**:桥接继电器允许连接到 Tor 网络，即使它被政府禁止。此外，桥隐藏了一个人使用 Tor 的事实。

# 为什么要跑桥

[看帖子要求多跑几座桥](https://blog.torproject.org/run-tor-bridges-defend-open-internet)

**TL；博士**:它让生活在压迫政权下的人们能够访问开放的互联网。自由获取信息是一项基本人权。

# 如何跑桥

1.  租一个 VPS 或者使用你已经拥有的。我发现[数字海洋](https://www.digitalocean.com/)是最方便使用的提供商，但我还是选择了[我当地的提供商](https://vscale.io)来降低传递的成本。
2.  遵循安装说明。 [Docker 安装](https://community.torproject.org/relay/setup/bridge/docker/)是最简单的一种。

# 监控

[安装后说明](https://community.torproject.org/relay/setup/bridge/post-install/)描述了该过程。您还可以通过查看中继日志来统计用户数量。在你桥得到第一个用户之前，可能需要几个月的时间。建议耐心等待，直到你的桥积累了用户。

**TL；博士**:进入[接力搜索](https://metrics.torproject.org/rs.html)，输入你的接力指纹。这是你将得到的页面的[示例。将它加入书签，你就可以监控你的桥梁活动了。](https://metrics.torproject.org/rs.html#details/EDA4827F76C8815EE3D33B20E942C69899EACC4E)

* * *

非常感谢 Tor 项目的 Philipp Winter 帮助我搭建了我的第一座桥。

如果你发现自己需要建议，不要犹豫给我发电子邮件到 [pavel@vergeev.site](mailto:pavel@vergeev.site) 。