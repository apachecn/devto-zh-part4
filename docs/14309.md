# AWS 推出 PostgreSQL 无服务器服务

> 原文：<https://dev.to/levivm/aws-launches-aurora-postgresql-serverless-hfn>

亚马逊刚刚宣布了亚马逊 Aurora PostgreSQL 无服务器服务

数据库通常是软件架构中最关键的部分，管理数据库，尤其是关系数据库，从来都不容易。出于这个原因，我们创建了 Amazon Aurora Serverless，它是 Amazon Aurora 的自动扩展版本，可以根据您的应用程序工作负载自动启动、关闭和扩展。

您不需要固定的 RDS 实例或 EC2 实例，数据库服务器连接到自动扩展组。对于节约成本和扩展能力而言，这将是一个很好的改进。

因此，如果您有一个开发/qa 环境，您不需要让数据库一直运行，现在使用无服务器服务，它将按需收费，从而节省大量资金。

出于生产目的，您不再需要自己处理自动或手动缩放。处理交通高峰将像任何无服务器(按需)服务一样容易。

你可以在这里找到与[相关的一切](https://aws.amazon.com/blogs/aws/amazon-aurora-postgresql-serverless-now-generally-available/)