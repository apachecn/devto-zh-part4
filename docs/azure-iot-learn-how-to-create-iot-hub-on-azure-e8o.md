# Azure 物联网:了解如何在 Azure 上创建物联网中心

> 原文：<https://dev.to/vishalservicenow/azure-iot-learn-how-to-create-iot-hub-on-azure-e8o>

社会将生物联系在一起，并给它们在一起和联系在一起的感觉。如果我们能把无生命的东西相互连接起来，建立一种有意义的物物交换关系，会怎么样？互联网为你做到了这一点，这个过程被称为物联网(IoT)。这篇关于 Azure IoT 的文章将帮助你更深入地理解 IoT，并告诉你如何在 Azure 云平台上使用它。

本文将关注以下几点:

IoT 是什么？
什么是 Azure？
Azure 物联网
演示:创建 Azure 物联网中心
让我们开始吧，

# IoT 是什么？

物联网(IoT)是一种通过互联网连接的设备网络，可以感知、积累和传输，而无需人类的任何干预。

让我们从这样一个问题开始讨论:是什么让人类感到活着？这是它们感知和相互交流的能力。如果我说我们可以赋予无生命物体这种能力，让它们在没有任何人类干预的情况下感知并相互交流，那会怎么样？那不是很好吗？

嗯，这就是物联网让你做的事情，这在很大程度上形成了同样的基础。

[![Alt text of image](img/7d81ca52252742fb14f49e31e2d232b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4M1qkhze--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2019/06/nwwa.png)

事实上，我们已经从传统的人与人之间的交流发展到现代的社交媒体和互联网，我们看到这些天产生了大量的数据。这意味着人们关注这些数据的时间更少，导致处理和捕获这些数据的准确性更低。如果我们有机器为我们做这件事，并且我们可以专注于跟踪这些数据，会怎么样？这肯定会减少同样的努力和成本。这种方法最终催生了物联网。

那么我们现在知道什么是物联网了吗？让我们进一步了解 Azure 以及 Azure IoT 的意义。

# 什么是 Azure？

微软 Azure 是微软云计算平台。它提供各种云计算服务，这些服务在本质上是高度可扩展和动态的。

那么这些服务有什么特别之处呢？简单来说。与其从零开始构建一个软件，让你管理和配置所有需要的资源，不如把配置资源的工作外包出去，最后以更实惠和优化的价格租用所需的资源，这不是很好吗？

微软 Azure 就是这样工作的，它就像电力消耗，你消耗电力，只为你消耗的电力付费，同时让供应商担心配置和供应。

# 蔚蓝物联网

现在我们知道什么是物联网，也知道什么是 Azure？摆在面前的问题是为什么要一起使用 Azure IoT，以及是什么使它们成为一个好的组合。Azure 为您提供高效、可扩展的物联网解决方案。它满足了经验丰富的云开发人员或刚刚开始接触物联网的人的需求。Azure 拥有强大的服务和工具来帮助您开发下一代物联网解决方案。

以下是相同的列表:

为常见物联网场景定制解决方案模板- Azure 物联网解决方案加速器

将智能从云扩展到您的边缘设备——Azure IoT Edge

连接、监控和控制数十亿物联网资产——Azure 物联网中心

创建物理空间或资产的数字模型——Azure Digital Twins

实时从时序物联网数据中获得洞察——Azure 时序洞察

构建和连接高度安全的 MCU 驱动设备——Azure Sphere

To location APIs 为 data - Azure 地图提供地理空间上下文

上面提到的服务几乎触及了使用云平台实施物联网的所有重要方面，因此也是 Azure IoT 成为良好健康的物联网选择的主要原因。

## 演示:创建 Azure 物联网中心

在我们开始之前，首先你可能想了解 Azure 物联网中心，

物联网中心是托管在云中的托管服务，充当物联网应用程序及其管理的设备之间双向通信的中央消息中心。您可以使用 Azure IoT Hub 来构建物联网解决方案，在数百万个物联网设备和云托管的解决方案后端之间进行可靠和安全的通信。您几乎可以将任何设备连接到物联网中心。

现在，让我们试着创建一个。

第一步:首先，你需要 Azure 免费层帐户。如果没有，请创建一个。

第二步:登录它，你会被带到如下所示的页面，

[![Alt text of image](img/da2673167d978405b5cf22e967e7a2ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lgLejHRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2019/06/AzureIOT.png)

第三步:点击创建资源，进入物联网，选择物联网中心。

[![Alt text of image](img/57dc6d97832ffd67991de63d412ef2cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v1VFiVEH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2019/06/AzureIOT2-1.png)

第 4 步:以下窗口将会打开，填写详细信息，您就拥有了一个可以使用的物联网中心

[![Alt text of image](img/730af3e6dea58f2e9f6ce78360a45f8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2kIDGQu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2019/06/AzureIOT3.png)

来源: [Edureka](https://www.edureka.co)