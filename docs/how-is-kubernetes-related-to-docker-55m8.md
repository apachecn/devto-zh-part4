# Kubernetes 和 Docker 有什么关系？

> 原文：<https://dev.to/elmomagalona/how-is-kubernetes-related-to-docker-55m8>

众所周知，Docker 提供容器的生命周期管理，Docker 映像构建运行时容器。但是，由于这些单独的容器必须进行通信，所以使用了 [Kubernetes](https://mindmajix.com/kubernetes-training) 。因此，Docker 构建容器，这些容器通过 Kubernetes 相互通信。因此，运行在多个主机上的容器可以使用 Kubernetes 手动链接和编排。