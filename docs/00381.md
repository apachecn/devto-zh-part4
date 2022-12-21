# AWS 管理工具

> 原文：<https://dev.to/vikashagrawal/aws-management-tool-4p1k>

# 云观察

它有助于在执行过程中捕获日志。
在 EC2 实例中，默认情况下有以下 4 个指标可用:

```
o   CPU
 CPU Credit Balance
 CPU Credit Usage
 CPU Surplus Credit Balance
 CPU Surplus Credits Charged
 CPU Utilization
o   Disk
 Disk Read Bytes
 Disk Read Ops
 Disk Write Bytes
 Disk Write Ops
o   Network
 Network in
 Network out
 Network packets in
 Network packets out
o   Status
 Status Check Failed
 Status Check Failed instance
 Status Check Failed system 
```

还可以创建自定义指标。
·事件允许您为 AWS 服务定义一种触发器，例如，EC2 实例一启动，我就允许 DNS 名称使用该 EC2 实例的公共 ip 地址，这样我们就可以管理流量。这里

```
o   Events of interest: EC2 instance
o   Rule: as soon as it comes up
o   Action: assign DNS name with public IP address. 
```

可以定义警报，根据定义的警报发送任何通知。
监控

```
o   Standard: 5 minutes
o   Detailed: 1 minute 
```

日志用于监视和汇总 AWS 服务的日志。

# 云的形成:

它帮助您开发自己的云，包括 ALB 等。它有助于您绘制类似设计图的东西，您可以在其中建立各种 AWS 服务组件之间的关系。
·结果可以是 json 和 yaml 的形式。
一旦实施，它将定义所有的基础设施，如 ELB、ASG、EC2 等。

# CloudTrail

它捕获 AWS 环境中发生的每一次操作，仅存储 1 周的记录。无非就是你在 AWS 工作的记录。
它是按 AWS 帐户和地区启用的。

# 配置

它提供了 AWs 环境的配置演示。

# OpsWorks

这是实现环境自动化的一种方式。您需要一个配置管理服务，使您的系统管理员能够使用 Chef 配置和操作您的 web 应用程序。
它使用厨师和厨师组成的食谱来保持一致的状态。

# 服务目录

它是关于您的组织向客户提供的服务的细节。

# 系统管理员

它主要管理 EC2，例如，如果您想要修补/取消修补大约 100 个 EC2 系统。
“AWS 系统管理器运行”命令使您能够远程安全地管理托管实例的配置。托管实例是混合环境中已经为 Systems Manager 配置的任何 Amazon EC2 实例或本地机器。
o Run Command 使您能够自动执行常见的管理任务，并执行大规模的临时配置更改。
o 您可以从 AWS 控制台、AWS 命令行界面、AWS Tools for Windows PowerShell 或 AWS SDKs 使用 Run 命令。
o 运行命令免费提供。

# 值得信赖的顾问

它建议打开所有端口，并让您知道是否要越过任何账单金额，它不同于 Inspector(Inspector 是什么？)
AWS 可信顾问分析您的 AWS 环境，并在以下五个类别中提供最佳实践建议:

```
o   Cost Optimization
o   Performance
o   Fault Tolerance
o   Security
o   Service Limits 
```

你可以用一种记忆法，比如 CPFSS，来记住这五个类别。

# 托管服务

它管理您的服务，无论是自动扩展组、ALB 等。