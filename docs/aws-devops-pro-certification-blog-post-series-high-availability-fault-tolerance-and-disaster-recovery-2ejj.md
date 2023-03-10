# AWS DevOps Pro 认证博文系列:高可用性、容错和灾难恢复

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-high-availability-fault-tolerance-and-disaster-recovery-2ejj>

Emiel Molenaar 在 [Unsplash](https://unsplash.com/photos/JOrUKpuMOeU) 拍摄的照片

## 考试指南怎么说？

要通过此域，您需要了解以下内容:

*   确定多 AZ 与多区域架构的适当使用
*   确定如何实现高可用性、可伸缩性和容错
*   基于业务需求(例如，RTO/RPO、成本)确定合适的服务
*   确定如何设计和自动化灾难恢复策略
*   评估部署中的故障点

该领域占考试总分数的 16%。

## 有哪些相关的白皮书？

根据 [AWS 白皮书](https://aws.amazon.com/whitepapers)页面，我们应该查看以下文档:

*   [使用 AWS 的备份和恢复方法(2016 年 6 月)](https://d1.awsstatic.com/whitepapers/Storage/Backup_and_Recovery_Approaches_Using_AWS.pdf)
*   [在 AWS 上构建容错应用(2011 年 10 月)](https://d1.awsstatic.com/whitepapers/aws-building-fault-tolerant-applications.pdf)

## 该领域涵盖哪些服务和产品？

*   [AWS 单点登录](https://aws.amazon.com/single-sign-on/)是亚马逊的托管单点登录服务，允许您的用户使用现有的微软活动目录(AD)登录 AWS 和其他连接的服务。
*   [亚马逊 CloudFront](https://aws.amazon.com/cloudfront/) 是一项托管内容交付网络(CDN)服务。
*   自动缩放资源——亚马逊有两种产品[亚马逊自动缩放](https://aws.amazon.com/autoscaling/)和[亚马逊 EC2 自动缩放](https://aws.amazon.com/ec2/autoscaling/)
*   [Amzon Route 53](https://aws.amazon.com/route53/) 是托管域名服务(DNS)。
*   数据库
    *   [Amazon RDS](https://aws.amazon.com/rds/) 是一种托管关系数据库服务，有大量引擎可供选择:Amazon Aurora、PostgreSQL、MySQL、MariaDB、Oracle 数据库和 SQL Server。
    *   [Amazon Aurora](https://aws.amazon.com/rds/aurora/) 是 RDS 产品的一部分，但它的独特之处在于，它提供了与 MySQL 和 PostgreSQL 引擎的兼容性，同时性能也大大优于它们(MySQL 的 5 倍，PostgreSQL 的 3 倍)。
    *   [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) 是一个托管的 NoSQL(非关系)数据库服务，可用于存储键值对或基于文档的记录。

## 其他类型的文档呢？

如果你有时间，一定要阅读用户指南，但是它们通常有几百页。

*   亚马逊单点登录
*   [亚马逊 CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/index.html)
*   [亚马逊自动缩放](https://docs.aws.amazon.com/autoscaling/plans/userguide/)和[亚马逊 EC2 自动缩放](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/index.html)
*   [亚马逊路线 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/index.html)
*   数据库
    *   [亚马逊 RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/index.html)
    *   [亚马逊极光](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/index.html)
    *   [亚马逊 DynamoDB](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)

或者，使用常见问题来熟悉这些服务:

*   [亚马逊单点登录](https://aws.amazon.com/single-sign-on/faqs/)
*   [亚马逊 CloudFront](https://aws.amazon.com/cloudfront/faqs/?nc=sn&loc=6)
*   [亚马逊自动缩放](https://aws.amazon.com/autoscaling/faqs/)和[亚马逊 EC2 自动缩放](https://aws.amazon.com/ec2/autoscaling/faqs/)
*   [亚马逊路线 53](https://aws.amazon.com/route53/faqs/)
*   数据库
    *   [亚马逊 RDS](https://aws.amazon.com/rds/faqs/)
    *   [亚马逊极光](https://aws.amazon.com/rds/aurora/faqs/)
    *   [亚马逊 DynamoDB](https://aws.amazon.com/dynamodb/faqs/)

你们都应该知道 API

*   [亚马逊 CloudFront](https://docs.aws.amazon.com/cloudfront/latest/APIReference/Welcome.html)
*   [亚马逊自动缩放](https://docs.aws.amazon.com/autoscaling/plans/APIReference/)和[亚马逊 EC2 自动缩放](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/index.html)
*   [亚马逊路线 53](https://docs.aws.amazon.com/Route53/latest/APIReference/index.html)
*   数据库
    *   [亚马逊 RDS](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/index.html)
    *   亚马逊 Aurora 使用与 RDS 相同的 API
    *   [亚马逊 DynamoDB](http://docs.aws.amazon.com/amazondynamodb/latest/APIReference/)

在恐慌之前，您将开始发现 API 动词的一种模式。

和 CLI 命令

*   [亚马逊 CloudFront](https://docs.aws.amazon.com/cli/latest/reference/cloudfront/index.html)
*   [亚马逊自动缩放](https://docs.aws.amazon.com/cli/latest/reference/autoscaling-plans/index.html)和[亚马逊 EC2 自动缩放](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/index.html)
*   Amazon Route53 有三个子命令: [DNS 和健康检查](https://docs.aws.amazon.com/cli/latest/reference/route53/index.html)、[服务发现](https://docs.aws.amazon.com/cli/latest/reference/servicediscovery/index.html)和[域名注册](https://docs.aws.amazon.com/cli/latest/reference/route53domains/index.html)
*   数据库
    *   [亚马逊 RDS](https://docs.aws.amazon.com/cli/latest/reference/rds/index.html)
    *   亚马逊 Aurora 使用与 RDS 相同的 CLI
    *   亚马逊 DynamoDB 有两个子命令: [dynamodb](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html) 和 [dynamodbstreams](https://docs.aws.amazon.com/cli/latest/reference/dynamodbstreams/index.html)

与 API 一样，命令也有模式。

## 高可用性、容错和灾难恢复，天啊！

让我们抛开基础知识，讨论这个领域的核心概念。

我将使用 Patrick Benson 在他的博客文章中提供的一个很好的例子:[容错、高可用性、&灾难恢复的区别](http://www.pbenson.net/2014/02/the-difference-between-fault-tolerance-high-availability-disaster-recovery/)

一架飞机有多个发动机，可以在失去一个或多个发动机的情况下工作。飞机的设计使其能够承受因引擎故障而从空中坠落的风险。这种设计是**容错**。

就基础设施而言，这可能是一种类似 RDS 的托管服务，其中数据库引擎拥有多个磁盘和 CPU 来应对灾难性故障。

然而，汽车中的备用轮胎是不容错的，也就是说，你必须停止更换轮胎，但首先有备用轮胎使汽车仍然**高度可用**。就基础设施而言，任何类型的技术，如自动扩展组。

一个解决方案实现一个容错(弹性)和高度可用(可伸缩)的系统是很常见的。

最后，战斗机上的弹射座椅是灾难恢复措施。目标是在所有其他措施(容错和 HA)都失败后保留试点，或者在我们的情况下，保留服务。

通常在基础架构方面，这可能是一个备用基础架构或数据库副本，位于不同的 AWS 区域，并使用 Route 53 指向袖手旁观基础架构。虽然手动灾难恢复策略仍然很常见，但对于该领域，我们有望提供自动化解决方案。

Unsplash path(我用什么术语来获得封面图片):飞机

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*