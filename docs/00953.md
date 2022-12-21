# 简化的 Kubernetes 存储配置

> 原文：<https://dev.to/adipolak/kubernetes-storage-config-simplified-3fhn>

*   刚到库伯内特吗？简单阅读[Kubernetes](https://dev.to/adipolak/kubernetes-and-virtual-kubelet-in-a-nutshell-gn4)

来自 Kubernetes 网站:

> 容器中的磁盘上文件是短暂的，这给在容器中运行的重要应用程序带来了一些问题。

这意味着由于 pod 是一次性的，我们不能将文件保存到容器或 pod 文件系统中。崩溃时，管理它的 kubelet 将重新启动容器/pod，并删除所有文件。

👉**我们为什么需要储物？**

一些最佳实践提到，微服务应该是无状态的，但现实表明，有时应用程序需要能够存储和检索数据。它可以来自文件系统或 NoSQL / SQL 存储。

为了让这些数据可用，我们需要配置 [*卷*](https://kubernetes.io/docs/concepts/storage/volumes/) 。

## 什么是卷？

在讨论数据存储时，**卷**指的是逻辑驱动器。硬盘上的实际物理文件系统。

对于我们想要使用的任何类型的存储，我们都需要首先使用 YAML 文件来定义它们。

卷有很多种类型，我们将重点介绍其中的 **3** :

### 💾空目录

使用`emptydir`我们可以在节点上定义一个目录。
但是，如果节点关闭了，`emptydir`的内容就会被删除。

在 YAML 文件中，它将是这样的:

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}<o:p></o:p> 
```

### 💽主机路径

还有一个是`hostPath`。

使用`hostPath`,我们将主机节点文件系统中的一个目录或文件挂载到 pod 中。
`hostPath`和`emptyDir`的主要区别在于`hostPath`可能不是一个空目录。
顺便说一句，`hostPath`和特权 Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 可用于通过在主机上运行命令来配置底层虚拟机。然而，我们应该小心这样做，因为它会导致安全漏洞。

*   **DeamonSet** -在后台定义的节点上运行的一组进程。例如日志收集类型的 DeamonSet。这意味着我们希望在后台为我们所有的 pod 运行日志收集过程，这样我们就可以收集日志并将它们存储在一个专用的地方。

在 YAML 文件中，它将是这样的:

```
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: Directory 
```

最后但同样重要的是:

### 🌤️公共云

如果你使用的是公共云，你可以利用许多现有的服务，比如 AWS Elastic Block Store 和 Azure [Disk Storage](https://azure.microsoft.com/en-us/services/storage/disks/?WT.mc_id=devto-blog-adpola) 。

## 💡这是一个总结！资源并了解更多信息:

*   [带有 AK 的 Kuberenetes 中的 SQL server 容器](https://docs.microsoft.com/en-us/sql/linux/tutorial-sql-server-containers-kubernetes?view=sql-server-2017/?WT.mc_id=devto-blog-adpola)
*   [立方体积](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir)
*   [AKS 中存储和备份的最佳实践](https://docs.microsoft.com/en-us/azure/aks/operator-best-practices-storage/?WT.mc_id=devto-blog-adpola)
*   这篇文章的灵感来自于:[我如何为裸机 Kubernetes 集群配置存储？](https://www.weave.works/blog/kubernetes-faq-configure-storage-for-bare-metal-cluster)
*   所有 YAML 文件示例都来自于 [Kubernetes.io](https://kubernetes.io) docs。

[![](img/0bec8ec11980a5a5a9578082393e664f.png)](https://i.giphy.com/media/3oGRFKhwAmd94V8HUQ/giphy.gif)