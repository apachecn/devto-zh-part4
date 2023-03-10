# AWS 数据库服务

> 原文：<https://dev.to/vikashagrawal/aws-database-services-303p>

```
• OLTP: Online Tx Processing, e.g. return a row with id is equal to something.
• OLAP: Online Analytics Processing, e.g. Sum of radios sold in the given region.
• Data ware housing is meant for analytics. 
```

# RDS(关系数据库服务)(OLTP)

AWS 中提供以下数据库:

```
o SQL Server
o Oracle
o MySQL, an open source
o Postgres
o Aurora
o MariaDB 
```

RDS 实例端点始终是 DNS 名称，而不是 IP 地址。每当两个服务之间有集成时，它也涉及 SGs 集成，这意味着第二个 SG 的入站规则应该有第一个 SG。
备份:

```
o Types
▪ Automated
• It is highly discouraged to disable the automated backups in Amazon RDS because it disables point-in-time recovery. Disabling automatic backups for a DB instance deletes all existing automated backups for the instance. If you disable and then re-enable automated backups, you are only able to restore starting from the time you re-enabled automated backups.
• It gets deleted after original DB is deleted.
• It allows you to recover database to any point in time with in “retention period”.
• The retention period can be between 1 and 35 days.
• It will take a full daily snapshot and also store the Tx logs throughout the day.
• While recovering, it chooses the most recent daily back up and then apply Tx logs relevant to that day.
• It's enabled by default.
• It's stored in S3.
• You get free storage space in S3 equal to that of the size of DB.
▪ Snapshot
• It's done manually.
• It’s stored even after original DB is deleted.
o The restored DB is always a new DB instance with the new DNS end point.
o Backup time also can be defined.
o During the back, the I/O operation will be slow. 
```

加密

```
o Encryption at rest is supported for MySQL, Oracle, SQL Server, PostgreSQL, MariaDB, Aurora
o Encryption is done using AWS Key Management Service.
o Once RDS instance is encrypted then it’s underlying data, it’s automated backups, snapshots and read replicas are also encrypted.
o Encrypting an existing RDS instance is not supported, to encrypt an existing RDS instance:
▪ Take a snapshot of DB.
▪ Make a copy of the snapshot.
▪ Encrypt this copy. 
```

多重 AZ

```
o It provides the stand by DB, where any write operations to primary DB gets reflected immediately to stand by DB.
o It's for Data recovery.
o It's available in following DB:
▪ SQL Server
▪ Oracle
▪ MySQL
▪ Postgres
▪ Aurora: by default, it’s available
▪ MariaDB
o Multi-AZ deployments for the MySQL, MariaDB, Oracle, and PostgreSQL engines utilize synchronous physical replication to keep data on the standby up-to-date with the primary.
o Multi-AZ deployments for the SQL Server engine use synchronous logical replication to achieve the same result, employing SQL Server-native Mirroring technology. Both approaches safeguard your data in the event of a DB Instance failure or loss of an Availability Zone.
o Failover is automatically handled by Amazon Aurora so that your applications can resume database operations as quickly as possible without manual administrative intervention.
o If you have an Amazon Aurora Replica in the same or a different Availability Zone, when failing over, Amazon Aurora flips the canonical name record (CNAME) for your DB Instance to point at the healthy replica, which in turn is promoted to become the new primary. Start-to-finish, failover typically completes within 30 seconds.
o If you do not have an Amazon Aurora Replica (i.e. single instance), Aurora will first attempt to create a new DB Instance in the same Availability Zone as the original instance. If unable to do so, Aurora will attempt to create a new DB Instance in a different Availability Zone. From start to finish, failover typically completes in under 15 minutes.
• Read Replicas:
o Read replicas is needed because the internet world is more of read operation and less of write operation.
o Whenever there is a write operation, it pushes the data to all read replicas.
o Read replicas can be present in different AZ or even different region too.
o It provides up to 5 read replicas per instance.
o Coping data is asynchronous operation.
o Used for scaling not DR as read replicas is read only.
o Each read replica has its own DNS point.
o Must have automatic backups on in order to deploy a read replica.
o It can be promoted to its own DB, but it breaks the replication.
o Following DB supports RR:
▪ MySQL
▪ PostgreSQL
▪ Amazon Aurora
▪ MariaDB 
```

将数据从主 RDS 实例复制到辅助 RDS 实例时，不收费。
拍摄数据库快照时 I/O 操作暂停
在 RDS 中使用多个可用性区域时，不能将辅助数据库用作独立的读取节点。
默认情况下，Oracle 和 MySQL RDS 实例上的最大调配 IOPS 容量(使用调配 IOPS)为 30，000 IOPS: False
在 RDS 中，当使用多个可用性区域时，您不能将辅助数据库用作独立的读取节点？

# 迪纳摩 DB

NoSQL
它既是文档又是键值数据模型。
存储在固态硬盘
上，分布在 3 个不同的数据中心。
·一致性:

```
o Options:
▪ Eventual Consistent Read (Default)
▪ Strongly consistent Read
o It comes with “Eventual Consistent Read” options where consistency across all copies of data is usually within a second, so better wait for some time (at least 1 second), before you make read operation after you updated the data. 
```

DynamoDB 更易于扩展，无需停机。
·dynamo db 支持键值和文档数据结构。键值存储是一种数据库服务，它支持存储、查询和更新使用包含实际存储内容的键和值来标识的对象集合。同时，文档数据存储支持以 JSON、XML 和 HTML 等文档格式存储、查询和更新项目。
·dynamo db 生存时间(TTL)机制使您能够轻松管理应用程序的 web 会话。它允许您设置一个特定的时间戳来删除表中过期的项目。一旦时间戳过期，相应的项目将被标记为过期，并随后从表中删除。通过使用此功能，您不必跟踪过期数据并手动删除它。TTL 可以帮助您减少存储使用量，并降低存储不再相关的数据的成本。
Amazon dynamo db 存储由主键索引的结构化数据，并允许对从 1 字节到 400KB 的项目进行低延迟读写访问。亚马逊 S3 存储非结构化 blobs，适合存储高达 5 TB 的大型对象。为了优化 AWS 服务的成本，大型对象或不常访问的数据集应该存储在亚马逊 S3，而较小的数据元素或文件指针(可能指向亚马逊 S3 对象)最好保存在亚马逊 DynamoDB 中。

# 弹性缓存

这是一个 web 服务，缓存最频繁的查询结果。
您可以使用内存中的键/值存储(如 Redis 和 Memcached)来管理来自 web 服务器的 HTTP 会话数据。
·支持 2 个开源内存缓存选项:

```
o Memcached: Memcached is an in-memory key-value store for small arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.
o Redis: Redis is an open source, in-memory data structure store used as a database, cache, and message broker. 
```

# 【红移】(OLAP)

由组织用来分析庞大的数据。用于德克萨斯州的 OLAP。
配置

```
o Single Node (160 GB)
o Multi Node:
▪ Leader Node:  manages client connection
▪ Compute Node: store data and perform query. Up to 128 nodes. 
```

与任何其他普通 DB 不同，

```
o this is column-based storage, where queries mostly involve performing aggregates over the huge set of data
o Column based data is better compressed than row-based data as column-based data is of same type while row-based data is across all columns and these columns are of different type. 
```

它不需要任何索引和物化视图，因此占用更少的空间。
·借助多节点架构，它提供了大规模并行处理(MPP)
·您将为计算节点而非领导节点付费。
加密

```
o Encryption at rest using AES-256 encryption.
o By default, Red shift takes care of key management, but still you can manage it using:
▪ AWS Key Management Service.
▪ HSM
o Encrypted in transit using SSL. 
```

这不是生产数据库，而是用于分析，因此对多 AZ 没有意义，因此是单 AZ。
在断电的情况下，可以将快照恢复到新的 AZ。

# 极光

兼容 MySQL 的数据库
，在扩展期间会停机。
·每个 AZ 2 份数据，最少 3 份，即 6 份数据。这意味着它可以承受最多 3 个拷贝丢失的读取可用性和 2 个拷贝丢失的写入可用性。解释可能是因为在每个 AZ 中应该存在至少 1 个用于读取的拷贝，因此它可以承受 3 个用于读取操作的拷贝的丢失。在 3 个 AZ 中，1 个将是主数据库，但不确定哪个副本，因此该特定 AZ 不应该宕机，因此它可以承受写操作丢失 2 个副本。
这是自愈的。对数据块和磁盘进行错误扫描并自动修复。
·两种类型的复制品:

```
o Aurora replicas: total 15
o MySQL replicas: 5 
```

Aurora 副本和 MySQL 副本的唯一区别是 Aurora 副本是故障转移。