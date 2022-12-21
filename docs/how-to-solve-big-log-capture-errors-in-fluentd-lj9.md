# 如何解决 Fluentd 中的大日志捕获错误？

> 原文：<https://dev.to/yoshidaagri/how-to-solve-big-log-capture-errors-in-fluentd-lj9>

## 总结:

*   这个备忘录是在用 Fluentd 构建一个日志监控系统的时候。

## 建筑:

*   见上图。

## 我的 Fluentd 错误信息:

```
2019-08-30 09:44:20 +0900 [info]: #0 bulk insert values size (table: access_log) => 37207
2019-08-30 09:44:20 +0900 [warn]: #0 failed to flush the buffer. 
retry_time=1 
next_retry_seconds=2019-08-30 09:44:21 +0900 chunk="5914aebbe44b1ad68c3f180a99706368" error_class=Mysql2::Error::ConnectionError 
error="MySQL server has gone away" 
```

*   ...离开了？

## 错误原因:

*   错误的原因是我的 Mysql 设置。
*   设置名称为 max _ allowed _ packet.defualt 设置 4M。
*   我用的是标准设置。但是，要捕获的日志文件是 9M...

## 对应关系:

*   修复了 mysql 设置。
    *   之前:最大允许数据包数:4M
    *   之后:最大允许数据包:16M

## 结果:

```
2019-08-30 16:10:29 +0900 [info]: #0 detected rotation of /var/log/nginx/access.log; waiting 5 seconds
2019-08-30 16:10:35 +0900 [info]: #0 following tail of /var/log/nginx/access.log
2019-08-30 16:10:39 +0900 [info]: #0 bulk insert values size (table: access_log) => 37207
2019-08-30 16:10:47 +0900 [info]: #0 bulk insert values size (table: access_log) => 13854 
```

太好了！！

谢谢你