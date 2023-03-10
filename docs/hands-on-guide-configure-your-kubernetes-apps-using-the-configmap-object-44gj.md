# 了解如何使用 ConfigMap 配置 Kubernetes 应用程序

> 原文：<https://dev.to/itnext/hands-on-guide-configure-your-kubernetes-apps-using-the-configmap-object-44gj>

“配置与代码分离”是 [12 因素应用](https://12factor.net/config)的原则之一。我们将可以改变的东西具体化，这反过来有助于保持我们的应用程序的可移植性。这在 Kubernetes 世界中是至关重要的，在那里我们的应用程序被打包成 Docker 映像。Kubernetes `ConfigMap`允许我们从代码中抽象出配置，最终抽象出 Docker 映像。

这篇博文将为 Kubernetes 中可用的应用配置相关选项提供实践指南。

和往常一样，代码[可以在 GitHub](https://github.com/abhirockzz/kubernetes-in-a-nutshell) 上获得。所以让我们开始吧....

[![](img/26cf6090126e7e799ee16033b1094c90.png)](https://i.giphy.com/media/l2SpYDOZmp3H2cAAo/giphy.gif)

要在 Kubernetes 中配置您的应用程序，您可以使用:

*   好的旧环境变量
*   `ConfigMap`
*   这将在随后的博客文章中讨论

首先，您需要一个 Kubernetes 集群。这可以是一个简单的单节点本地集群，使用 [`minikube`](https://kubernetes.io/docs/setup/learning-environment/minikube/) 、 [`Docker for Mac`](https://blog.docker.com/2018/01/docker-mac-kubernetes/) 等。或者来自[Azure](https://docs.microsoft.com/azure/aks/?WT.mc_id=devto-blog-abhishgu)、 [Google](https://cloud.google.com/kubernetes-engine/) 、 [AWS](https://aws.amazon.com/eks/) 等的托管 Kubernetes 服务。要访问你的 Kubernetes 集群，你需要 [`kubectl`](https://kubernetes.io/docs/reference/kubectl/overview/) ，这很容易安装。

例如，要为 Mac 安装`kubectl`,您只需

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl && \
chmod +x ./kubectl && \
sudo mv ./kubectl /usr/local/bin/kubectl 
```

Enter fullscreen mode Exit fullscreen mode

### 使用环境变量进行配置

让我们从一个简单的例子开始，看看如何通过在我们的`Pod`规范中直接指定环境变量来使用它们。