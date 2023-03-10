# 亚马逊基础设施服务:构建您的云计算预算

> 原文：<https://dev.to/eddiesegal/amazon-infrastructure-services-build-your-budget-for-the-cloud-1852>

[![](img/3807068e1d7b3dd4be801b5e3053ddf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IniPcCqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1j7bytxbt5vw3ejdq0w.jpeg) 

<figure>

<figcaption>图片来源:

<figure>[ctrl . blog](https://www.ctrl.blog/media/hero/particle-network.jpeg)

<figcaption></figcaption>

</figure>

</figcaption>

</figure>

亚马逊网络服务是最成熟的云提供商之一。AWS 提供各种服务，利用其广泛的虚拟化服务器网络。本简要指南涵盖了各种可用 AWS 体系结构的定价选项。

### 云锋定价

*   **将区域数据传输到互联网**—最初 10tb/月的价格为每 GB 0.085 美元，超过 5 PB /月的价格降至每 GB 0.02 美元
*   **用于将区域数据传输到原点**——每 GB 0.02 美元
*   **对于 HTTP 请求**——每 10，000 个请求 0.0075 美元### CloudWatch 定价
*   **对于亚马逊 CloudWatch 仪表盘**，每个仪表盘每月 3.00 美元
*   **对 Amazon EC2 实例**的详细监控，每月每个实例——从 2.10 美元开始，以 1 分钟的频率降至 0.14 美元
*   **对于自定义指标**，每月每指标—前 10，000 个指标 0.30 美元，超过 1，000，000 个指标则降至 0.02 美元
*   **对于报警**，每月每个报警——0.10 美元，高分辨率报警额外 0.20 美元
*   **对于 API 请求**——每请求 1，000 个指标或 1，000 个 API 请求收取 0.01 美元
*   **对于亚马逊 CloudWatch 日志**——每月摄入每 GB 0.50 美元，归档每 GB 0.03 美元
*   **对于出售的日志**，每 GB—开始收取前 10TB 出售日志数据的费用为 0.50 美元，超过 50TB 的费用为 0.05 美元
*   **对于事件**，每百万生成的自定义事件—1.00 美元###亚马逊弹性负载平衡(ELB)定价
*   **对于应用负载平衡器**——每个应用负载平衡器小时(或不足一小时)0.0225 美元，每个 LCU 小时(或不足一小时)0.008 美元
*   **对于网络负载平衡器**——每个网络负载平衡器每小时(或不足一小时)0.0225 美元，每个 LCU 小时(或不足一小时)0.006 美元
*   **对于经典负载平衡器**——每个网络负载平衡器 0.0225 美元-小时(或部分小时)$0.006 每个 LCU-小时(或部分小时)##亚马逊弹性容器服务(ECS)定价亚马逊的弹性容器服务可以通过亚马逊 EC2 或亚马逊 Fargate 运行。对于 EC2 任务，您可以使用绑定装载主机卷或 Docker 管理的卷，它们可以与外部 [AWS 存储系统(如 EBS](https://cloud.netapp.com/blog/ebs-volumes-5-lesser-known-functions) )很好地集成。我将使用 Fargate 部署作为 ECS 定价的示例。

AWS Fargate 定价是根据 vCPU 和内存资源计算的，从您开始下载容器映像(docker pull)开始，直到您的容器收集(称为任务)终止，最低收费为 1 分钟。

定价基于任务所需的 vCPU 和内存资源，两者均独立计算，并按秒计费:

*   **对于 vCPU** ，每小时 0.0506 美元
*   **对于 GB 内存**，每小时——每小时 0.0127 美元### Amazon Lambda 定价 Amazon 的无服务器计算服务每次开始执行以响应事件通知或 invoke 调用时都会计算一个请求。你需要为所有 [Lambda 函数](https://docs.aws.amazon.com/lambda/latest/dg/lambda-introduction-function.html)的请求总数付费。

持续时间是从代码开始执行的时间开始计算的，直到代码返回或终止。价格取决于分配给函数的内存量:

*   **对于请求**—前 100 万次请求免费，此后每 100 万次请求收费 0.20 美元
*   **持续时间** —每月 400，000 GB 秒免费，此后每 GB 秒 0.00001667 美元

## 结论

在这篇文章中，我介绍了亚马逊基础设施服务的定价方案:

*   亚马逊云锋
*   亚马逊云观察
*   亚马逊 ELB
*   亚马逊 ECS
*   亚马逊拉姆达

这将有助于您根据自己的需求和预算选择合适的架构。但是，此处显示的数字是估计值，可能会因您所在的位置而异，或者会随着时间而变化。要完全理解使用 AWS 基础设施的长期成本，您必须亲自试用。