# 创建一个 AKS 集群

> 原文：<https://dev.to/cheahengsoon/create-an-aks-cluster-14kp>

1.  打开 Azure 门户。
2.  选择搜索框旁边的云壳图标。
3.  选择 PowerShell，然后创建 Azure 文件共享。
4.  使用 Azure PowerShell 创建资源组:

**注意**:要找到您附近的位置，请参考 https://azure.microsoft.com/regions/services 的[。](https://azure.microsoft.com/regions/services)

**PS Azure:>**az group create–name az 500–location westus

Azure 输出以下内容:

Azure:/

" id ":"/subscriptions/61f 927 e 9-94e 6-4f6d-a737-5d 482 C6 f 4316/resource groups/az 500 "，

" location": "westus "，" managedBy": null，" name": "AZ500 "，" properties": {

" provisioning state ":" Succeeded " }，" tags": null，" type": null}

5.创建 Kubernetes 集群，这是一个三节点集群。构建集群时,–no-wait 会返回到命令行界面(CLI)窗口:

**PS Azure:>**az aks create–resource-group az 500-name alamo-node-count 3–generate-ssh-keys–no-wait

Azure 输出以下内容:

Azure:/

SSH 密钥文件'/home/philip/。ssh/id_rsa '和/home/philip/。“ssh/id_rsa.pub”已在~/下生成。ssh 允许通过 SSH 访问虚拟机。如果使用没有永久存储的机器，如没有附加文件共享的 Azure Cloud Shell，请将您的密钥备份到安全的位置

已完成服务主体创建[# # # # # # # # # # # # # # # # # # # # # # # # # #]100.0000%

Azure:/

6.查看 Azure 门户中的资源组，找到您创建的 Kubernetes 服务。