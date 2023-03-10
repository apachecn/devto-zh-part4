# 将附加网络接口(NIC)和公共 IP(PIP)关联到虚拟机

> 原文：<https://dev.to/patricksameerajayalath/associate-additional-network-interface-nic-and-public-ip-pip-to-virtual-machine-24>

在本教程中，我将展示如何:

创建新的资源组
创建虚拟网络并将其与资源组关联
将子网添加到虚拟网络
将虚拟机添加到虚拟网络
将多个 NIC 和 pip 与虚拟机关联
远程连接到虚拟机
在虚拟管理器上安装 IIS
向虚拟机发送 http 流量

在本教程的最后，我们将得到一个类似下图的网络拓扑。

[![Alt Text](img/9b7ec864a45f69046d8003baf18e58dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mN7iez7b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yg5z9hjt5v6cwhicqqnw.JPG)

你可以在这里找到这个教程[的 PDF 版本。](https://github.com/patricksameerajayalath/AzureTutorials/blob/master/Associate%20additional%20NIC%20and%20PIP%20to%20Virtual%20Machine.pdf)

尽情享受吧！！