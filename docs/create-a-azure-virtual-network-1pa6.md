# 创建 Azure 虚拟网络

> 原文：<https://dev.to/cheahengsoon/create-a-azure-virtual-network-1pa6>

可以使用 Azure 门户、Azure CLI 1.0 或 Azure PowerShell 创建虚拟网络。

**使用 Azure 门户**

1.登录 Azure 门户。
2。在屏幕的左上角，选择创建一个资源>网络>虚拟网络。
3。在创建虚拟网络中，输入或选择下表中的信息。

设置动作
名称:myVirtualNetwork
地址空间:10.1.0.0/16。
订阅:选择您的订阅
资源组:选择新建，进入 myResourceGroup，然后选择确定。
地点:选择一个离你近的地点。
子网名称:myVirtualSubnet。
子网地址范围:10.1.0/24。

4.保留其余的默认值，然后选择 Create。请注意这些设置。指向信息图标查看每个设置的描述:

*   DDoS 保护
*   服务端点
*   防火墙

5.选择显示突出显示数字的通知图标，然后通过选择转到资源查看新的虚拟网络。

要向您的虚拟网络添加两个虚拟机，请遵循此处详述的步骤。
[https://docs . Microsoft . com/en-us/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-portal)