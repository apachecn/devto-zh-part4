# 如何:删除顽固的 Kubernetes 命名空间。

> 原文：<https://dev.to/david_j_eddy/how-to-delete-a-stubborn-kubernetes-namespaces-2841>

[第一次发布在我的博客上。](https://blog.davidjeddy.com/)

## 形势

最近，有人向我介绍了一个新项目。入门文档很不错，我能够启动这个项目。然而，当需要将项目从我的本地机器 Kubernetes 集群中清除时，同样的空间进入了`terminating`生命周期阶段，并在那里停留了几天。快进几个星期，我注意到它在那里，仍然终止。原来这个项目有一个`finalizer`没有回应。想象一下远程服务调用。在这种情况下，远程服务从不响应。下面是我整理的一个小脚本，用来删除`force`的一个名称空间。

## 代码

```
#!/bin/bash
k8s_delete_ns=$1
echo "Provided namesapce: ${k8s_delete_ns}..."
echo "Exporting namespace configuration..."
kubectl get namespaces -o json | grep "${k8s_delete_ns}"
kubectl get namespace ${k8s_delete_ns} -o json > temp.json
echo "Opening editor..."
wait 3
vi temp.json
echo "Sending configuration to k8s master for processing..."
curl -H "Content-Type: application/json" -X PUT --data-binary @temp.json http://127.0.0.1:8080/api/v1/namespaces/${k8s_delete_ns}/finalize
echo "Waiting for namespace deletion to process..."
wait 12
kubectl get namespaces
echo "...done."
```

这是做什么的？让我们去掉 shell、echo 和 wait 语句，因为我们知道机器实际上并不处理这些语句。

```
k8s_delete_ns=$1
kubectl get namespaces -o json | grep "${k8s_delete_ns}"
kubectl get namespace ${k8s_delete_ns} -o json > temp.json
vi temp.json
curl -H "Content-Type: application/json" -X PUT --data-binary @temp.json http://127.0.0.1:8080/api/v1/namespaces/${k8s_delete_ns}/finalize
kubectl get namespaces
```

## 那么这到底是干什么的呢？

好吧，那么我们这里有什么。第一行从命令调用中获取第一个参数，并将其赋给一个局部变量。`./script.sh stuck_namespace`。所以`stuck_namespace`是分配给`k8s_delete`的值。

接下来的两个命令获取当前的名称空间配置，并将其写入`temp.json`。

第四个命令使用`vi`打开`temp.json`配置文件。

在配置打开的情况下，我们希望删除`finalizers`数组中列出的任何项目。这是真正的神奇时刻。使用 allow kubectl 移除命名空间来移除终结器。

说到下一个命令，使用`curl`将新编辑的配置传递给 kubectl。这将使用不包含终结器的配置更新命名空间配置。

最后，`get namespaces`将在更新后输出现有的名称空间。如果一切按预期进行，列表将不包含卡住的名称空间。

## 结论

这就是了。互联网上有很多解决方案，但是 Kubernetes 的发布节奏使得其中一些在几个月内就过时了。所以让我声明:这个解决方案适用于 Kubernetes 1.13。除此之外，我希望它能像帮助我一样帮助你。

附注:如果你想了解我制作的 Kubernetes 助手，请在 [GitHub](https://github.com/davidjeddy/k8s_helper_scripts) 上查看回购协议。