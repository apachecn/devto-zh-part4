# 分布式内存缓存

> 原文：<https://dev.to/vikashagrawal/distributed-memory-caching-33fd>

有两种内存数据存储系统可用:

# 再说一遍

*   Redis 是一个单线程系统。
*   它可以很容易地水平缩放。
*   键和值的大小可以分别达到 512 MB。
*   以下 5 种数据类型可用:
    *   线
    *   混杂
    *   目录
    *   一组
    *   排序集合
*   任何种类的 CRUD 操作都可以在给定数据的字段级别上执行。
    *   这意味着应用程序不需要加载全部数据。
*   有多种驱逐策略可用:
    *   不驱逐:当达到内存限制时，它抛出错误。
    *   所有键 LRU:删除最近最少使用的键。
    *   易变 LRU:首先删除最近最少使用的密钥，这些密钥设置了到期时间。
    *   所有键随机:随机移除键。
    *   Volatile Random:随机删除设置了到期时间的密钥
    *   TTL:删除已经过期的密钥。
*   支持两种备份:
    *   RDB 快照:这种快照定期发生。
    *   AOF:对每个写操作执行仅附加文件。它可以降低写操作的速度，但可以确保数据的可用性。

# Memcached

*   Memcached 是一个多线程系统。
*   它可以很容易地垂直缩放。
*   密钥的大小最大为 250B，值最大为 1 MB。
*   通过客户端复杂算法，水平缩放是可能的。
*   只是在字符串数据类型上。
*   无法在给定数据的字段级别上执行任何操作。
    *   这意味着应用程序需要加载全部数据。
*   它只对 LRU 驱逐政策有效。这是一种分布式内存缓存对象存储。它有 4 个组成部分:

```
o Client software: fetch the list of available servers.
o Server software: A logic to store and retrieve the value for the passed key.
o Expiry: Mechanism to remove the older contents based on the time expiry.
o Client based hashing algorithm: to fetch the corresponding server. 
```

Enter fullscreen mode Exit fullscreen mode

缓存数据存储在 RAM 中，因此当 RAM 耗尽内存时，它会进行清理。没有容错意味着它不会与其他服务器共享数据。