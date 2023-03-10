# Kubernetes 初学者术语

> 原文：<https://dev.to/azure/kubernetes-terminology-for-beginners-2pdb>

在我的上一篇博文中，我花时间向您展示了如何构建 Azure Kubernetes 服务集群。这一次我们将回顾一些 kubernetes 的特定术语，以便随着我们的不断发展，我们确切地知道这些术语的含义。

# 术语概述

让我们来了解一些术语。以下是来自于 Kubernetes 的 Azure Kubernetes 服务核心概念(AKS)文档的一些关键内容。

**豆荚**

Kubernetes 使用 pods 来运行应用程序的一个实例。一个 pod 代表应用程序的一个实例。pod 通常与容器具有 1:1 的映射关系，尽管在一些高级场景中，一个 pod 可能包含多个容器。这些多容器 pod 在同一节点上一起调度，并允许容器共享相关资源。

**部署和 YAML 清单**

一个部署代表一个或多个相同的 pod，由 Kubernetes 部署控制器管理。部署定义了要创建的副本(pod)的数量，Kubernetes 调度程序确保如果 pod 或节点遇到问题，会在健康的节点上调度额外的 pod。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
spec:
  selector:
      matchLabels:
        app: demo-app
  replicas: 1
  template:
      metadata:
        labels:
            app: demo-app
      spec:
        containers:
        - name: demo-app
          image:  ACRHERE.azurecr.io/demo-app:v1
          imagePullPolicy: Always
          readinessProbe:
            httpGet:
              port: 8000
              path: /
          livenessProbe:
            httpGet:
              port: 8000
              path: /
          resources:
            requests:
              memory: "128Mi"
              cpu: "100m"
            limits:
              memory: "256Mi"
              cpu: "500m" 
```

Enter fullscreen mode Exit fullscreen mode

**状态集合和异常集合**

部署控制器使用 Kubernetes 调度程序在任何具有可用资源的可用节点上运行给定数量的副本。这种使用部署的方法对于无状态应用程序来说可能足够了，但是对于需要持久命名约定或存储的应用程序来说就不够了。对于需要副本存在于集群中的每个节点或选定节点上的应用程序，部署控制器不会查看副本是如何跨节点分布的。

有两种 Kubernetes 资源可以让您管理这些类型的应用程序:

*   状态集——在单个 pod 生命周期之外维护应用程序的状态，如存储。
*   daemon sets——在 Kubernetes 引导过程的早期，确保每个节点上都有一个正在运行的实例。

**舵**

Kubernetes 中管理应用程序的一种常见方法是使用 Helm。您可以构建并使用包含应用程序代码打包版本和 Kubernetes YAML 清单的现有公共掌舵图来部署资源。这些舵图可以存储在本地，或者通常存储在远程存储库中，比如 Azure 容器注册中心舵图报告。

要使用 Helm，需要在 Kubernetes 集群中安装一个名为 Tiller 的服务器组件。Tiller 管理集群中图表的安装。Helm 客户端本身安装在您的本地计算机上，或者可以在 Azure Cloud Shell 中使用。您可以使用客户端搜索或创建舵图，然后将它们安装到您的 Kubernetes 集群。

有关更多信息，请参见[在 Azure Kubernetes 服务(AKS)中使用 Helm 安装应用程序。](https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm?WT.mc_id=devto-blog-jagord)

## 视频

这里有一个视频，介绍了微软杰出的工程师 Brendan Burns 对 kubernetes 术语的理解:

[https://www.youtube.com/embed/daVUONZqn88](https://www.youtube.com/embed/daVUONZqn88)

## 下一个？

这些只是 Azure Kubernetes 服务文档中的一小部分。多阅读一些文档，然后马上开始构建一些东西。

如果你想尝试 Azure Kubernetes 服务，请查看免费的 200 美元点数和 12 个月的免费 Azure 服务。