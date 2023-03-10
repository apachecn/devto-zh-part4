# AWS 存储服务

> 原文：<https://dev.to/vikashagrawal/aws-storage-services-4ocl>

# S3(简单仓储服务)

它是基于对象的存储，因此不适合存储操作系统和数据库。
物体存放在桶中。
桶的 URL 将是[https://S3-](https://s3-). Amazon AWS . com/
它包括:

```
o Key: Name of the object
o Value: Actual data inside the file
o Version Id:
o Meta Data: information about the object like content-type.
o Sub resources:
▪ Access Control List: Individual permission.
▪ Torrent: Used for torrenting (Not so important) 
```

充电:

```
o Storage
o Requests
o Storage Management Pricing: Tagging to particular department like HR, Engineering etc.
o Data Transfer Pricing
o Transfer Acceleration
▪ it enables to use the cloud front (edge location) to be used by the user to upload the files and then AWS uses the optimize route to upload it to its Region based S3 bucket.
▪ While enabling this property under S3 bucket, it provides the unique link, which connects to edge location. 
```

它还提供网络托管，URL 看起来像 http://. s3-website-. Amazon aws . com
它提供无限存储
数据自动分布在 AWS 区域内地理上分离的至少三个物理设施上，亚马逊 S3 还可以自动将数据复制到任何其他 AWS 区域。
亚马逊 S3 现在提供更高的性能，支持每秒至少 3，500 个添加数据的请求和每秒 5，500 个检索数据的请求，这可以节省大量处理时间，而无需额外收费。
允许上传文件，文件大小可以从 0B 到 5TB。
在 S3 多部分上传中，您可以上传最大 5 TB 的对象大小和 5 MB 到 5 GB 的部分大小(最后一部分可以是< 5 MB)
请参考下表了解完整信息:

```
o Maximum object size    5 TB
o Maximum number of parts per upload    10,000
o Part numbers    1 to 10,000 (inclusive)
o Part size    5 MB to 5 GB, last part can be < 5 MB
o Maximum number of parts returned for a list parts request    1000
o Maximum number of multipart uploads returned in a list multipart uploads request    1000 
```

S3 是通用名称空间，这意味着存储桶名称必须是唯一的。
·S3 存储桶是在区域级别创建的，但上市是在“全球”级别进行的。
当文件上传到 bucket 成功时，您将收到 HTTP 代码 200
立即上传新对象的写后读一致性。
覆盖上传和删除的最终一致性可能需要一些时间来传播，因为它分布在多个 az 上，因此更新需要一些时间，而创建新对象不需要太多时间。
加密

```
o In Transit
▪ SSL/TLS
o At Rest
▪ Server side
• S3 managed keys – SSE-S3 (AWS self-managed)
• AWS Key Managed Service – SSE-KMS, it’s same as SSE but with extra charge.
• Customer provided key – SSE-C
▪ Client side 
```

访问控制列表(针对单个文件)
存储桶策略
版本

```
o Once the versioning has been enabled to bucket, it cannot be disabled, it can be suspended.
o Being version control system, it would like to keep the deleted file as well. So when we delete any file, it deletes the file and put “Delete Marker” to it, which can be seen by clicking “show” option and then if you delete “delete marker”, it brings back the file, whichever is latest that time.
o Being object-based storage, it keeps every versioned file within it and hence space consuming, so not suggested for bucket having bigger file.
o MFA can be enabled to avoid accidental delete. 
```

跨区域复制

```
o It enables to copy the object from any bucket of any account to any bucket of any account.
o As the replications replaces the object hence it has to versioned. So only bucket with “Version enabled” can be replicated to any bucket with “Version enabled”.
o The time when the replication is enabled, it copies the data from that time onwards only, for the pre-existing objects, it has to copied explicitly.
o It works for object modified directly in the bucket not in the Versioning (Show option). E.g. if any file has 2 version and it has been deleted the same will be replicated in the destination bucket but if the “Delete Marker” has been deleted to bring the latest file back, the same wouldn’t be replicated in destination bucket.
o Replication to multiple bucket doesn’t work for now. 
```

为 99.99 %的可用性而构建，但亚马逊保证 99.9%
。99.999999999%(11 个 9)的耐用性保证意味着给定文件的 99.999999999%是安全的。
分层存储

```
o S3 standard: 99.99 % availability and 99.999999999% (11 9s) durability
o S3-IA: infrequently accessed but spread across multiple AZs.
o S3 One Zone-IA: infrequently accessed but spread across only 1 AZ.
o Reduced Redundancy Storage (RRS): For frequently accessed data. Stores noncritical, reproducible data at lower levels of redundancy than Standard. It provides 99.99% availability and 99.99%durability. It will be useful when we have some retrieval policy like meta data in dynamo DB.
o Glacier:
▪ Expedited: Retrieval is quick
▪ Standard: Retrieval time is 3-5 hours.
▪ Bulk: Retrieval time is 7-12 hours 
```

生命周期管理

```
o Can be used in conjunction with versioning.
o Can be applied to current and previous version.
o It enables the movement of file from standard to following category in the mentioned order, with minimum number of days and gap between the 2 categories should be 30:
▪ IA
▪ one zone IA
▪ glacier 
```

# 冰川

归档文件存储，用于访问频率很低的内容。

# 雪球

这是一种通过防篡改和安全设备手动将大量数据引入 AWS 数据中心的方式。它主要用于遗留应用程序。它可以从 S3 进出口。
类型

```
o Snowball
▪ it’s an appliance, which helps in transfer data to and from AWS.
▪ Once the data is processed and verified, AWS perform erasure.
▪ It supports up to 80TB
o Snowball edge
▪ It supports up to 100TB
▪ It supports compute facility as well.
o Snowball mobile
▪ It supports up to 100PB 
```

# 存储网关

这是将组织内部的 DCs 连接到 AWS 服务的方式。
·AWS 提供基于虚拟机的软件，安装在您的主机上并与之连接。
类型:

```
o File Gateway (NFS): Flat file.

o Volume Gateway (iSCSI): Block based storage like OS, it’s stored in Amazon Elastic Block Store and it stores only the changed block.
▪ Stored Volume: Entire data set is stored in onsite and backed up to cloud asynchronously. It stores from 1GB to 16TB

▪ Cached Volume: Most frequently accessed data is stored in onsite and rest is backed up in cloud. It stores from 1GB to 32TB

o Tape Gateway (VTL): Infrequently access data, which then moved to Glacier based on the Lifecycle management. 
```

# 【弹性文件存储】(EFS)

它不需要像我们在创建 EC2 时那样进行任何资源调配。
“EFS”是基于数据块的存储。
它根据需要收缩和扩张。
支持网络文件系统 V4。
它可以扩展到 Pb 级。
支持多达数千个 NFS 连接。
·写后读一致性。
·它在给定区域的多个 az 中传播。
EC2 实例通过装载目标访问 EFS。
AWS 建议在所有 az 中创建挂载目标，以便每个实例都有自己的挂载目标。
为了让 EC2 访问 EFS，EFS 的安全组应该是 EC2 的安全组之一
。按照 EFS 的说明，我们可以将其挂载到所有 EC2 实例。