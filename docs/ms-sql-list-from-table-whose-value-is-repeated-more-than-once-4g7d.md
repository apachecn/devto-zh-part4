# MS-SQL，其值重复多次的表中的列表。

> 原文：<https://dev.to/bikashgosai/ms-sql-list-from-table-whose-value-is-repeated-more-than-once-4g7d>

```
SELECT 
NAME, EMAIL, COUNT(*) 
FROM 
USERS 
GROUP BY
 NAME, EMAIL
HAVING COUNT(*) > 1 
```

Enter fullscreen mode Exit fullscreen mode