# 今天的三件事- 8/29

> 原文：<https://dev.to/goatmale/three-things-from-today-8-29-2ai9>

# [8/29·T1](#829)

### 又回来了。

大家好！希望你周四过得愉快。我知道我是。我很高兴与你分享我今天学到或发现有趣的三件事。

##### 1。Kubernetes kube-proxy 实际上是做什么的？

在我学习 K8s 的过程中，我一直试图将每个组件分解成快速可理解的部分。今天我快速浏览了一下 kube-proxy。

### 那么 kube-proxy 实际上是做什么的？

*   Kube-proxy 作为 pod 存在于每个节点上。
*   Kube-proxy 管理服务的虚拟 IP。
*   在大多数版本中，默认情况下，Kube-proxy 使用 iptables 来完成这项工作。
    *   iptable 规则与 Linux“netfilter”内核挂钩进行交互，以使
    *   这发生得很快，且没有太多开销。
*   这允许您使用虚拟 IP 将服务流量路由到各种后端 pod。
*   Kube-proxy 充当监视 k8s api 的看门狗，如果服务发生变化，它会更新相关的 IPtables 规则。

通过查看 kube-proxy pod 的日志，您可以看到您正在使用的模式:

```
kubectl logs kube-proxy-qfqxk
W0804 15:49:33.304448       1 server.go:198] WARNING: all flags other than --config, --write-config-to, and --cleanup are deprecated. Please begin using a config file ASAP.
I0804 15:49:33.618663       1 server_others.go:148] Using iptables Proxier. 
```

Enter fullscreen mode Exit fullscreen mode

整洁！

##### 2。 [Linux 编程接口](http://man7.org/tlpi/index.html)——我正在看的新书

好吧，我实话实说，通过 Safari 这样的服务，你可以随意使用大量的书籍，这让你只能阅读第一章，对于很多书来说，其他的真的不多。我希望实际上保持这种状态，并学习许多使 Linux 工作的低级魔法。

##### 3。[安全眼](http://slgobinath.github.io/SafeEyes/)👀- FOSS Linux 眼睛疲劳预防工具

这个工具最近真的对我的眼睛很有帮助。每隔 15 分钟，它会要求你做一些事情，比如盯着远处看 15 秒钟，这不仅有助于你的眼睛，还可以快速休息一下，清空你的大脑。我担心上下文切换，但对我来说，这似乎不会对我的工作效率产生负面影响，事实上，我认为它实际上是有帮助的。