# 书脊和树叶的实际应用，EGP 和 IGP 的结合！

> 原文：<https://dev.to/ngschmidt/spine-and-leaf-practical-applications-egp-and-igp-combined-9m2>

到目前为止，所有的例子都非常简单，并且独立运行良好(如果你有便宜的 L3，OSPF Clos 在校园网中也能很好地工作),但是可能不能有效地处理一些更高级的用例。

简而言之，我们即将进入利基领域。

一般来说，BGP 被认为是最先进的网络话题，直到它不是。大部分的复杂性在于 iBGP(到处都是相同的自治系统)，因为 BGP 的主要环路防止机制是 AS-Path(依靠此路由经过的 AS 数量)。

为了成功实施 iBGP(每个运营商的主要产品，因此完全可行)，网络工程师必须选择以下途径之一(非包容性列表):

*   将所有 BGP 发言人彼此完全网状连接(因子缩放)
*   实现一个路由反射器:[https://en.wikipedia.org/wiki/Route_reflector](https://en.wikipedia.org/wiki/Route_reflector)

在这种情况下，更具可伸缩性的选择是选择路由反射器，但这并不容易。

与 eBGP 相比，iBGP:

*   不关心到对等说话者的跳数。只要路由反射器的距离小于 255 跳，就没有问题
*   通常不关心解析到对等说话者的路径——这是**其他事情**的问题。

为了提供一个可扩展的、可伸缩的结构的良好示例，该结构可以将 eBGP 作为服务提供给对向网络设备，我们将实现 IS-IS 作为结构内路由协议，然后利用 iBGP 和主干交换机作为路由反射器。

首先，图表在这里:( [YAML](https://raw.githubusercontent.com/ngschmidt/labfabric-diagram/master/ibgp-clos-fabric.yml) )

[![](img/5d298612ab1c54368ac033c6352d265b.png)](https://1.bp.blogspot.com/-s_C8WI1Adrs/XQ6YIZHR8yI/AAAAAAAAApI/cRYiFg6ELRM9JGfZ2xhlw4COp6-QcqCPgCLcBGAs/s1600/iBGP%2BClos%2BFabric.png)

首先，我们要配置脊柱。请注意，未来的版本(我的家庭实验室正在使用 IOS 12.2.55，对这个来说太旧了)中，更现代的网络操作系统中的 [BGP 动态邻居](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-3sg/irg-xe-3sg-book/irg-dynamic-neighbor.html)非常有用。

注:路由反射器客户端状态在服务器端配置:

```
router bgp 65000 bgp log-neighbor-changes neighbor 10.6.0.0 remote-as 65000 neighbor 10.6.0.0 update-source L0 neighbor 10.6.0.0 route-reflector-client neighbor 10.6.0.1 remote-as 65000 neighbor 10.6.0.1 update-source L0 neighbor 10.6.0.1 route-reflector-client neighbor FD00:6::0 remote-as 65000 neighbor FD00:6::0 update-source L0 neighbor FD00:6::0 route-reflector-client neighbor FD00:6::1 remote-as 65000 neighbor FD00:6::1 update-source L0 neighbor FD00:6::1 route-reflector-client maximum-paths 2 ! address-family ipv4 neighbor 10.6.0.0 activate neighbor 10.6.0.1 activate maximum-paths 2 no auto-summary network 10.6.0.240 mask 255.255.255.254 network 10.6.240.0 mask 255.255.255.254 network 10.6.240.2 mask 255.255.255.254 exit-address-family ! address-family ipv6 neighbor FD00:6::0 activate neighbor FD00:6::1 activate network FD00:6::240/127 network FD00:6:240::/126 network FD00:6:240::4/126 no synchronization maximum-paths 2 exit-address-family! 
```

然后是叶子配置:

```
router bgp 65000 bgp log-neighbor-changes neighbor 10.6.0.240 remote-as 65000 neighbor 10.6.0.240 update-source L0 neighbor 10.6.0.241 remote-as 65000 neighbor 10.6.0.241 update-source L0 neighbor FD00:6::240 remote-as 65000 neighbor FD00:6::240 update-source L0 neighbor FD00:6::241 remote-as 65000 neighbor FD00:6::241 update-source L0 maximum-paths 2 ! address-family ipv4 neighbor 10.6.0.240 activate neighbor 10.6.0.241 activate maximum-paths 2 no auto-summary network 10.6.0.240 mask 255.255.255.254 network 10.6.240.0 mask 255.255.255.254 network 10.6.240.2 mask 255.255.255.254 exit-address-family ! address-family ipv6 neighbor FD00:6::240 activate neighbor FD00:6::241 activate network FD00:6::240/127 network FD00:6:240::/126 network FD00:6:240::4/126 no synchronization maximum-paths 2 exit-address-family! 
```

请注意，还没有 BGP 对等体启动——BGP 也知道问题出在哪里！

```
bgp-rr0-s0#show ip bgp06:12:15: %SYS-5-CONFIG_I: Configured from console by consoleneighBGP neighbor is 10.6.0.0, remote AS 65000, internal link BGP version 4, remote router ID 0.0.0.0 BGP state = Active Last read 00:03:02, last write 00:03:02, hold time is 180, keepalive interval is 60 seconds Message statistics: InQ depth is 0 OutQ depth is 0 Sent Rcvd Opens: 0 0 Notifications: 0 0 Updates: 0 0 Keepalives: 0 0 Route Refresh: 0 0 Total: 0 0 Default minimum time between advertisement runs is 0 seconds For address family: IPv4 Unicast BGP table version 1, neighbor version 0/0 Output queue size : 0 Index 1, Offset 0, Mask 0x2 Route-Reflector Client 1 update-group member Sent Rcvd Prefix activity: ---- ---- Prefixes Current: 0 0 Prefixes Total: 0 0 Implicit Withdraw: 0 0 Explicit Withdraw: 0 0 Used as bestpath: n/a 0 Used as multipath: n/a 0 Outbound Inbound Local Policy Denied Prefixes: -------- ------- Total: 0 0 Number of NLRIs in the update sent: max 0, min 0 Address tracking is enabled, the RIB does not have a route to 10.6.0.0 Address tracking requires at least a /0 route to the peer Connections established 0; dropped 0 Last reset never Transport(tcp) path-mtu-discovery is enabled No active TCP connection 
```

注意它是怎么说的**RIB 没有到 10.6.0.0** 的路由——那是因为 iBGP 没有为我们解析下一跳。让我们通过推出支持 iBGP 的**内部网关协议(IGP)** 来解决这个问题。我使用 IS-IS 有几个原因，即:

*   像 BGP 一样，【IPv4 和 IPv6 都有一个路由协议
*   ISPF 选择性驱油
*   我现在对 OSPF 来说太时髦了

```
router isis CLOS-1 net 42.0000.0000.0000.0240.00 is-type level-2-only ispf level-2 log-adjacency-changes!interface ip router isis CLOS-1interface Loopback0ip router isis CLOS-1 
```

这适用于每台路由器，同时更改每台设备的 net-ID。看着相邻关系弹出来很有趣，所以我也在这里补充一下。

```
*Mar 1 06:31:34.670: %CLNS-5-ADJCHANGE: ISIS: Adjacency to 0000.0000.0240 (FastEthernet0/22) Up, new adjacency*Mar 1 06:31:34.670: %CLNS-5-ADJCHANGE: ISIS: Adjacency to 0000.0000.0241 (FastEthernet0/23) Up, new adjacency *Mar 1 06:31:41.565: %BGP-5-ADJCHANGE: neighbor 10.6.0.240 Up*Mar 1 06:31:47.169: %BGP-5-ADJCHANGE: neighbor 10.6.0.241 Up 
```

请注意，在这种情况下，IS-IS 解析环回的下一跳后，BGP 是如何立即弹出的。遗憾的是，我的老式实验室交换机似乎不支持 IPv6，所以我将在下一个主题中添加 OSPF v3-**在数据中心网络中实际使用 Clos**

```
bgp-rr0-s0#show ip bgp sumBGP router identifier 10.6.0.240, local AS number 65000BGP table version is 3, main routing table version 32 network entries using 234 bytes of memory4 path entries using 208 bytes of memory3/1 BGP path/bestpath attribute entries using 420 bytes of memory0 BGP route-map cache entries using 0 bytes of memory0 BGP filter-list cache entries using 0 bytes of memoryBGP using 862 total bytes of memoryBGP activity 4/0 prefixes, 6/0 paths, scan interval 60 secsNeighbor V AS MsgRcvd MsgSent TblVer InQ OutQ Up/Down State/PfxRcd10.6.0.0 4 65000 8 7 3 0 0 00:03:03 110.6.0.1 4 65000 6 5 3 0 0 00:01:51 1FD00:6:: 4 65000 0 0 0 0 0 never ActiveFD00:6::1 4 65000 0 0 0 0 0 never Active 
```

如果您想复制本实验室生成的配置，请点击此处的。