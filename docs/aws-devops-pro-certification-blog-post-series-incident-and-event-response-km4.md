# AWS DevOps Pro 认证博客文章系列:事故和事件响应

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-incident-and-event-response-km4>

胡振摄于 [Unsplash](https://unsplash.com/photos/Xruf17OrkwM)

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   解决问题并确定如何恢复操作
*   确定如何自动化事件管理和警报
*   应用实施自动修复所需的概念
*   应用设置事件驱动的自动化操作所需的概念

该领域占考试总分数的 18%。

## 有哪些相关的白皮书？

根据针对安全性和合规性的 [AWS 白皮书](https://aws.amazon.com/whitepapers/#security),我们应该查看以下文档:

*   [AWS 安全事件响应(2019 年 5 月)](https://d1.awsstatic.com/whitepapers/aws_security_incident_response.pdf)
*   [AWS 安全最佳实践(2016 年 8 月)](https://d1.awsstatic.com/whitepapers/Security/AWS_Security_Best_Practices.pdf)

产品页面推荐的其他白皮书:

*   [对 AWS 账户活动的实时观察](https://aws.amazon.com/answers/account-management/real-time-insights-account-activity/)
*   [利用 Kinesis 数据分析进行实时网络分析](https://aws.amazon.com/answers/web-applications/real-time-web-analytics-with-kinesis/)
*   [采用亚马逊 Kinesis 的 AWS 上的流数据解决方案(2017 年 7 月)](https://d1.awsstatic.com/whitepapers/whitepaper-streaming-data-solutions-on-aws-with-amazon-kinesis.pdf)

## 该领域涵盖哪些服务和产品？

*   [亚马逊守卫职责](https://aws.amazon.com/guardduty/) -线程检测
*   [亚马逊检查员](https://aws.amazon.com/inspector/) -安全方面的最佳实践
*   亚马逊 Kinesis 为每个子产品提供了单独的页面
    *   [Kinesis 视频流](https://aws.amazon.com/kinesis/video-streams/)允许您安全地从连接的设备上流式传输视频，用于分析、机器学习和其他形式的处理。
    *   [Kinesis 数据流](https://aws.amazon.com/kinesis/data-streams/)是可扩展且持久的实时数据流服务
    *   [Kinesis Data Firehose](https://aws.amazon.com/kinesis/data-firehose/) 允许您捕获、转换数据流并将其加载到 AWS 数据存储(S3)中，同时允许您使用现有的商业智能工具执行近实时分析
    *   [Kinesis 数据分析](https://aws.amazon.com/kinesis/data-analytics/)是下一层，当你已经超越了 CloudWatch 的分析能力时。允许您使用 SQL 或 Java 实时处理数据流。

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。

*   [亚马逊护卫舰](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
*   [亚马逊检查员](https://docs.aws.amazon.com/inspector/latest/userguide/inspector_introduction.html)
*   亚马逊 Kinesis
    *   [Kinesis 视频流](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)
    *   [Kinesis 数据流](http://docs.aws.amazon.com/kinesis/latest/dev/introduction.html)
    *   [Kinesis 数据消防软管](https://docs.aws.amazon.com/firehose/latest/dev/)
    *   [Kinesis 数据分析](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html)

或者，使用常见问题来熟悉这些服务:

*   [亚马逊护卫舰](https://aws.amazon.com/guardduty/faqs/)
*   [亚马逊检查员](https://aws.amazon.com/inspector/faqs/)
*   亚马逊 Kinesis
    *   [Kinesis 视频流](https://aws.amazon.com/kinesis/video-streams/faqs/?nc=sn&loc=5)
    *   [Kinesis 数据流](https://aws.amazon.com/kinesis/data-streams/faqs/)
    *   [Kinesis 数据消防软管](https://aws.amazon.com/kinesis/data-firehose/faqs/)
    *   [Kinesis 数据分析](https://aws.amazon.com/kinesis/data-analytics/faqs/)

你们都应该知道 API

*   [亚马逊护卫舰](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_api_ref.html)
*   [亚马逊检查员](https://docs.aws.amazon.com/inspector/latest/APIReference/Welcome.html)
*   亚马逊 Kinesis
    *   [Kinesis 视频流](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/API_Reference.html)
    *   [Kinesis 数据流](http://docs.aws.amazon.com/kinesis/latest/APIReference/Welcome.html)
    *   [Kinesis 数据消防软管](http://docs.aws.amazon.com/firehose/latest/APIReference/Welcome.html)
    *   [Kinesis 数据分析](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_Reference.html)

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [亚马逊护卫舰](https://docs.aws.amazon.com/cli/latest/reference/guardduty/index.html)
*   [亚马逊检查员](https://docs.aws.amazon.com/cli/latest/reference/inspector/index.html)
*   亚马逊 Kinesis
    *   Kinesis 视频流有 3 个不同的子命令:[kinesis-视频-存档-媒体](https://docs.aws.amazon.com/cli/latest/reference/kinesis-video-archived-media/index.html)、[kinesis-视频-媒体](https://docs.aws.amazon.com/cli/latest/reference/kinesis-video-media/index.html)、 [kinesisvideo](https://docs.aws.amazon.com/cli/latest/reference/kinesisvideo/index.html)
    *   Kinesis 数据流没有 CLI 子命令
    *   Kinesis 数据消防软管没有 CLI 子命令
    *   [Kinesis 数据分析](https://docs.aws.amazon.com/cli/latest/reference/kinesisanalytics/index.html)

与 API 一样，命令也有模式。

## 请放下武器。你有 20 秒时间服从。

引用:ED-209，机械战警(1987)

这是另一个领域，可能需要了解产品而不是实际使用(特别是在 Kinesis 套件的情况下)。正如我们将看到的，如果您的公司没有信息安全功能，Amazon GuardDuty 和 Amazon Inspector 确实可以立即用于中小型企业。

正如本节的标题所示，这是一个专注于如何响应事故和事件的领域。事故通常与安全环境有关。而事件通常与数据流相关。

我将在这篇文章中介绍这个领域的所有产品，因为这是一个相对较小的领域，很少有机会提供使用 AWS CLI 的实际操作经验。

## 什么？

**Amazon GuardDuty** 是一项威胁检测服务，它分析由您的帐户生成的元数据以及在 AWS CloudTrail 事件、亚马逊 VPC 流量日志和 DNS 日志中发现的网络活动。它可以检测:

*   侦测-端口扫描、登录失败、异常 API 活动和来自已知不良 IP 的访问。
*   实例危害-恶意软件、出站拒绝服务活动、异常高的流量、来自已知不良 IP 的活动、通过 DNS 的数据泄漏
*   帐户受损-试图禁用 AWS CloudTrail 日志记录、异常实例或基础架构启动(想想:用于加密货币挖掘的 GPU 实例类型)、资源在正常使用之外的区域旋转、来自已知不良 IP 的活动。
*   守卫职责产生的调查结果的数据保留期为 90 天。

Amazon Inspector 是另一种自动化安全评估服务，就像 GuardDuty 一样，但它不是识别您的帐户或网络活动的问题，而是主要关注您的 EC2 实例。它具有内置规则(由 AWS 安全研究人员创建和维护),用于检查从互联网对您的实例的访问、启用的远程 root 登录或安装的易受攻击的软件版本。

**亚马逊 Kinesis** 是一套产品:

*   **Kinesis 视频流**接受来自设备的视频流，并允许你执行分析、机器学习(亚马逊 Rekognition Video 和亚马逊 SageMaker)和其他形式的处理。
*   **Kinesis Data Streams** 是一种可扩展且持久的实时数据流服务，可以从数十万个来源捕获大量数据(每秒数千兆字节的数据)。然后，您可以使用 Kinesis Data Analytics、Spark 或在 EC2 实例(API)或 AWS Lambda 上运行的代码来处理这些数据。
*   **Kinesis Data Firehose** 允许您将大量的流数据加载到数据存储(S3、亚马逊红移、亚马逊弹性搜索和一个名为 Splunk 的第三方工具)中进行分析。
*   **Kinesis 数据分析**可以从 Kinesis Data Firehose 和 Kinesis 数据流中获取数据流，并使用 SQL 或 Java 对数据执行查询。

## 为什么？

**Amazon GuardDuty** 提供全面的威胁检测服务，虽然它确实通过仪表板提供报告，但响应工作流程确实需要更多工作，即您需要围绕事件(CloudWatch)编写工具(AWS Lambda)。这可能就是为什么亚马逊正在兜售能够帮助实现这些工作流程的[合作伙伴](https://aws.amazon.com/guardduty/resources/partners/)的原因。

与 GuardDuty 一样，Amazon Inspector 为您的 EC2 实例提供了一个全面的安全评估工具。

亚马逊 Kinesis 套件提供可以处理大量数据的托管服务。当你运行你自己的 [Apache Kafka](https://kafka.apache.org/) 环境时，你将需要一个大团队来支持它的设置和持续维护。花絮:亚马逊还为阿帕奇卡夫卡提供一项托管服务，名为[亚马逊为阿帕奇卡夫卡托管流媒体](https://aws.amazon.com/msk/)。Kinesis 存在的关键是它拥有视频和大容量数据流的专业工具。由于紧密集成到 AWS 数据服务中，Kinesis Data FireHose 可以通过最少的配置将大量数据加载到其服务中。Kinesis Data Analytics 可以用于对这些数据流进行分析，或者当你已经超越了 CloudWatch 之类的日志分析功能时。最后，该套件中的所有这些产品都可以一起使用或单独使用，尽管 Kinesis 数据分析确实需要来自 Kinesis Data FireHouse 或 Kinesis 数据流

## 什么时候？

当您不想管理自己的威胁检测和响应系统时，应该使用 Amazon GuardDuty 。要自己做到这一点，你需要使用像 [Snort](https://www.snort.org/) 或 [Tripwire](https://github.com/Tripwire/tripwire-open-source) 这样的工具，并订阅像 [Proofpoint](http://www.proofpoint.com/) 和 [CrowdStrike](https://www.crowdstrike.com/) 这样的第三方威胁情报源。

Amazon Inspector 是 Amazon GuardDuty 的补充工具，所以如果你正在使用它(Amazon GuardDuty)，你应该使用 Amazon Inspector。

**亚马逊 Kinesis** 的一些使用案例有:

*   捕捉汽车的实时视频流，识别车牌，然后采取行动。
*   将批量加载(以及随后的隔夜报告)转变为实时分析。
*   从车队物联网设备收集遥测数据，并使用 AWS Lambda 等其他 AWS 服务对数据做出反应。

## 如何？

**亚马逊 GuardDuty** 提供 [30 天免费试用](https://aws.amazon.com/guardduty/pricing/)，之后你按事件量(CloudTrail)和数据量(亚马逊 VPC 流量日志和 DNS 日志)收费。在 AWS 控制台的服务页面中，您需要注意的关键事项(也构成导航菜单)是:

*   调查结果是警卫发现的潜在安全问题。
*   设置在以下位置:
    *   定义了服务的权限
    *   设置 GuardDuty 向 CloudWatch 发送事件的间隔
    *   用样本调查结果预载警戒任务
    *   暂停或禁用守卫职责
*   列表是列表管理页面，您可以在其中上传可信 IP 列表或威胁列表(恶意 IP 地址)。每个地区每个 AWS 帐户最多可以上传 6 个威胁列表。
*   帐户-管理服务中的主(管理员)和成员(用户)帐户
*   新功能公告(您也可以通过 [SNS 主题](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_sns.html)订阅)
*   免费试用-你还有多少时间

**亚马逊检查员**为前 250 个实例评估提供 [90 天免费试用](https://aws.amazon.com/inspector/pricing/)。需要注意的关键事项是:

*   代理——可以分析你的 EC2 实例而不需要使用它的[代理](https://docs.aws.amazon.com/inspector/latest/userguide/inspector_agents.html),但是你将只限于[网络可达性](https://docs.aws.amazon.com/inspector/latest/userguide/inspector_network-reachability.html)规则。
*   评估目标是使用 AWS 标记组合在一起的 EC2 实例的集合。
*   评估模板
*   发现
*   规则包有两类:网络和主机评估。
    *   重要提示:如果没有安装代理，您只能运行网络评估规则。
    *   这些类别中的规则是
        *   网络评估
        *   网络可达性
        *   主机评估
        *   常见漏洞和暴露
        *   互联网安全中心(CIS)基准
        *   Amazon Inspector 的安全最佳实践
        *   运行时行为分析
*   遥测是 Amazon Inspector 在评估运行时从 EC2 实例收集的数据(行为、配置等)。

亚马逊 Kinesis 确实需要开发才能使用这些服务。提供的示例需要相当多的设置。如果您能够像使用 Amazon GuardDuty 或 Amazon Inspector 一样预先加载样本数据，那就太好了。

Unsplash path(我用什么术语来获得封面图片):事件，紧急情况

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*