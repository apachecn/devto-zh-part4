# AWS 上的 Kubernetes 集群自动缩放器

> 原文：<https://dev.to/thnery/kubernetes-cluster-autoscaler-on-aws-5g5i>

如果资源得不到有效利用，在任何云(AWS、GCloud、MSAzure)中运行生产环境都会非常昂贵。以一个 web 平台使用 4 个 EC2 实例 t2.large 的场景为例，在我们转向 Kubernetes 之前，这是我工作的堆栈——有一天我会写这方面的内容。因此，在这种情况下，有太多的资源并不总是需要的。使用 Kubernetes，我们创建了一个更加灵活的环境，但我们始终有相同数量的节点在运行。集群自动缩放器来解决这个问题。我们可以定义最小数量的节点，并在必要时扩展它。这是我们如何为我们的环境安装和配置 Cluster Autoscaler 的方法。我们的环境基本上是这样的:

*   亚马逊 AWS EC2
*   Kubectl:服务器版本 1.10.6
*   Kops: 1.10.0
*   舵:2.11.0

在安装之前，有一些准备集群的步骤。首先，向节点实例组添加一些额外的标签。使用下面的命令打开编辑器。

```
$ kops edit ig nodes 
```

Enter fullscreen mode Exit fullscreen mode

现在在 cloudLabels 键中添加新标签。

```
spec:
  cloudLabels:
    k8s.io/cluster-autoscaler/my.cluster.com: ""
    k8s.io/cluster-autoscaler/enabled: ""
    k8s.io/cluster-autoscaler/node-template/label: ""
    kubernetes.io/cluster/my.cluster.com: owned
  ...
  minSize: 5
  maxSize: 10 
```

Enter fullscreen mode Exit fullscreen mode

这种配置使 Kubernetes 能够根据集群名称自动发现实例组。强烈建议这样做，尤其是在有多个实例组的情况下。现在为节点添加额外的 IAM 策略规则，为此，只需编辑集群配置。

```
$ kops edit cluster 
```

Enter fullscreen mode Exit fullscreen mode

现在添加下面的策略:

```
...
kind: Cluster
spec:
  additionalPolicies:
    node: |
      [
        {
          "Effect": "Allow",
          "Action": [
            "autoscaling:DescribeAutoScalingGroups",
            "autoscaling:DescribeAutoScalingInstances",
            "autoscaling:SetDesiredCapacity",
            "autoscaling:DescribeLaunchConfigurations",
            "autoscaling:DescribeTags",
            "autoscaling:TerminateInstanceInAutoScalingGroup"
          ],
          "Resource": ["*"]
        }
      ]
... 
```

Enter fullscreen mode Exit fullscreen mode

查看更新的命令是:

```
$ kops update cluster 
```

Enter fullscreen mode Exit fullscreen mode

要应用更新，只需添加标志`--yes`

```
$ kops update cluster --yes 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在集群已经更新了，是时候安装集群自动缩放器了。首先安装 Helm，它是 Kubernetes 的一个包管理器，这将使工作变得容易得多。要了解更多关于赫尔姆的信息，请查看:[https://github.com/helm/helm](https://github.com/helm/helm)。

集群自动缩放器将安装在 kube-system 名称空间中的主节点上。记得选择正确的 AWS 区域(创建集群的区域)。

要安装 Cluster Autoscaler，请使用这个命令，随意调整您的环境的值:

```
helm install --name cluster-autoscaler \
 --namespace kube-system \
 --set image.tag=v1.2.0 \
 --set autoDiscovery.clusterName=my.cluster.com \
 --set extraArgs.balance-similar-node-groups=false \
 --set extraArgs.expander=random \
 --set rbac.create=true \
 --set rbac.pspEnabled=true \
 --set awsRegion=us-east-1 \
 --set nodeSelector."node-role\.kubernetes\.io/master"="" \
 --set tolerations[0].effect=NoSchedule \
 --set tolerations[0].key=node-role.kubernetes.io/master \
 --set cloudProvider=aws \
 stable/cluster-autoscaler 
```

Enter fullscreen mode Exit fullscreen mode

Obs。:要了解应该安装哪个版本的集群自动缩放器，请查看位于[https://github . com/kubernetes/auto scaler/tree/master/Cluster-auto scaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)的发布部分

要检查的一个有趣的值是 expander。集群自动缩放器目前有 4 个扩展器。它们旨在为选择要扩展的节点组提供不同的策略。

*   random:默认扩展器，应该在没有特别需要以不同方式扩展节点组时使用:
*   most-pods:选择计划要扩展的 pods 数量最多的节点组
*   最少浪费:选择浪费最少内存/CPU 资源的节点组
*   价格:根据价格选择节点组

要获得有关 CA 的其他值的更多信息，请检查:[https://github . com/kubernetes/auto scaler/blob/master/Cluster-auto scaler/FAQ . MD](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)
使用此命令查看集群自动缩放日志:

```
$ kubectl -n kube-system get pods
NAME                                                         READY
<cluster-autoscaler-id>                                        1/1

$ kubectl -n kube-system logs -f <cluster-autoscaler-id> 
```

Enter fullscreen mode Exit fullscreen mode

为了测试它是否真的工作，创建一些 NGINX Pods 和大量的副本:

```
$ kubectl run nginx --image=nginx --port=80 --replicas=200 
```

Enter fullscreen mode Exit fullscreen mode

转到 AWS EC2 并检查自动缩放组，所需的数量将根据需要增加。要缩小规模，只需移除 NGINX deploy:

```
$ kubectl delete deploy nginx 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

Cluster Autoscaler 是使用 Kubernetes 实现高效环境的第一步。它将提供非常好的云资源自动化扩展。希望能有所帮助，如果有什么考虑，就告诉我。感谢阅读！！