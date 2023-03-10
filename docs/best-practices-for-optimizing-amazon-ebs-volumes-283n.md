# 优化 Amazon EBS 卷的最佳实践

> 原文：<https://dev.to/eddiesegal/best-practices-for-optimizing-amazon-ebs-volumes-283n>

[![](img/139f4f28be0b192143309a5c14205770.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V1kjUhid--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ovb2yapsp11vxabkqfob.jpg)

<figure>

<figcaption>

<figure>[flick](https://www.flickr.com/photos/encountered_ontheroad/34241964116/in/photolist-UaR1XE-TZx1E7-otgjTC-otgZCS-25xaaib-25dokua-8dNNus-ouHB71-otszYU-odRzU5-odXDc6-arhthY-odbwBT-oe1uFx-XoDFgJ-LizBiY-2bQPkyp-7pzEuJ-2bHQMY8-ZMm1sw-Ys3wm4-oH1aEe-oeHj4D-wP3ExV-L2Rrmf-xyQ2Wt-x6UCtV-wjN2bv-s6YH4A-2bHQNut-oy35fV-MRpdK6-LatBWN-2ar4akB-E5tiqR-owgWPA-Y4n5zK-sMaqNL-x4zgtj-oeesdk-ovVPyy-ow9iHN-28Rawjf-xgPrM3-ovUgkN-KwvUaH-LmneLe-uy3fVV-2fYxkyM-oubHpL)

<figcaption></figcaption>

</figure>

</figcaption>

</figure>

亚马逊 EBS (Elastic Block Store)是亚马逊网络服务(AWS)提供的存储服务。EBS 中的每个存储设备称为一个卷。AWS 客户需要熟悉 Amazon EBS 卷，以便以最有效的方式将其数据存储在 AWS 云上。在本文中，我将解释什么是 Amazon EBS 卷，以及如何优化它们的成本和性能。

什么是 EBS 卷？
亚马逊 EBS 卷是一个块级存储设备，设计用于[亚马逊 EC2 实例](https://aws.amazon.com/ec2/)。将卷附加到实例后，您可以像使用常规物理硬盘驱动器一样使用它。您可以使用特定的文件系统、主机操作系统和应用程序对其进行格式化，并根据它们制作快照。

## 使用 EBS 卷的好处

EBS 卷具有以下优势:

*   **数据可用性**—每个 Amazon EBS 卷都自动复制到其他存储设备，以防止数据丢失并保持高可用性。
*   **数据持久性**—Amazon EBS 卷独立于它所连接的实例而存在。只要数据存在，您就要继续为卷的使用付费。当实例终止时，连接到正在运行的实例的 EBS 卷可以自动与实例分离，其数据保持不变，并且该卷可以重新连接到新的实例。这使您能够无限期地处理和存储卷上的数据，同时仅在需要时使用处理和存储资源。
*   **数据加密**—您可以使用 Amazon EBS 加密功能创建加密的 EBS 卷。所有 EBS 卷类型都支持加密。您可以使用加密的 EBS 卷来满足各种静态数据加密要求。加密发生在托管 EC2 实例的服务器上，为从 EC2 实例传输到 Amazon EBS 存储的数据提供加密。
*   **快照** —Amazon EBS 提供创建任何 EBS 卷的快照并写入数据副本的能力。不需要将卷连接到正在运行的实例来拍摄快照。当您继续向卷中写入数据时，可以定期创建卷的快照，用作新卷的基准。加密 EBS 卷的快照会自动加密。
*   **灵活性** —EBS 卷在使用时支持实时配置更改。您可以在不中断服务的情况下修改卷类型、卷大小和 IOPS(每秒 I/O)容量。##优化 Amazon EBS 卷的最佳实践 Amazon EBS 可能非常昂贵，具体取决于您选择使用的卷类型。您应该考虑以下四个标准来优化 Amazon EBS volumes 的成本和性能:
*   磁盘空间
*   磁盘延迟
*   每秒 I/O 的峰值请求率(IOPS)
*   最大排出量

以下最佳实践将帮助您管理 Amazon [EBS volumes](https://cloud.netapp.com/blog/ebs-volumes-5-lesser-known-functions) 的成本，而不会牺牲性能:

**1。了解与 EBS 卷**
相关的成本为了优化 EBS 卷，您应该熟悉它们的各种大小和类型。

*   **通用 SSD(GP2)**━通用卷类型。低延迟的高成本固态硬盘存储。
*   **调配 IOPS SSD(io1)**━低延迟卷和调配的 I/O 性能。这是成本最高的固态硬盘存储。
*   **吞吐量优化的硬盘(st1)**━成本更低的磁卷，针对大量顺序数据传输进行了优化。
*   **冷硬盘(SC1)**━成本最低、延迟更高的磁卷。
*   **磁性**━延迟高，I/O 性能较低。

通过将卷类型与其将接收的最大负载和所需的最大延迟相匹配，您可以节省成本。默认情况下，AWS 会提供非常快速的高成本卷。许多 I/O 需求可以用低成本的磁驱动器来满足。将不太重要的卷转移到磁存储中可以节省大量成本。昂贵的 io1 卷可以替换为高性能的 gp2 卷，其大小可以满足应用程序所需的实际 I/O。

**2。评估 PIOPS 使用情况**
过度调配 PIOPS(调配 IOPS)会导致不必要的成本。有时不清楚哪里的最大读或写操作从未接近 PIOPS。要获得 io1 卷的 I/O 的最佳度量，请使用 [DiskReadOps](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html) 和 [DiskWriteOps](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html) 度量。这是 AWS 对 io1 性能最保守的衡量标准。消耗的读/写操作结合了读取和写入，并补偿了较大的读取和写入。使用消耗的读/写操作，您可以很好地了解 I/O 的实际需求，并选择适当的卷类型。

**3。寻找迁移到磁卷**
的候选者 Sc1 和 st1 比 gp2 便宜得多。它们在许多 I/O 不密集或不要求延迟少于 1 毫秒的应用中表现良好。高吞吐量的卷是 st1 的理想候选卷。要评估从 gp2 到 sc1 或 st1 的过渡，建议长时间运行容量指标报告，以估计平均 I/O 水平。

**4。终止分离和闲置的卷**
AWS 按照 GB/小时的费率对卷收费，无论它们是否连接到您的亚马逊云服务，也无论它们是否被访问。通过监控未连接和空闲的卷，您可以避免购买不必要的驱动器。

## 总结起来

优化 EBS 卷可以显著节约成本。高成本的 io1 卷通常可以迁移到 gp2。Gp2 卷通常可以迁移到磁性存储，从而节省大量成本。Amazon EBS Volumes 用户应该了解如何选择最适合预算和所需性能的最佳卷。