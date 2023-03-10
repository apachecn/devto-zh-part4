# 使用 OpenVPN 在 NAT 后的裸机上设置 Kubernetes

> 原文：<https://dev.to/drazisil/setting-up-kubernetes-on-bare-metal-behind-a-nat-with-openvpn-5dj3>

我一直想建立一个 kubernetes (k8s)集群，主要是因为我想了解它是如何工作的。但是，因为我就是我，我拒绝用简单的方法做任何事情，所以我不想使用 GCP 或 AWS。我有 4 台*裸机*在网上游荡，没有理由我不能使用它们，对吗？

实际上，有几个原因。首先，它们并不完全是裸机，而是云裸机，这意味着它们中的大多数都位于一个奇怪的 NAT 之后，不会与它们共享外部 IP。所以没有一个接口有外部 IP。第二，我的家用机器，我打算用它作为控制机器，在一个路由器后面。所以，再说一次，奈特。

这当然不是问题，对吗？

> Kubernetes 对任何网络实施提出了以下基本要求(不包括任何有意的网络分段策略):
> 
> *   一个节点上的 pod 可以与所有节点上的所有 pod 通信，而无需 NAT [1](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

哼。让我想想。AWS 和 GCP 显然必须能够通过 NAT 实现这一点，对吗？否则 IP 空间不可能存在。

我们用 VPN 吧。这篇文章看起来很完美，一个简单的 OpenVPN 安装脚本

安装它，连接客户端，客户端立即失去所有互联网连接。哼。它能 ping 通 VPN 服务器，所以那不是它。

结果是您想要编辑 server.conf 中的几行内容。

(你可以在这里找到完整的例子: [3](https://github.com/OpenVPN/openvpn/blob/master/sample/sample-config-files/server.conf) )

你想:

*   删除以`push "redirect-gateway`开头的行
*   添加`client-to-client`行

现在，服务器可以 ping 客户端，客户端也可以 ping 服务器。成功！

只剩下一件事要做，那就是告诉`kubeadmin init`使用 VPN。我使用法兰绒作为我的 pod 网络，所以我的 init 命令是`kubeadm -v 1 init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=10.8.0.1`

我们到了。

```
drazisil@central:~$ kubectl get nodes
NAME      STATUS   ROLES    AGE   VERSION
central   Ready    master   28m   v1.15.3
server1   Ready    <none>   26m   v1.15.3
server2   Ready    <none>   94s   v1.15.3 
```

Enter fullscreen mode Exit fullscreen mode