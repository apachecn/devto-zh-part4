# 基于 HAProxy 的缓存服务器 Nuster 现在支持磁盘持久性

> 原文：<https://dev.to/nuster/nuster-a-cache-server-based-on-haproxy-now-supports-disk-persistence-3f9m>

nuster 3 . 0 . 0 . 19 发布，现在支持磁盘持久性

[https://github.com/jiangwenyuan/nuster](https://github.com/jiangwenyuan/nuster)

有四种模式。

*   关:默认，禁用磁盘持久性，数据只存储在内存中
*   仅:仅将数据保存到磁盘，不存储在内存中
*   同步:将数据保存到内存和磁盘(内核)，然后返回到客户端
*   异步:将数据保存到内存并返回给客户端，缓存的数据将由主进程保存到磁盘

```
nuster rule off   disk off   ttl 1m if { path_beg /disk-off } 
nuster rule only  disk only  ttl 1d if { path_beg /disk-only }
nuster rule sync  disk sync  ttl 1h if { path_beg /disk-sync }     
nuster rule async disk async ttl 2h if { path_beg /disk-async } 
nuster rule others ttl 100 
```

1.  /disk-off 将只缓存在内存中
2.  /disk-only 将只缓存在磁盘中
3.  /disk-sync 将被缓存在内存和磁盘中，然后返回给客户端
4.  /disk-async 将缓存在内存中并返回给客户端，缓存的数据稍后将保存到磁盘
5.  其他请求将只缓存在内存中