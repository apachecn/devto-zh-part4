# 完成家庭媒体服务器的设置！！

> 原文：<https://dev.to/akash_rajvanshi/complete-setup-for-home-media-server-24c0>

如果你想摆脱这些在线云服务提供商，摆脱那些棘手的条款&条件，那么你必须阅读这篇文章。

<figure>[![](img/16ed85cf7f57ded95e0639de5d65e2ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--osCh7oJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhJFUKbjC9uAOYcpb) 

<figcaption>摄影由[贾斯汀·包蒂斯塔](https://unsplash.com/@justinb1221?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

### 什么是家庭媒体服务器？？

媒体服务器是一种网络设备，充当用户在网络上访问媒体文件的中心位置。任何媒体服务器系统都有三个主要组件:

1.  **服务器** —它是一种智能设备，为客户提供所需的媒体文件。
2.  **存储设备** —用于存储视频、照片、电脑文件等所有媒体文件。
3.  **客户端** —终端用户可以通过这些设备访问媒体。例子:电视、手机、你的个人电脑。

所有这三个组件必须能够相互通信，通常它们通过局域网连接(但也可能通过无线方式连接)。

<figure>[![](img/36b57f1f393a24c7af0933eabdaecb38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FMc6uUPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2ACeO0EcvQEXy23YzxR3MPdQ.png) 

<figcaption>集中式家庭媒体服务器</figcaption>

</figure>

最后，媒体服务器系统还需要具有适当的媒体服务器软件，以允许用户实际上与服务器通信并访问文件。

### 家庭媒体服务器用哪个硬件/软件最好？？

我们不能购买任何东西来设置媒体服务器，因为我们需要专门的组件来获得更好的性能，例如，我们不能在服务器中安装标准硬盘( *WD /希捷标准硬盘*)，而是需要一个企业级硬盘(*希捷 IronWolf/ WD Red 等*)用于我们的服务器。

对于手动创建家庭服务器我们需要:

> **CPU** —高性能英特尔& AMD 处理器
> 
> **RAM**—HyperX Fury 4x8GB DDR 3–1600
> 
> **HDD**—希捷 iron wolf/Pro 4TB-10TB
> 
> **SSD**—三星 840EVO
> 
> **主板**—Asrock Z77 Pro 4-M

 **对于手动设置，我们需要这样的硬件。这种类型的操作系统( *FreeNas & Ubuntu Server* )的主要缺点是，它们没有任何移动应用程序，所以我们必须使用第三方应用程序，这些应用程序在设置它们时有另一个层面的问题，而且这些服务器在家庭使用时会消耗大量的电力。

为了摆脱这一点，我更希望*准备使用* Nas 设备，这是家庭用户和小型企业用户的更好选择。根据市场建议，家庭/小型企业用户的最佳 Nas 服务是 **SYNOLOGY** 。

Synology 根据您的需求提供各种 NAS 系统。

最佳预算 NAS 是一款**Synology NAS ds 218+**

在下一节中，我将告诉您它的优缺点，并与大家分享我的完整设置。

### SYNOLOGY NAS DS218+(预算 NAS)

Synology DiskStation DS218+采用双核处理器，带有 AES-NI 加密引擎和转码引擎，提供高速文件传输并支持实时 4K 转码。DS218+是保护关键资产和跨不同平台共享文件的理想选择。Synology DS218+由 Synology 的 2 年有限保修提供支持。

Synology DiskStation DS218+专为追求紧凑、可靠的共享存储解决方案的家庭用户或小型企业而设计，可灵活地将 2 GB RAM 扩展至最高 6 GB RAM，以处理密集型工作负载。DS218+采用双核 2.0 GHz 处理器，突发频率为 2.5 GHz。借助 AES-NI，DS218+在 RAID 1 配置下提供高达 113 MB/s 的读取和 112 MB/s 的写入的加密性能 1。DS218+带有三个 USB 3.0 端口。热插拔驱动器托盘设计允许在 3.5 英寸硬盘上轻松安装和维护，无需额外工具。

Its 自带操作系统: **DSM**

它覆盖了所有的移动操作系统，如 Android、IOS 以及主要的 Linux 发行版。它拥有低功耗的英特尔赛扬处理器，功耗更低。

**与之配合使用的硬件:**

**硬盘** : *希捷铁狼 2TB 到 12TB*

**膨胀 RAM :**

1.  synology RAM DDR3L-1866 SO-DIMM 4GB(d3n 1866 l-4G)，或
2.  HyperX 4GB 1866 MHz DDR3L CL11 1.35v SODIMM HyperX Impact 笔记本电脑内存 HX318LS11IB/4

### 从头开始设置 Synology NAS DS218+

> 首先，做好所有的连接，安装硬盘和内存。

<figure>[![](img/e2da0b3b4af33c7261850877e1eb2127.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mDHXOaKE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhDC2mG98u5WXctc7ZahrRQ.png)

<figcaption>NAS 桌面</figcaption>

</figure>

#### **去找 Synology NAS**

将服务器成功连接到家用路由器后，打开浏览器。有两种方法可以在你的网络上找到 synology:

1.  Find.synology.com
2.  磁盘站:5000

#### 设置同义词

填写您的首选用户名和密码，并设置快速连接。

<figure>[![](img/029c8680b8674735e152d420f31484eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V_5iDWiD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYxVuTy4yuwgZuDIgLXggdQ.png) 

<figcaption>设置页面中的</figcaption>

</figure>

**一些快速设置:**

**1。** **首先检查 DSM 更新**

**2。** **右上角****个人设置**

*   启用账户的 **2FA** 设置。

*   根据您的需要定制登录页面。

**3。控制面板设置**

**+共享文件夹** — `{{ Create Encrypted Folder or other Folders according to your Need }}`

**+外部访问** — `{{ Advanced ➢ Write your custom Domain ( Diskstation.example.com). we have discussed the whole setup of the custom domain in the next section and also write down your custom Ports for both (HTTP/HTTPS )
For instance, in my setup, I used 5009 for HTTP and 5010 for HTTPS. }}`

**+网络** — `{{ DSM Settings ➢ Write down the ports that we have set in above settings and check on boxes of “Automatically Redirect HTTP connections to HTTPS” & Enable HTTPS v2 }}`

**+安全—**+T0】

**+其他设置—** `{{ Set Themes and other user privileges settings according to your necessity }}`

**4。包中心设置**

包中心【设置】点击“Synology Inc . and Trusted Publishers”复选框

名称:SynoCommunity

地点:【https://packages.SynoCommunity.com/】T2

这将向包管理器添加一些额外的包。

**5。

部分必备套餐(针对家庭用户)【套餐管理器】所有套餐**

*   音频站
*   云站服务器
*   DNS 服务器
*   文档查看器
*   下载站
*   超级备份
*   邮件加服务器
*   邮件加
*   照片站
*   视频站
*   USB 复制
*   网站
*   丛
*   WordPress

<figure>[![](img/e431e8e63c0f2aea358671c131232ab2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HyMq7p2N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2o13qqdS4xN9wpNnKvTptQ.png) 

<figcaption>包中心</figcaption>

</figure>

**6。如何重置 Synology NAS**

**方法 1:(在此方法中，您不会丢失 NAS 中的数据)**

按下复位按钮中的 pin 5 秒钟，然后在 Bip 后释放 pin。再次插入插销，按下 5 秒钟，3 秒钟后松开插销。这将重启 NAS，并在状态 LED 上显示红灯，然后用 find.synology.com*再次连接您的 synology 并重新安装 DSM。*

**方法二:(在此方法中，数据将被完全擦除)**

打开控制面板**更新部分**复位****

 ****#### **如何将您的服务器设置为您的个人域名**

*   **购买域名。**

对于我的服务器，我已经从 NameCheap.com 购买了一个域名，并设置了个人电子邮件试用服务。这将需要在您的域中添加 SSL。

*   **将您的域连接到您的服务器的 DNS 设置**

将您的记录添加到您的域中。在我的设置中，我的子域是 *Synologyds，*你可以随意设置你的子域。******