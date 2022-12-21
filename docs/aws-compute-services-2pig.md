# AWS 计算服务

> 原文：<https://dev.to/vikashagrawal/aws-compute-services-2pig>

# EC2(弹性计算云)

它以虚拟机的形式提供基础架构。默认情况下，EC2 无权访问 S3，您需要定义角色并将角色附加到 EC2。
·EC2 根据地区和 S3 进行定义。表示 S3 上的任何命令，这些命令与 ec2 不在同一个区域，您可能需要传递额外的参数"- region "(如果 S3 与 EC2 在不同的区域，有时也会传递)-因此使用"- region"
是安全的，例如 AWS S3 CP–recursive S3:///home/EC2-user–region eu-west-2
。引导脚本是定义脚本的一种方式，可以在 EC2 实例运行期间使用:

```
#!/bin/bash
yum update –y
yum install httpd –y 
```

EC2 实例元数据:
curl[http://169 . 254 . 169 . 254/最新/元数据](http://169.254.169.254/latest/meta-data)，curl[http://169 . 254 . 169 . 254/最新/元数据/公共 ipv4](http://169.254.169.254/latest/meta-data/public-ipv4) 这可以用来模拟如下场景:

```
o Put this curl command while directing to a file as part of bootstrap script.
o Copy this file to s3 bucket.
o Have a lambda function defined with the trigger on this s3 bucket. 
```

EC2 实例用户数据:
curl[http://169 . 254 . 169 . 254/latest/user-Data](http://169.254.169.254/latest/user-data)如果主机没有问题，您的实例可以保留在同一台主机上。此外，如果实例是 EC2-Classic 实例，则其弹性 IP 地址将与该实例解除关联。否则，如果它是 EC2-VPC 实例，弹性 IP 地址将保持关联。
对于在 EC2-Classic 中启动的 EC2 实例，当实例停止或终止时，AWS 会释放私有 IPv4 地址。如果您重新启动已停止的实例，它会收到一个新的私有 IPv4 地址。
对于在 VPC 中启动的 EC2 实例，当实例停止并重新启动时，私有 IPv4 地址仍与网络接口相关联。只有当实例终止时，它才会被释放。
·定价，根据使用需求，定价如下:

```
o On Demand
▪ per hour or per second.
▪ Without any upfront payment
▪ Being developed and tested for first time.
▪ To handle unpredictable workload.
o Reserved
▪ Predictable workload
▪ User can make upfront payment to reduce cost.
▪ Types:
• Standard
• Convertible
• Scheduled
o Spot: bidding
▪ Applications with flexible start and end time
▪ AWS will shut it off when the peak time comes but if you are shutting it down, you will be charged for complete hour.
▪ Spot instances typically offer a significant discount off the On-Demand prices
▪ Your instances can be interrupted by Amazon EC2 for capacity requirements with a 2-minute notification
▪ Spot prices adjust gradually based on long term supply and demand for spare EC2 capacity.
▪ You can choose to have your Spot instances terminated, stopped, or hibernated upon interruption. Stop and hibernate options are available for persistent Spot requests and Spot Fleets with the maintain option enabled.
o Dedicated Host
▪ Use for regulatory requirement, where you need to have the dedicated host.
• Pricing:
o On demand
o Reserved
• Instance Type: FIGHT DR MC PX
o F FPGA
o I IOPS
o G GRAPHICS
o H HIGH THROUGHPUT
o T CHEAP GENERAL PURPOSE, T2
o D DENSITY
o R RAM
o M MAIN CHOICE FOR GENERAL PURPOSE
o C COMPUTE
o P PICS/GRAPHICS
o X EXTREME MEMORY 
```

状态检查
o 系统状态检查:检查它是否可以接收网络数据包，如果不能，则说明操作系统等底层基础设施有问题，如果重启不起作用，则可能需要技术支持。o 实例状态检查:它检查底层操作系统是否可以接受流量，如果不能，则重新引导可以纠正它。
监测
基本:每 5 分钟一次。
o 详细:每 1 分钟一次。
·终止保护在默认情况下是关闭的，应该打开它，以便当您要终止它时，它会首先要求禁用它。
·安全组:

```
o It’s a virtual firewall.
o Hence any changes to the security group reflects immediately.
o Any rule added to “Inbound” will be applicable to “outbound” as well if no rule is mentioned in “outbound” and hence SG is stateful.
o All outbound traffic is allowed by default.
o All inbound traffic is blocked by default.
o The traffic can be configured for allowed, not for disallowed.
o To block IP address, use Network Access Control List.
o As mentioned, when we create AWS account, it attaches a default VPC in each of the region and each VPC has its default Security Group.
o You can assign multiple SGs to single EC2 instance.
o You can have multiple EC2 instances attached to same SG.
o When connecting to your EC2 instance via SSH, you need to ensure that port 22 is allowed on the security group of your EC2 instance. 
```

# EBS

o 弹性块存储
o 如果 EC2 是虚拟服务器，则 EBS 是虚拟卷，因此 EBS 应该位于 EC2 所在的 AZ 中。
o EBS 是独立于 EC2 的实例，因此您可以选择是否不希望在 EC2 终止时删除 EBS，默认情况下它处于启用状态，这意味着 EBS(仅根卷)将在 EC2 终止时删除。
o EBS 默认 AMI 的根卷(来自亚马逊)无法加密，只能用第三方工具加密。另一种选择是，当我们从 EC2 实例创建 AMI 时，可以在 AMI 创建期间启用加密。
o 卷配置(如卷类型、大小)可以随时更改，但不能在不影响停机时间的情况下更改磁性卷。
o 创建快照、映像是将 EC2 实例迁移到任何地区和任何 AZ 的方法。
o 要为启动卷创建快照，应该停止启动卷，但这并不意味着不能这样做，这只是为了避免任何风险。o 加密卷的快照将自动加密。
o 只有未加密的卷才能与其他人甚至其他帐户共享。
快照存在于 S3 上，因为它不是卷。
o 快照是增量的，这意味着自上次快照以来发生更改的数据块将作为下一次快照的一部分进行复制。
o 可以从给定的卷创建快照。(快照表示备份)

```
o Once the snapshot is created,
▪ the volume can be created from the same in any AZ in the given region.
▪ The snapshot can be copied to any region.
▪ The image can be created in the same region. 
```

o 您可以使用亚马逊数据生命周期管理器(亚马逊 DLM)来自动创建、保留和删除用于备份您的亚马逊 EBS 卷的快照。

```
Automating snapshot management helps you to:
▪ Protect valuable data by enforcing a regular backup schedule.
▪ Retain backups as required by auditors or internal compliance.
▪ Reduce storage costs by deleting outdated backups.
▪ Combined with the monitoring features of Amazon CloudWatch Events and AWS CloudTrail, Amazon DLM provides a complete backup solution for EBS volumes at no additional cost.
▪ it is the fastest and cost-effective solution in providing an automated way of backing up your EBS volumes. 
```

o 类型

启动卷是 c 盘。
您不能将 1 个 EBS 卷挂载到 1 个以上的 EC2 实例，而是使用 EFS。
50:1 是调配的 IOPS 与请求的卷大小的最大比率，单位为吉字节(GiB)。例如，一个 10 GiB 的卷最多可以配置 500 IOPS。任何 640 GiB 或更大的卷都允许配置最大 32，000 IOPS(50×640 GiB = 32，000)。
RAID:独立磁盘冗余阵列，如果需要良好的 IO 操作，那么创建多个卷，放入 RAID。
o RAID 0:由 2 个或更多磁盘组成的阵列，卷在各磁盘间被剥离，因此，如果 1 个磁盘出现故障，则整个 RAID 会出现故障，因此没有冗余。性能不错。
o RAID 1:由两个或更多磁盘组成的阵列，这些磁盘互为镜像，因此具有冗余性。o RAID 5:适合读取，不适合写入。AWS 不建议这么做。o RAID 10:0 和 1 的组合，因此被剥离和镜像，良好的冗余和性能。
远程桌面连接
如果 SG 为 RDP 定义了入站规则，任何 EC2 实例都可以通过远程桌面客户端进行连接。o 可以在 AWS 控制台中的给定 EC2 实例上生成 IP、用户名和密码。
o 如果在创建 EC2 时添加了多个卷，在使用远程桌面连接启动 windows 实例后，可以将这些卷添加为 RAID 阵列。

# EBS vs 实例存储

o EBS 支持的卷是持久的，实例存储支持的卷不是持久的。意味着 EBS 备份的卷独立于 EC2 实例，因此可以分离或重新连接。意味着实例存储只存在于 EC2 实例的生命周期中。意味着无法停止实例存储卷，如果您这样做，数据将被清除。意味着 EBS 支持的卷是长期的。
o 基于实例存储的 EC2 允许最多添加一个实例存储(卷)。
o 创建基于实例存储的 EC2 后，您可以添加任意数量的基于 EBS 的卷。
基于 EC2 的 o 实例存储不能停止，可以重启或终止。这意味着如果底层主机出现故障，它将丢失数据。
o 实例存储卷称为临时存储。

# ELB(弹性负载平衡器)

o 类型:
应用负载平衡器
最适合 HTTP 和 HTTPS，位于第 7 层
如果您需要灵活的应用管理和 TLS 终端，我们建议您使用应用负载平衡器。
网络负载平衡器
针对 TCP 流量，位于第 4 层
。如果您的应用需要极高的性能和静态 IP，我们建议您使用网络负载平衡器。
经典负载平衡器
这是一款传统负载平衡器，适用于第 7 层的 HTTP 和 HTTPS，适用于第 4 层的 TCP
如果您的应用构建在 EC2 经典网络中，那么您应该使用经典负载平衡器。
创建传统负载平衡器时，跨区域负载平衡的默认值取决于您创建负载平衡器的方式。使用 API 或 CLI 时，跨区域负载平衡在默认情况下是禁用的。使用 AWS 管理控制台，默认情况下会选择启用跨区域负载平衡的选项。创建经典负载平衡器后，您可以随时启用或禁用跨区域负载平衡。
您可以在 EC2-Classic 中设置您的负载平衡器，以便在单个可用性区域或多个可用性区域中的 EC2 实例之间分配传入的请求。首先，在您计划使用的所有可用性区域中启动 EC2 实例。接下来，向负载平衡器注册这些实例。最后，将可用性区域添加到负载平衡器中。添加可用性区域后，负载平衡器开始将请求路由到该可用性区域中的已注册实例。请注意，您可以随时修改负载平衡器的可用性区域。
默认情况下，负载均衡器在其可用性区域内均匀地路由请求。要在可用性区域中的注册实例之间均匀地路由请求，请启用跨区域负载平衡。它没有给出 IP 地址，亚马逊在内部维护它。

# EC2 放置组

o 类型
集群放置组:是同一 AZ 中的多个实例的分组
分散放置组:分别放置在不同底层硬件上的实例组
o PG 的名称在 AWS 帐户中应该是唯一的
o 放置组不能跨多个可用性区域部署。
o 在 PG 中只能启动特定类型的实例类型(计算优化、GPU、内存优化、存储优化)
o AWS 推荐 PG 中的同类实例
o 您不能合并 PG
o 您不能从 PG 中移动现有实例。您可以从给定的实例创建 AMI，然后启动另一个实例。

示例:
创建了一个 EC2 实例。
下载了 Word press 内容。
作为这个网站的一部分，我们也可以上传媒体，这将是同一个 EC2 实例。
定义 2 个 S3 存储桶，一个用于代码，另一个用于媒体。
将 cron 作业定义为将 WP 的代码部分内容同步到代码桶，并将另一个 cron 作业定义为将媒体内容同步到媒体桶。
·这样，我们可以确保在实例停机时，我们有备份。
定义云锋，并将其链接到媒体桶。
拥有。httpaccess 文件定义了将媒体访问重定向到云前端。
定义一个 ELB 来拥有这个 EC2 实例。
从这个 EC2 实例创建一个 AMI。
·将 ASG 组定义为具有来自该 AMI 的 3 个实例。
现在这 3 个实例将作为代码内容(网站托管)。
·ASG 将确保至少有 3 个实例处于活动状态。
修改 ELB 中的实例列表，删除较旧的实例，这些实例将充当媒体服务并重定向到媒体桶。
修改 ELB 的实例列表以添加 3 个实例，这些实例是从 ASG 创建的。

# EC2 集装箱服务

它提供了码头化。这是一种容器管理服务。

# 弹性豆茎

如果开发人员不想在编码时担心底层基础设施，这是最好的选择。
当您创建示例应用程序或上传任何 web 应用程序代码时，它会创建以下内容:
o EC2 实例
o SG
o ASG
o S3 桶
o 云观察
o 域名

它是任何代码部署的一个入口，您不必担心创建任何基础设施。

# 轻帆

创建基础设施时，如果工程师不想担心其他底层服务，如 RDB、固定 IP 地址等。

# λ

它提供 java 的执行环境，节点 js。对于每次执行，它都会创建单独的环境。
它可以自动横向扩展，因此您不必像 ELB 那样引入任何基础架构。

```
• Following triggers are available for lambda
o API Gateway
o AWS IoT
o Alexa Skill Kit
o Alexa Smart Home
o Cloud Front
o Cloud Watch Events
o Cloud Watch Logs
o Code Commit
o Cognito Sync Trigger
o Dynamo DB
o Kinesis
o S3
o SNS
o SQS 
```

```
• Languages Supports:
o Node Js
o Java
o Python
o .Net
o Go 
```

Lambda 是无服务器的，但 EC2 不是无服务器的。
·Lambda 执行最长时间为 5 分钟。
·Lambda 自动向外扩展(不是向上扩展)。

# 批量

在云中进行任何批量计算。