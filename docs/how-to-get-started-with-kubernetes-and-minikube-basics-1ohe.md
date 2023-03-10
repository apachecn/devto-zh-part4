# 如何开始使用 Kubernetes 和 Minikube 基础知识

> 原文：<https://dev.to/peterj/how-to-get-started-with-kubernetes-and-minikube-basics-1ohe>

[https://www.youtube.com/embed/FacPam4bwRk](https://www.youtube.com/embed/FacPam4bwRk)

Minikube 是一个非常棒的工具，它允许你在自己的计算机上运行一个虚拟机内的单节点 Kubernetes 集群。

为了帮助你开始，我录制了一段视频来指导你完成 Minikube 的安装过程。在视频中，我解释并介绍了几个基本的 Minikube 命令，您可以使用这些命令来处理 Kubernetes 集群，并向您展示如何在使用 Minikube 时访问集群内部的应用程序。

这是我第一次尝试制作视频，我希望听到你的反馈！

## 管理您的 Minikube 集群

创建一个新集群就像运行`minikube start`一样简单。默认情况下，start 命令创建一个具有 2 个 CPU 和 2000 MB 内存的虚拟机。通常，这可能还不够，所以您可以使用`--cpus`和`--memory`标志来设置所需的 CPU 和内存数量，如下所示:

```
minikube start --cpus=4 --memory 8192. 
```

Enter fullscreen mode Exit fullscreen mode

要停止运行集群的虚拟机，使用`minikube stop`命令。这将停止虚拟机，但会保留所有文件，因此您可以使用 start 命令再次启动集群。如果我不使用集群，我通常会停止它，这样它就不会耗尽所有的 CPU 和内存。

## Minikube 问题故障排除

希望您不会在 Minikube 上遇到任何问题，但是，如果您遇到了问题，请使用`minikube status`命令快速获得您的集群的状态。如果您需要更深入地挖掘并获得更多关于正在发生的事情的信息，请使用`minikube logs`命令。logs 命令将输出正在运行的 Kubernetes 实例的所有日志。

## 与您的 Kubernetes 集群互动

在大多数情况下，您将使用 Kubernetes CLI ( `kubectl`)与您的集群进行交互。然而，如果你更直观，你也可以使用 Kubernetes 仪表板。Minikube 自动安装 Kubernetes 仪表板，它有一个方便的命令`minikube dashboard`,可以创建仪表板的代理并在浏览器中打开仪表板 UI。

要访问您的集群和集群中运行的服务，您将使用集群 IP。要获取集群的 IP 地址，请运行`minikube ip`命令。该命令将集群 IP 输出到标准输出。您可以使用 IP 地址和服务端口的组合来访问服务。

Minikube 还提供了两个命令，允许您快速访问服务。一个命令叫做`minikube service`。如果您的服务属于`NodePort`类型，您可以使用这个命令快速访问服务。Minikube 将使用集群 IP 和端口，并在浏览器中打开您的服务。

或者，如果您有使用负载平衡器类型的服务，您可以使用`minikube tunnel`命令。这个命令公开了在`http://localhost`地址上所有类型为`LoadBalancer`的服务。