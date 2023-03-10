# 如何向 Azure 上的 Kubernetes 应用添加持久存储

> 原文：<https://dev.to/azure/how-to-add-persistent-storage-to-your-kubernetes-apps-on-azure-2kb2>

在这篇博文中，我们将看一个例子，如何使用 [Azure Disk](https://azure.microsoft.com/services/storage/disks/?WT.mc_id=devto-blog-abhishgu) 作为部署到 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=devto-blog-abhishgu)的应用的存储介质。

您将:

*   在 Azure 上设置 Kubernetes 集群
*   创建一个 Azure 磁盘和相应的`PersistentVolume`
*   为应用程序`Deployment`创建一个`PersistentVolumeClaim`
*   测试一下，看看它是如何端到端工作的

### 概述

您可以使用 Kubernetes `Volume` s 为您的应用程序提供存储。Kubernetes 支持多种类型的卷。将它们分类的一种方法如下

*   **短暂的** - `Volume` s，与`Pod`生存期紧密耦合(如`emptyDir`卷)，即如果`Pod`被移除(出于任何原因)，它们将被删除。
*   **持久** - `Volume` s，用于长期存储，独立于`Pod`或`Node`生命周期。在托管 Kubernetes 产品(如 Azure Kubernetes 服务、Google Kubernetes 引擎等)的情况下，这可能是`NFS`或基于云的存储。

Kubernetes `Volume`可以通过`static`或`dynamic`的方式进行调配。在“静态”模式下，手动创建存储介质，如 Azure 磁盘，然后使用如下的`Pod`规范进行引用:

```
 volumes:
        - name: azure
            azureDisk:
            kind: Managed
            diskName: myAKSDisk
            diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk 
```

Enter fullscreen mode Exit fullscreen mode

> 我强烈推荐阅读关于如何[“在 Azure Kubernetes 服务(AKS)中手动创建和使用带有 Azure 磁盘的卷”的优秀教程](https://docs.microsoft.com/azure/aks/azure-disk-volume?WT.mc_id=devto-blog-abhishgu)

### 有没有更好的办法？

[![](img/50f2b377a465b62df77885daee754447.png)](https://i.giphy.com/media/26gR0YFZxWbnUPtMA/giphy-downsized.gif)

在上面的`Pod`清单中，存储信息直接在`Pod`中指定(使用`volumes`部分)。这意味着开发人员需要知道存储介质的所有细节，例如在 Azure Disk 的情况下——`diskName`、`diskURI`(磁盘资源 URI)，它是`kind`(类型)。这里肯定有改进的余地，就像软件中的大多数事情一样，可以使用持久卷和持久卷声明的概念，通过另一个层次的间接或抽象来实现。

关键思想围绕着“职责分离”以及将存储创建/管理与其使用分离开来:

*   当一个应用程序需要持久存储时，开发者可以通过在 pod 规范中“声明”来请求——这是通过使用`PersistentVolumeClaim`来完成的
*   实际的存储配置，例如创建 Azure 磁盘(使用 azure CLI、门户等)。)并在 Kubernetes 集群中表示它(使用`PersistentVolume`)可以由另一个实体来完成，比如管理员

让我们看看这是怎么回事！

### 先决条件:

您将需要以下内容:

*   [微软 Azure 账户](https://docs.microsoft.com/azure/?WT.mc_id=devto-blog-abhishgu)—[注册一个免费账户吧！](https://azure.microsoft.com/free/?WT.mc_id=devto-blog-abhishgu)
*   Azure Kubernetes 服务(AKS)集群 -本博客将指导您创建一个集群
*   Azure CLI 或 Azure Cloud Shell——如果你还没有 Azure CLI ,你可以选择安装它(应该很快！)或者直接从你的浏览器使用 [Azure 云壳](https://azure.microsoft.com/features/cloud-shell/?WT.mc_id=devto-blog-abhishgu)。
*   [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 与 yur AKS 集群进行交互

> 在您的 Mac 上，您可以这样安装`kubectl`:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl 
```

Enter fullscreen mode Exit fullscreen mode

代码可从 GitHub 上的[获得。请在继续之前克隆该存储库](https://github.com/abhirockzz/aks-azuredisk-static-pv)

```
git clone https://github.com/abhirockzz/aks-azuredisk-static-pv
cd aks-azuredisk-static-pv 
```

Enter fullscreen mode Exit fullscreen mode

### 群簇设置

你只需要一个命令就可以在 Azure 上建立一个 Kubernetes 集群。但是，在此之前，我们必须创建一个资源组

```
export AZURE_SUBSCRIPTION_ID=[to be filled]
export AZURE_RESOURCE_GROUP=[to be filled]
export AZURE_REGION=[to be filled] (e.g. southeastasia) 
```

Enter fullscreen mode Exit fullscreen mode

切换到您的套餐并调用`az group create`

```
az account set -s $AZURE_SUBSCRIPTION_ID
az group create -l $AZURE_REGION -n $AZURE_RESOURCE_GROUP 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以调用`az aks create`来创建新的集群

> 为了简单起见，下面的命令创建了一个单节点集群。请根据您的要求随意更改规格

```
export AKS_CLUSTER_NAME=[to be filled]

az aks create --resource-group $AZURE_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --node-count 1 --node-vm-size Standard_B2s --node-osdisk-size 30 --generate-ssh-keys 
```

Enter fullscreen mode Exit fullscreen mode

使用`az aks get-credentials`获取 AKS 集群凭证——因此，`kubectl`现在将指向您的新集群。你可以证实这一点

```
az aks get-credentials --resource-group $AZURE_RESOURCE_GROUP --name $AKS_CLUSTER_NAME
kubectl get nodes 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你对使用 [Azure](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=devto-blog-abhishgu) 学习 Kubernetes 和 Containers 感兴趣，一个好的起点是使用文档中的[快速入门、教程和代码示例](https://docs.microsoft.com/azure/aks/?WT.mc_id=devto-blog-abhishgu)来熟悉这项服务。我也强烈推荐查看 [50 天 Kubernetes 学习路径](https://azure.microsoft.com/resources/kubernetes-learning-path/?WT.mc_id=devto-blog-abhishgu)。高级用户可能希望参考 [Kubernetes 最佳实践](https://docs.microsoft.com/azure/aks/best-practices?WT.mc_id=devto-blog-abhishgu)或观看一些[视频](https://azure.microsoft.com/resources/videos/index/?services=kubernetes-service&WT.mc_id=devto-blog-abhishgu)以了解演示、主要特性和技术会议。

### 创建 Azure 磁盘进行持久存储

Azure Kubernetes 集群可以使用 Azure 磁盘或 Azure 文件作为数据卷。在这个例子中，我们将探索 Azure 磁盘。你可以选择由标准硬盘驱动器支持的 [Azure 硬盘或](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types?WT.mc_id=devto-blog-abhishgu#standard-hdd)[高级固态硬盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types?WT.mc_id=devto-blog-abhishgu#premium-ssd)

获取 AKS 节点资源组

```
AKS_NODE_RESOURCE_GROUP=$(az aks show --resource-group $AZURE_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query nodeResourceGroup -o tsv) 
```

Enter fullscreen mode Exit fullscreen mode

在节点资源组中创建 Azure 磁盘

```
export AZURE_DISK_NAME=<enter-azure-disk-name>

az disk create --resource-group $AKS_NODE_RESOURCE_GROUP --name $AZURE_DISK_NAME --size-gb 2 --query id --output tsv 
```

Enter fullscreen mode Exit fullscreen mode

> 我们正在创建一个容量为 2 GB 的磁盘

您将获得 Azure 磁盘的资源 ID 作为响应，这将在下一步使用

```
/subscriptions/3a06a10f-ae29-4242-b6a7-dda0ea91d342/resourceGroups/MC_testaks_foo-aks_southeastasia/providers/Microsoft.Compute/disks/my-test-disk 
```

Enter fullscreen mode Exit fullscreen mode

### 将应用部署到 Kubernetes

`azure-disk-persistent-volume.yaml`文件包含了`PersistentVolume`的详细信息。我们创建它是为了在 AKS 集群中映射 Azure 磁盘。