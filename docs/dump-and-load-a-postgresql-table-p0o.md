# 转储和恢复 PostgreSQL 表

> 原文：<https://dev.to/andyatkinson/dump-and-load-a-postgresql-table-p0o>

在进行本地 web 开发时，使用真实数据可以更容易地重现和调试问题。特别是，如果复杂的配置只存在于生产或试运行中，精确地填充数据会特别方便。

为了解决这个问题，一个解决方案是在本地表中加载远程数据库内容。

`pg_dump`可用于将远程表转储到如下文件中。用真正的连接字符串替换`db_connection_string`，或者可以从环境变量中读取。

```
pg_dump --verbose --compress=6 -Fc --no-acl --no-owner -h localhost 
`db_connection_string` -t table_name > tmp/table_dump_file_name.sql 
```

Enter fullscreen mode Exit fullscreen mode

还要注意，`table_name`和`table_dump_file_name`是占位符，用您希望使用的表格替换它们。一旦创建了包含内容的文件:

`pg_restore`可用于将内容加载到如下表格中。

```
pg_restore -d database_name tmp/table_dump_file_name.sql 
```

Enter fullscreen mode Exit fullscreen mode

嘣。就是这样！