# 如何给 PostgreSQL 添加换行符？

> 原文：<https://dev.to/jetrockets/how-to-add-a-line-break-to-postgresql-2m2n>

您必须将一些数据插入到有换行符的 PostgreSQL 表中。因此，单元格将看起来像这样:

```
First line
Second line 
```

要在字符串中使用“转义序列”，你需要使用一个[“扩展”常量](https://www.postgresql.org/docs/current/sql-syntax-lexical.html#SQL-SYNTAX-STRINGS-ESCAPE)。您可以通过在字母 E 前加前缀来指定转义字符:

```
UPDATE posts SET body = E'First Line\nSecond line.' WHERE id = 2701; 
```