# VmList:管理它们的应用程序(实例！)

> 原文：<https://dev.to/julbrs/vmlist-the-app-to-manage-them-all-instances-4h3p>

<figure>[![](img/bef4bd1e9ac9e034b6000936a0537248.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ackJcyke--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AlmUCXfpn8Scr6bB7) 

<figcaption>照片由 [imgix](https://unsplash.com/@imgix?utm_source=medium&utm_medium=referral) 上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

我在一家处理太多实例系统的公司工作。我们仍然有一些运行 **Xen Dom-U** 的内部服务器，我们仍然有相当多的实例运行在 **VmWare Vsphere** 集群上，并且我们从几个月前开始转向 **AWS EC2** 实例和映像。

这是一个很好的举措，但是将所有东西都迁移到同一个地方需要时间，当员工需要与特定实例进行交互时，他们就会迷失方向。它位于哪里，我如何连接到它，谁是应用程序的所有者，等等…

我们已经决定写一个非常简单的工具，旨在列出该公司的所有实例，在我们所有的后端。

<figure>[![](img/1e0d87434fb508984f1fa5c455f4e352.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7myye-3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdNbo0ImN7Ask2eDWatf-CA.png) 

<figcaption>VmList 截图显示主要来自 AWS 的实例</figcaption>

</figure>

目前，该工具可以很好地完成工作，如果您想了解并实现它，您可以克隆以下存储库:

[gbandsmith/vmlist](https://github.com/gbandsmith/vmlist)

### 做得更多

部署该工具后，我们发现了一种改善员工体验的新方法。为了更好地控制你的开发团队，我们希望允许开发人员在短期内创建他们自己的实例。我们不希望允许他们通过 AWS 控制台启动实例，因为我们希望应用适当的标记系统。

我们已经在 VmList 应用程序中实现了这个特性，因此开发人员可以从我们在工具后端设置的一组特定 AMI (Amazon Images)中启动一个实例。

<figure>[![](img/e7e751e4d8de6fe7f7ae908cdc470888.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M8261sp---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASvqABrfliuYKRKycQD4yVw.png) 

<figcaption>带有启动实例特征的 VM list</figcaption>

</figure>

为了管理“短期”期间，VmList 设置为触发带有特定标签的实例，这由专用于该标签的 Lambda 函数触发(它在几天没有使用后终止所有这类实例)

最后，该工具为我们的开发人员提供了更大的灵活性，更好的视野，以及非常好的成本管理。

你也有这种需要吗？还是 AWS 控制台回答了你想要的一切？

感谢阅读！

* * *