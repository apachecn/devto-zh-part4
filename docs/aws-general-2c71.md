# AWS 常规

> 原文：<https://dev.to/vikashagrawal/aws-general-2c71>

# 地区

这只是地理名称。

# 可用性区域

它由一个或多个数据中心组成。

# 边缘

它是 AWS 的缓存交付形式(如云前端)，主要是媒体文件等。

每个区域至少包含 2 个 az。区域不同于边，边比区域多得多。Edge 是轻量级存储，当它第一次访问时，只有它才能获得内容，而且为了给用户提供流畅的操作，它应该更多。

# AWS 组织

这是一个帐户管理工具，用于维护多个 AWS 帐户(多个帐户负责组织中的不同团队),这些多个帐户称为关联帐户。这有助于给出合并账单，与单个团队的单个账单相比，合并账单更便宜。
·这也有助于共享资源，而不是让其他团队闲置。
它只是一个付费账户，并不用于部署资源。
它最多支持 20 个关联账户，但可以通过联系 AWS 来筹集。
可以在 AWS 帐户和区域级别启用云追踪，以便在根帐户级别启用云追踪:

```
o   Create the S3 bucket for root account.
o   Grant access for other AWS accounts.
o   Enable cloud trail for each of the AWS account and redirects the log to this S3 bucket. 
```

从上图可以看出，我们有以下优势:

```
o   Centrally managed policies across multiple AWS accounts.
o   Control access to AWS services.
 With the Service Control Policies control (SCP), you can deny access to DB and kinesis to HR team.
 SCP overrides IAM.
o   Automate AWS account creations and management.
o   Consolidated billing across multiple AWS accounts. 
```

# AWS 支持计划

它有以下计划:

```
o   Basic
o   Developer
o   Business
o   Enterprise 
```

所有客户都可以获得 AWS 帐户附带的基本支持。
所有计划，包括基本支持，提供

```
o   24x7 access to customer service
o   AWS documentation
o   Whitepapers
o   support forums. 
```

业务和企业计划提供对全套可信顾问检查的访问。