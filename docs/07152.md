# 在 Sql Server 中本地化日期

> 原文：<https://dev.to/mikeborn/localizing-dates-using-ms-sql-server-1cje>

几个月前，我在一个 CF 应用程序中添加了时区处理。这篇文章是我最大的努力来记录如何使用 MSSQL 时区功能本地化日期。

## 从 MSSQL 中检索时区

首先，我们需要知道我们可以使用什么时区。 [MSSQL 使用存储在注册表](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql?view=sql-server-2017)中的 Windows 时区，并且可以从`sys.time_zone_info`系统视图中检索它们。

```
SELECT * FROM sys.time_zone_info 
```

不要尝试使用 CFML 时区与这些时区进行交互——底层 Java 时区使用不同的时区格式，并且与 MSSQL/Windows 时区格式不匹配。

另外，你可能会注意到时区列表*不包括夏令时*的不同时区。例如，`Eastern Standard Time`有一个时区记录，即 EST。`Eastern Daylight Time`无处可寻，因为该视图会根据该时区的当前时间自动更新`current_utc_offset`和`is_currently_dst`的值。

## 从已知时区检索当前 UTC 偏移量

接下来，我们使用这些时区来获取当前的 utc 偏移量。注意，我说的是**当前**—**而不是**存储 UTC 偏移，因为对于给定的时区，它至少一年要改变两次。(例如夏令时。)

因此，当您需要用户的本地时间时，我们需要存储用户的时区并检索 UTC 偏移。

这里有一个简单的方法来检索一个已知时区的当前 UTC 偏移量:

```
SELECT current_utc_offset
FROM sys.time_zone_info
WHERE name='US Eastern Standard Time' 
```

显然，为了知道使用哪个时区，您需要允许用户选择他们的时区，这样您就可以存储用户的时区。

## 使用 AT 时区将日期本地化为时区

[如果需要指示日期时间值的 UTC 偏移量，请使用`AT TIME ZONE`](https://dzone.com/articles/dates-and-times-in-sql-server-at-time-zone):

```
GETDATE() AT TIME ZONE 'US Eastern Standard Time' 
```

基本上，这允许我们选择一个存储时没有 UTC 偏移的日期，并给它添加一个 UTC 偏移——前提是我们知道原始时区。这为我们使用`AT TIME ZONE`将日期从原始时区转换到另一个时区——使用[的`SWITCHOFFSET`函数](https://database.guide/switchoffset-examples-in-sql-server/)铺平了道路。

## 开关偏移

一旦我们有了一个`DATETIMEOFFSET`值，我们就可以使用`SWITCHOFFSET(DATETIMEOFFSET, time_zone)`来切换日期时间以匹配用户的本地时间:

```
SWITCHOFFSET(
    GETDATE() AT TIME ZONE 'US Eastern Standard Time',
    (
        SELECT current_utc_offset
        FROM sys.time_zone_info
        WHERE name='Central European Standard Time'
    )
) 
```

## 结论

不幸的是，这不是一篇完整的时区本地化文章。本地化非常复杂，我学得越多，知道的越少。但是希望这个使用内置 MSSQL 功能的时区处理的简要概述会对*的某人*有用——享受吧！