# 教程:如何在 Kubernetes 中编排无状态应用？

> 原文：<https://dev.to/itnext/stateless-apps-in-kubernetes-beyond-pods-4p52>

你好 Kubernauts！欢迎来到“果壳中的库伯内特”博客系列:-)

这是介绍用于管理无状态应用程序的原生 Kubernetes 原语的第一部分。Kubernetes 最常见的用例之一是编排和操作无状态服务。在 Kubernetes 中，您需要一个`Pod`(或者在大多数情况下需要一个`Pod`的*组*)来表示一个服务或应用程序——但是还有更多！我们将超越基本的`Pod`，探索其他高级组件，即`ReplicaSet`和`Deployment`

和往常一样，代码[可以在 GitHub](https://github.com/abhirockzz/kubernetes-in-a-nutshell) 上获得

[![](img/7374c44096e4593eecf008d2e1d41d5c.png)](https://i.giphy.com/media/l0IyjiXOXTX6Yemsg/giphy.gif)

首先，您需要一个 Kubernetes 集群。这可以是一个简单的单节点本地集群，使用 [`minikube`](https://kubernetes.io/docs/setup/learning-environment/minikube/) 、 [`Docker for Mac`](https://blog.docker.com/2018/01/docker-mac-kubernetes/) 等。或者来自[Azure](https://docs.microsoft.com/azure/aks/?WT.mc_id=devto-blog-abhishgu)、 [Google](https://cloud.google.com/kubernetes-engine/) 、 [AWS](https://aws.amazon.com/eks/) 等的托管 Kubernetes 服务。要访问你的 Kubernetes 集群，你需要 [`kubectl`](https://kubernetes.io/docs/reference/kubectl/overview/) ，这很容易安装。

例如，要为 Mac 安装`kubectl`,您只需

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl && \
chmod +x ./kubectl && \
sudo mv ./kubectl /usr/local/bin/kubectl 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经安装了 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&WT.mc_id=devto-blog-abhishgu) ，你需要做的就是 [`az acs kubernetes install-cli`](https://docs.microsoft.com/cli/azure/acs/kubernetes?view=azure-cli-latest&WT.mc_id=devto-blog-abhishgu#az-acs-kubernetes-install-cli) 。

> 如果你对使用 [Azure](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=devto-blog-abhishgu) 学习 Kubernetes 和 Containers 感兴趣，只需[创建一个**免费**账户](https://azure.microsoft.com/en-us/free/?WT.mc_id=devto-blog-abhishgu)就可以开始了！一个好的起点是使用文档中的[快速入门、教程和代码示例](https://docs.microsoft.com/azure/aks/?WT.mc_id=devto-blog-abhishgu)来熟悉这项服务。我也强烈推荐查看 [50 天 Kubernetes 学习路径](https://azure.microsoft.com/resources/kubernetes-learning-path/?WT.mc_id=devto-blog-abhishgu)。高级用户可能希望参考 [Kubernetes 最佳实践](https://docs.microsoft.com/azure/aks/best-practices?WT.mc_id=devto-blog-abhishgu)或观看一些[视频](https://azure.microsoft.com/resources/videos/index/?services=kubernetes-service&WT.mc_id=devto-blog-abhishgu)以了解演示、主要功能和技术会议。

让我们从理解`Pod`的概念开始。

## `Pod`

一个`Pod`是 Kubernetes 中最小的可能抽象，它可以运行一个或多个容器。这些容器共享资源(存储、卷)，并且可以通过`localhost`相互通信。

使用下面的 YAML 文件创建一个简单的`Pod`。

> `Pod`只是一个 Kubernetes 资源或对象。YAML 文件是描述其期望状态和一些基本信息的东西——它也被称为`manifest`、`spec`(规范的简写)或`definition`。