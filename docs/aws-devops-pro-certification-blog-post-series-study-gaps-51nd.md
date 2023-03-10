# AWS DevOps Pro 认证博文系列:研究差距

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-study-gaps-51nd>

Suad Kamardeen 在 [Unsplash](https://unsplash.com/photos/MYKAZlzW6Nw) 拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 研究空白

这一部分将会有很大的变化，因为我在模拟考试中发现了新的空白。

我尝试了考试条件下的[样本考试](https://d1.awsstatic.com/training-and-certification/docs-devops-pro/AWS%20Certified%20DevOps%20Engineer%20-%20Professional_Sample%20Questions.pdf)(在 AWS 对考试进行适应性调整之前，每个问题大约需要 2 分钟)。以下是我需要填补的一些空白:

### 通用

*   了解哪些服务能够使用[基于资源的策略](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html):
    *   Lambda ( [配置管理和基础设施作为代码](https://dev.to/2019/aws-devops-pro-certification-configuration-management-and-infrastructure-as-code-intro))。
    *   ECR(通过 ECS - [配置管理和基础设施作为代码](https://dev.to/2019/aws-devops-pro-certification-configuration-management-and-infrastructure-as-code-intro))。
    *   CloudWatch 日志([监控和记录](https://dev.to/2019/aws-devops-pro-certification-monitoring-and-logging))。
    *   AWS Secrets Manager ( [策略和标准自动化](https://dev.to/2019/aws-devops-pro-certification-policy-standards-automation/))。

### SDLC 自动化

*   需要阅读[蓝/绿](https://d1.awsstatic.com/whitepapers/AWS_Blue_Green_Deployments.pdf)白皮书( [SLDC 自动化](https://dev.to/2019/aws-devops-pro-certification-sdlc-intro/))。Pssst 如果你有时间，你应该阅读所有的 [DevOps](https://aws.amazon.com/whitepapers/) 相关白皮书！

#### 蓝/绿技术使用云形成或手动供应，即通过 AWS 控制台

这是基于[蓝/绿](https://d1.awsstatic.com/whitepapers/AWS_Blue_Green_Deployments.pdf)白皮书。

*   用 Amazon Route 53 更新 DNS 路由
    *   设置
    *   53 号公路域名系统
    *   蓝色/绿色环境
        *   弹性负载平衡器(ELB)
        *   ELB 背后的自动缩放组
        *   两种环境都指向同一个数据库实例(Amazon RDS Multi-AZ)
    *   子模式
    *   **经典 DNS 模式** -将别名(实时)记录从蓝色翻转为绿色
    *   **经典 DNS 加权分发** -使用拆分将流量发送到不同的环境
*   交换弹性负载平衡器后面的自动扩展组
    *   设置
    *   53 号公路域名系统
    *   ELB 指着
    *   蓝色和绿色自动缩放组
    *   两个 ASG 指向同一个数据库实例(Amazon RDS Multi-AZ)
*   更新自动扩展组启动配置
    *   设置
    *   53 号公路域名系统
    *   ELB 指着
    *   自动缩放组包含
        *   蓝色发射配置(LC)
        *   绿色启动配置(LC)
    *   LC 指向 Amazon DynamoDB、Amazon RDS Multi-AZ 或 Amazon ElastiCache

有 OpsWorks 和弹性豆茎的模式，如果我有时间会添加。

### 配置管理和基础设施作为代码

*   希腊字母的第 11 个
    *   部署新版本
    *   有哪些可用的触发器
    *   API 网关
    *   AWS 物联网
    *   应用负载平衡器
    *   CloudWatch 事件([监控和记录](https://dev.to/2019/aws-devops-pro-certification-monitoring-and-logging))
    *   CloudWatch 日志([监控和记录](https://dev.to/2019/aws-devops-pro-certification-monitoring-and-logging))
    *   代码提交( [SLDC 自动化](https://dev.to/2019/aws-devops-pro-certification-sdlc-intro/))
    *   认知同步触发器
    *   DynamoDB ( [高可用性、容错和灾难恢复](https://dev.to/2019/aws-devops-pro-certification-high-availability-fault-tolerance-disaster-recover/))
    *   Kinesis ( [事件和事件响应](https://dev.to/2019/aws-devops-pro-certification-incident-and-event-response/))
    *   知道以下服务被支持也没有坏处:S3，SNS 和 SQS

### 监控和记录

*   考试中涉及的[服务](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/EventTypes.html)的 CloudWatch 事件
    *   SDLC 自动化
        *   代码提交
        *   代码构建
        *   代码部署
        *   代码管道
    *   作为代码的配置管理和基础设施
        *   AWS 配置
        *   aws opsworks
        *   AWS (Lambda)阶跃函数
        *   AWS ECS
    *   监控和记录
        *   云观察(预定事件)
    *   策略和标准自动化
        *   你有亚马逊
        *   AWS 系统经理
            *   配置合规性
            *   维护窗口
            *   参数存储
        *   值得信赖的顾问
    *   事故和事件报告
        *   亚马逊瓜德罗蒂
    *   容错、高可用性和灾难恢复
        *   亚马逊 EC2 自动缩放
*   云观察事件规则[目标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)
    *   SDLC 自动化
    *   代码构建
    *   代码管道
    *   作为代码的配置管理和基础设施
    *   λ(和阶跃)函数
    *   事故和事件报告
    *   由外界刺激引起的不随意运动
        *   数据流
        *   数据消防软管
    *   亚马逊检查员
    *   策略和标准自动化
    *   系统经理
        *   运行命令
        *   自动化
    *   很高兴知道:社交网络和 SQS

### 容错、高可用性和灾难恢复

*   无线电数据系统
    *   快照及其在灾难恢复情况下的使用。([高可用性、容错和灾难恢复](https://dev.to/2019/aws-devops-pro-certification-high-availability-fault-tolerance-disaster-recover/))。
    *   了解灾难恢复时的恢复时间目标(RTO)和恢复点目标(RPO)。([高可用性、容错和灾难恢复](https://dev.to/2019/aws-devops-pro-certification-high-availability-fault-tolerance-disaster-recover/))。

### 政策和标准自动化

*   AWS 系统管理器- EC2 补丁程序组和补丁程序管理器的基准([策略和标准自动化](https://dev.to/2019/aws-devops-pro-certification-policy-standards-automation/))
*   AWS 服务目录——如何提供提供不同层(web、web + db)或堆栈的产品。NET 或 Ruby) ( [策略和标准自动化](https://dev.to/2019/aws-devops-pro-certification-policy-standards-automation/))

Unsplash 路径(我使用什么术语来获得封面图像):gap

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*