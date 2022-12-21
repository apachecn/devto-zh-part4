# 使用 CLUSTERIP 实现网络负载平衡

> 原文：<https://dev.to/rkeene/network-load-balancing-with-clusterip-93o>

## 负载均衡器少负载均衡

出于某种原因，互联网上没有太多关于 CLUSTERIP 的信息。这是一种旧技术的实现，通过 IPTables 目标扩展变得更加简单。

*   [Flavio 在 CLUSTERIP 上的技术演讲](http://www.rkeene.org/projects/info/resources/projects/ipcluster/flavios-tech-talk-on-ipcluster.html)
*   [使用 IPtables 和 Linux-HA 进行负载共享](http://www.linux-ha.org/ClusterIP)
*   微软称这种技术为“[网络负载平衡](http://technet2.microsoft.com/windowsserver/en/library/358b9815-3cd3-4912-a75a-cae85ea8d5ab1033.mspx?mfr=true)
*   [LARTC](http://lartc.org/) 在他们的文章“[如何在没有单点故障的情况下用 Linux 做简单的负载平衡](http://lartc.org/autoloadbalance.php3)”中对底层机制有更详细的解释

* * *

CLUSTERIP 的工作方式非常简单。

1.  集群的每个成员都连接到同一个[广播域]；
2.  集群的每个成员都配置有相同的多播 MAC 地址；
3.  然后，集群的每个成员过滤掉他们认为不应该处理的传入数据包:
    1.  以相对于其他节点的排他方式(即，集群中没有其他成员将处理该分组)；
    2.  并且以关于分组的包含方式(即，当集群的所有节点都启动时，每个分组将由一个节点处理)；也
    3.  通常使用以下标准:
        1.  基于源 IP；或者
        2.  基于源 IP 和源端口；或者
        3.  基于源 IP、源端口和目标端口
    4.  如果一个节点出现故障，另一个节点可以注意到并承担其传入数据包份额的责任
4.  发送的传出数据包带有群集的源 IP 和 MAC 地址，但带有下一跳路由器(网关)的目标 IP 和目标 MAC 地址

从上面的描述可以看出 CLUSTERIP 的主要弱点。传入的数据包被复制 N 次(针对集群的每个成员)，因此 CLUSTERIP(单独使用时，其他更高层的技术有时可以减轻这种情况)无法用于有效地负载平衡传入的流量带宽。传出流量不受影响，并将在负载平衡方案允许的情况下平均分配。