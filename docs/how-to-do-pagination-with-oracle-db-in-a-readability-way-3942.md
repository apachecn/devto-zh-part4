# 如何用 Oracle DB 以可读性强的方式实现分页

> 原文：<https://dev.to/sgtino/how-to-do-pagination-with-oracle-db-in-a-readability-way-3942>

那是一个星期天的早上。一个星期天的清晨。我在等我的家醒来。所以我再次关注我未完成的任务:如何用 Oracle DB (11c)对我的记录集进行分页...).

我很受启发，找到了一个不用子选择的解决方案。

我尽量避免使用 subselect，因为我认为它可读性不强(我找到了很多使用它的例子)。我已经找到了使用 [`WITH`语句](https://oracle-base.com/articles/misc/with-clause)的方法。

`WITH`语句允许有一个干净简单的 SQL 代码。

这就是结果。

```
WITH RECORDSET AS (
    -- put here your select with the complete recordset.
    SELECT FIELDA, FIELDB, FIELDC FROM TABLE
), 
NUMBERED AS (
    SELECT 
    ROW_NUMBER() OVER (ORDER BY FIELDA) RN, 
    RECORDSET.*
    FROM RECORDSET)
SELECT
    -- page number parameter
    :page_number PAGE_NUMBER, 
    -- total recordset pages
    CEIL((SELECT COUNT(*) FROM NUMBERED) / :page_size) TOTAL_PAGES, 
    -- page size parameter
    :page_size PAGE_SIZE, 
    -- total rows
    (SELECT COUNT(*) FROM NUMBERED) TOTAL_ROWS, 
    NUMBERED.*
FROM NUMBERED
WHERE 
    RN BETWEEN ((:page_size*:page_number)-:page_size+1) AND (:page_size*:page_number) 
```

这段代码需要两个参数:记录集`:page_size`和要检索的`:page_number`。

第一个字段包含分页数据:`PAGE_NUMBER`、`TOTAL_ROWS`、`PAGE_SIZE`和`TOTAL_ROWS`。

我认为这是获得分页记录集的一种干净的方式，我发现它的性能也很好。