# 厨师食谱的流命令输出

> 原文：<https://dev.to/zedtux/stream-command-output-from-a-chef-recipe-52m1>

在本文中，我将向您展示如何实时查看 Chef recipe 的命令输出。

## 执行一个命令，厨师道

作为一个命令示例，正如我现在正在使用的，让我们来看看 Kubernetes 安装工具: [kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/) 。

这个应用程序在其输出中告诉你他在干什么。

我第一次这样做: