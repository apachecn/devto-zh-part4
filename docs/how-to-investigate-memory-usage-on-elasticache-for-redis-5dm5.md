# 如何调查 Redis 的 ElastiCache 上的内存使用情况

> 原文：<https://dev.to/danimal141/how-to-investigate-memory-usage-on-elasticache-for-redis-5dm5>

最近，我遇到了一个问题，Redis 的 ElastiCache 上的内存使用量变得很大。这篇文章描述了我们如何调查这样一个问题(哪些键是 ElastiCache 上的瓶颈)。

## 导出并下载 Redis 备份

首先，为了进行分析，我们必须按照官方指南的[从我们的 ElastiCache 下载`.rdb`文件。](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/backups-exporting.html)

导出 ElastiCache 备份时，请注意 S3 的区域必须与备份的区域相同。

## 用 redis-rdb-tools 分析`.rdb`

下载文件后，我们可以用 [redis-rdb-tools](https://github.com/sripathikrishnan/redis-rdb-tools) 进行分析。

例如，如果我们想生成一个内存报告，我们可以这样做。

```
> rdb -c memory /var/redis/6379/dump.rdb --bytes 128 -f memory.csv
> cat memory.csv

database,type,key,size_in_bytes,encoding,num_elements,len_largest_element
0,list,lizards,241,quicklist,5,19 
```

Enter fullscreen mode Exit fullscreen mode

如果您的`.rdb`太大(可能很难打开 csv 文件)，您也可以生成一个示例报告，如:

```
> rdb -c memory /var/redis/6379/dump.rdb | ruby -ne 'print $_ if rand < 0.1' > memory.csv 
```

Enter fullscreen mode Exit fullscreen mode

当然，`redis-rdb-tools`支持除了生成内存报告之外的特性。
更多信息，请参见[自述](https://github.com/sripathikrishnan/redis-rdb-tools)。

## 总结

*   我们可以使用 ElastiCache 和 S3 轻松导出和下载备份文件。
*   我们可以使用类似于`redis-rdb-tools`的工具轻松分析一个`.rdb`文件。

## 参考文献

*   [https://docs . AWS . Amazon . com/amazonelastache/latest/red-ug/backups-exporting . html](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/backups-exporting.html)
*   [https://github . com/sripathikrisnan/redis RDB 工具](https://github.com/sripathikrishnan/redis-rdb-tools)