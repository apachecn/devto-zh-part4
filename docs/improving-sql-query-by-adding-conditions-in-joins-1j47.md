# 通过在连接中添加条件来改进 SQL 查询

> 原文：<https://dev.to/akashkava/improving-sql-query-by-adding-conditions-in-joins-1j47>

最近我试图优化查询，这是查询，

```
SELECT ... 
FROM A
INNER JOIN B
   ON B.ID = A.ID
WHERE B.Status = 'Done' 
   AND B.DateCreated BETWEEN @Start and @End
   AND NOT EXISTS (...)
   AND A. ... other conditions 
```

Enter fullscreen mode Exit fullscreen mode

表`B`很大，查询需要几秒钟。即使有一个索引(ID，Status，DateCreated)。

当我将查询更改为，

```
SELECT ... 
FROM A
INNER JOIN B
   ON B.ID = A.ID AND B.Status = 'Done'
   AND B.DateCreated BETWEEN @Start and @End
   AND NOT EXISTS (...)
WHERE A. ... other conditions 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，查询只花了几毫秒。经过进一步的研究，我发现将`NOT EXISTS`移动到 JOIN 内部可以提高速度。