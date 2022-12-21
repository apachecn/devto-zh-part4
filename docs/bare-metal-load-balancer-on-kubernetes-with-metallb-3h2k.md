# 带有 metallo 的 kubernetes 上的裸机负载平衡器

> 原文：<https://dev.to/drazisil/bare-metal-load-balancer-on-kubernetes-with-metallb-3h2k>

迷你系列的第二部分，第一部分在这里 [1](https://dev.to/drazisil/setting-up-kubernetes-on-bare-metal-behind-a-nat-with-openvpn-5dj3)

现在我在裸机上有了一个可用的 Kubernetes 集群，下一件事就是让外部流量进入其中，而不需要`kubectl proxy`。使用 API 路径访问像 dashboard 这样的站点似乎很愚蠢。

让流量进入集群的最干净的方法似乎是负载均衡器。然而，它需要一个通常由 GCP 或 AWS 提供的外部服务，而这不是 Kubernetes 提供的。怎么办？

幸运的是，有一款名为 MetalLB [2](https://metallb.universe.tf/) 的神奇软件，专为在裸机上创建负载平衡器而设计。尽管处于 alpha 阶段(在撰写本文时),但它运行良好，在各种情况下都受到人们的喜爱，正如 GitHub“问题” [3](https://github.com/danderson/metallb/issues/5#) 所证明的那样。

我使用 manifest 选项安装了它，没有做任何修改，并使用了第 2 层配置 [4](https://metallb.universe.tf/configuration/#layer-2-configuration) ，使用了默认 OpenVPN 范围`10.8.0.0`中的 IP 地址子集。我使用这些 IP 地址的原因是，我希望能够从集群中的任何机器访问我的“外部”服务，包括我的主机。

我最初计划修改一个入口并让站点运行，但是 nginx 入口控制器 pod 和我的节点上的 Apache 彼此不满意，所以为了取得成功，我决定从仪表板开始。

为单个端口 [5](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#configuration-file) 创建一个负载平衡器服务是一个非常基本的任务，所以我将在这里分享我的清单:

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/name: load-balancer-dashboard
  name: dashboard-service
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 8080
      protocol: TCP
      targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  type: LoadBalancer 
```

一旦我看到我的仪表板服务器获得了什么外部 IP，我就可以转到机器上的浏览器并访问`https://<external-ip>:8080/`来访问我的仪表板。不再有`kubectl proxy`！

又及:我差点忘了。请确保注释掉控制面板清单的`tolerations`部分(或将它们添加到 MetalLB 清单中),以便让控制面板和 metallb 在同一节点上运行。询问您是否尝试过这种方法，并发现了任何问题！