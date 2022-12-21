# 如何在 SQL 中使用 Group By 获取组中的第一个或最后一个值

> 原文：<https://dev.to/haki/how-to-get-the-first-or-last-value-in-a-group-using-group-by-in-sql-3j0o>

最近，我不得不在一个包含用户帐户余额事件的表格上生成报告。用户可以从他们的帐户中存款和取款，支持人员可以设置帐户的信用，这是用户可以透支的最大金额。

表格大致如下:

```
db=# SELECT * FROM event;
 id | account |    type    |      happened_at       |               data
----+---------+------------+------------------------+-----------------------------------
  1 |       1 | created    | 2019-08-01 15:14:13+03 | {"credit": 0, "delta_balance": 0}
  2 |       1 | deposited  | 2019-08-01 15:15:15+03 | {"delta_balance": 100}
  3 |       1 | withdraw   | 2019-08-02 09:35:33+03 | {"delta_balance": -50}
  4 |       1 | credit_set | 2019-08-03 16:14:12+03 | {"credit": 50}
  5 |       1 | withdraw   | 2019-08-03 14:45:44+03 | {"delta_balance": -30}
  6 |       1 | credit_set | 2019-08-03 16:14:12+03 | {"credit": 100}
  7 |       1 | withdraw   | 2019-08-03 16:15:09+03 | {"delta_balance": -50}
(7 rows) 
```

[![What deposit boxes used to look like. Photo by <a href="https://unsplash.com/@tjevans">Tim Evans</a>](img/49a29cbe99fe539fbb69305629846d96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---DcvcHFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7Bstatic%img/00-sql-group-by-first-last-value.jpg)

为了获得账户的当前余额，我们将`delta_balance` :
中的变化相加

```
db=# SELECT
    account,
    SUM((data->'delta_balance')::int) AS balance
FROM
    event
GROUP BY
    account;

 account | balance
---------+---------
       1 |     -30 
```

`data`字段包含特定于每种类型事件的信息。为了从`data`列中提取`delta_balance`的值，我们使用 PostgreSQL 提供的[箭头操作符。](https://www.postgresql.org/docs/current/functions-json.html#FUNCTIONS-JSON-OP-TABLE)

查询结果显示账户 1 的当前余额为-30。这意味着账户透支了。为了检查这是否在允许的范围内，我们需要将其与该帐户的信用集进行比较。创建帐户时，帐户 1 的信用最初设置为 0。该积分随后调整了两次，目前设定为 100。

为了获得一个帐户的当前状态，我们需要它的总余额和为它设置的最新信用。

## 问题

在 Oracle 中有一个叫做 [`last`](https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions071.htm) 的函数，我们可以用它来获取最后一个`credit_set`事件。使用`last`的查询可能如下所示:

```
-- Oracle
SELECT
    account,
    MAX(CASE WHEN type = 'credit_set' THEN data ELSE null END)
        KEEP (DENSE_RANK LAST ORDER BY id) AS credit
FROM
    event
GROUP BY
    account; 
```

PostgreSQL 还有一个 [`LAST_VALUE`](https://www.postgresql.org/docs/current/functions-window.html#id-1.5.8.26.6.2.2.10.1.1) 解析函数。不能像聚合函数那样在组中使用分析函数:

```
db=# SELECT
    account,
    LAST_VALUE(data) OVER (
        PARTITION BY account ORDER BY id
        RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS credit
FROM
    event
WHERE
    type = 'credit_set'
GROUP BY
    account;

ERROR:  column "event.data" must appear in the GROUP BY clause or be used in an aggregate function
LINE 3:     LAST_VALUE(data) OVER ( 
```

该错误告诉我们，分析函数中使用的`data`字段必须在 group by 中使用。这不是我们真正想要的。要使用 PostgreSQL 的`LAST_VALUE`函数，我们需要删除 group by:

```
db=# SELECT
    account,
    LAST_VALUE(data) OVER (
        PARTITION BY account ORDER BY id
        RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS credit
FROM
    event
WHERE
    type = 'credit_set';

 account |     credit
---------+-----------------
       1 | {"credit": 100}
       1 | {"credit": 100} 
```

这些并不完全是我们需要的结果。分析函数或窗口函数对一组行进行操作，而不是对 group by 进行操作。

PostgreSQL 没有使用 group by 获得组中第一个或最后一个值的内置函数。

要获得一个组中的最后一个值，我们需要变得有创造性！

* * *

## 老式 SQL

简单的 SQL 解决方案是分而治之。我们已经有一个查询来获取帐户的当前余额。如果我们编写另一个查询来获取每个帐户的信用，我们可以将两者结合在一起，并获得帐户的完整状态。

要获得 PostgreSQL 中每个帐户的最后一个事件，我们可以使用 [`DISTINCT ON`](https://www.postgresql.org/docs/current/sql-select.html#SQL-DISTINCT) :

```
db=# SELECT DISTINCT ON (account)
    account,
    data->'credit' AS credit
FROM
    event
WHERE
    type = 'credit_set'
ORDER BY
    account,
    id DESC;

account | credit
---------+--------
       1 | 100 
```

太好了！使用`DISTINCT ON`,我们得到了每个账户的最后一个信用集。

*DISTINCT ON:我曾经写过关于[PostgreSQL](https://hakibenita.com/the-many-faces-of-distinct-in-postgre-sql)中 DISTINCT 的许多方面。*

下一步是连接两个查询，并获得完整的账户状态:

```
db=# SELECT
    a.account,
    a.balance,
    b.credit
FROM
    (
    SELECT
        account,
        SUM((data->'delta_balance')::int) AS balance
    FROM
        event
    GROUP BY
        account

    ) AS a
    JOIN
    (

    SELECT DISTINCT ON (account)
        account,
        data->'credit' AS credit
    FROM
        event
    WHERE
        type = 'credit_set'
    ORDER BY
        account,
        id DESC

    ) AS b

    ON a.account = b.account;

 account | balance | credit
---------+---------+--------
       1 |     -30 | 100 
```

我们得到了预期的结果。

在我们继续之前，让我们看一下执行计划:

```
 QUERY PLAN
---------------------------------------------------------------------------------------------
 Hash Join  (cost=44.53..49.07 rows=4 width=44)
   Hash Cond: (event.account = b.account)
   ->  HashAggregate  (cost=25.00..27.00 rows=200 width=12)
         Group Key: event.account
         ->  Seq Scan on event  (cost=0.00..17.50 rows=750 width=36)
   ->  Hash  (cost=19.49..19.49 rows=4 width=36)
         ->  Subquery Scan on b  (cost=19.43..19.49 rows=4 width=36)
               ->  Unique  (cost=19.43..19.45 rows=4 width=40)
                     ->  Sort  (cost=19.43..19.44 rows=4 width=40)
                           Sort Key: event_1.account, event_1.id DESC
                           ->  Seq Scan on event event_1  (cost=0.00..19.39 rows=4 width=40)
                                 Filter: (type = 'credit_set'::text) 
```

事件表被扫描两次，每个子查询一次。`DISTINCT ON`子查询也需要按照`account`和`id`进行排序。然后使用散列连接来连接这两个子查询。

**PostgreSQL 无法将两个子查询合并到一次表扫描中。**如果事件表非常大，执行两次全表扫描、一次排序和一次散列连接可能会变得很慢并消耗大量内存。

公共表表达式:使用公共表表达式(CTE)使查询更具可读性是很有诱惑力的。但是， [CTE 目前是优化栅栏](https://hakibenita.com/be-careful-with-cte-in-postgre-sql)，在这里使用它将绝对会阻止 PostgreSQL 执行任何涉及两个子查询的优化。

* * *

## 本阵绝招

使用优秀的 ol' SQL 为我们找到了答案，但它需要经过两次验证。我们可以用下面的技巧做得更好:

```
db#=>SELECT
    account,
    SUM((data->'delta_balance')::int) AS balance,
    (MAX(ARRAY[id, (data->'credit')::int]) FILTER (WHERE type = 'credit_set'))[2] AS credit
FROM
    event
GROUP BY
    account;

 account | balance | credit
---------+---------+--------
       1 |     -30 |    100 
```

这比上一个问题简单多了，我们来分解一下。

### PostgreSQL 如何比较数组

为了理解这里到底发生了什么，我们首先需要理解【PostgreSQL 如何比较数组:

> 数组比较使用元素数据类型的默认 B 树比较函数，逐个元素地比较数组内容。

比较数组时，PostgreSQL 会逐个元素地比较，并根据类型比较值。演示:

```
db=# SELECT greatest(ARRAY[1, 200], ARRAY[2, 100]);
 greatest
----------
 {2,100} 
```

第二个数组(2)的第一个元素比第一个数组(1)的第一个元素大，所以它是最大的。

```
db=# SELECT greatest(ARRAY[1, 200], ARRAY[1, 201]);
 greatest
----------
 {1,201}
(1 row) 
```

两个数组的第一个元素相等(1)，因此 PostgreSQL 会移动到下一个元素。在这种情况下，第二阵列(201)的第二元素是最大的。

### Max by 键...

利用 PostgreSQL 的这个特性，我们构造了一个数组，其中第一个元素是排序所依据的值，第二个元素是我们想要保留的值。在我们的例子中，我们希望通过最大值`id` :
获得积分

```
MAX(ARRAY[id, (data->'credit')::int]) 
```

并非所有事件都设置信用，因此我们需要将结果限制为`credit_set`个事件:

```
MAX(ARRAY[id, (data->'credit')::int]) FILTER (WHERE type = 'credit_set') 
```

这个表达式的结果是一个数组:

```
db#=>SELECT
    account,
    MAX(ARRAY[id, (data->'credit')::int]) FILTER (WHERE type = 'credit_set'))
FROM
    event
GROUP BY
    account;

 account |   max
---------+---------
       1 | {6,100} 
```

我们只需要第二个元素，即`credit` :
的值

```
(MAX(ARRAY[id, (data->'credit')::int]) FILTER (WHERE type = 'credit_set'))[2] 
```

这就是了！这样我们就可以得到每个账户的最后一笔贷款。

接下来，让我们检查一下执行计划:

```
 QUERY PLAN
---------------------------------------------------------------
 HashAggregate  (cost=32.50..34.50 rows=200 width=16)
   Group Key: account
   ->  Seq Scan on event  (cost=0.00..17.50 rows=750 width=72) 
```

简单查询的简单计划！

这种方法的主要好处是它只需要一遍表，不需要排序。

### 告诫

这种方法非常有用，但是有一些限制:

所有元素必须是同一类型。

PostgreSQL 不支持具有不同类型元素的[数组。举个例子，如果我们想通过日期而不是 id 获得最后的信用设置:](https://www.postgresql.org/docs/current/arrays.html#ARRAYS-DECLARATION) 

```
db=# SELECT
    account,
    SUM((data->'delta_balance')::int) AS balance,
    (MAX(
        ARRAY[happened_at, (data->'credit')::int]
    ) FILTER (WHERE type = 'credit_set'))[2] AS credit
FROM
    event
GROUP BY
    account;

ERROR:  ARRAY types timestamp with time zone and integer cannot be matched
LINE 4:     (MAX(ARRAY[happened_at, (data->'credit')::int]) FILTER... 
```

PostgreSQL 告诉我们一个数组不能同时包含时间戳和整数。

在某些情况下，我们可以通过转换其中一个元素来克服这个限制:

```
db=# SELECT
    account,
    SUM((data->'delta_balance')::int) AS balance,
    (MAX(
        ARRAY[EXTRACT('EPOCH' FROM happened_at), (data->'credit')::int]
    ) FILTER (WHERE type = 'credit_set'))[2] AS credit
FROM
    event
GROUP BY
    account;

 account | balance | credit
---------+---------+--------
       1 |     -30 |    100 
```

我们[将时间戳转换为纪元](https://www.postgresql.org/docs/current/functions-datetime.html#FUNCTIONS-DATETIME-EXTRACT)，这是自 1970 年以来的秒数。一旦两个元素属于同一类型，我们就可以使用数组技巧了。

查询可能会消耗更多的内存。

这有点夸张，但是正如我们在过去的
，
中演示的那样，大的分组和排序键在连接和排序中消耗更多的内存。使用数组技巧，组键是一个数组，比我们通常排序所依据的普通字段大一点。

同样，数组技巧可以用来“搭载”多个值:

```
(MAX(ARRAY[
    id,
    (data->'credit')::int,
    EXTRACT('EPOCH' FROM happened_at)
]) FILTER (WHERE type = 'credit_set'))[2:] 
```

该查询将返回最后一个信用设置及其设置日期。整个数组用于排序，所以我们放入数组的值越多，组键就越大。

## 结论

数组技巧非常有用，可以显著简化复杂的查询并提高性能。我们使用它来生成关于时间序列数据的报告，并从事件表中生成读取模型。

* * *

**向我的读者呼吁:**我很确定在 PostgreSQL 中使用[用户定义的聚合函数应该可以创建一个带有签名`MAX_BY(key, value)`的函数。我还没有时间深入研究自定义聚合函数，但是如果有读者这样做，请分享您的实现，我很乐意在这里发布。](https://www.postgresql.org/docs/current/xaggr.html)

* * *

*更新日期:2019 年 8 月 17 日*

## 读者评论

在这篇文章发表后的几天里，我收到了一些读者的建议和评论。这是我收到的评论的摘要，以及我对它们的想法。

* * *

Reddit 上的一位[评论者](https://www.reddit.com/r/PostgreSQL/comments/cpskf7/how_to_get_the_first_or_last_value_in_a_group/ewsbdqo/)建议使用`ARRAY_AGG` :

```
db=# SELECT
    account,
    ((ARRAY_AGG(data ORDER BY happened_at DESC)
        FILTER (WHERE type = 'credit_set'))[1] -> 'credit')::int AS credit
FROM
    event
GROUP BY account;

 account | credit
---------+--------
       1 |     50 
```

这种方法显然是可行的，它不要求键和值是同一类型的，这是数组技巧的一个很大的限制。

这种方法的缺点是，对于非常大的数据集，它需要一种可能变得昂贵的排序:

```
 QUERY PLAN
------------------------------------------------------------------
 GroupAggregate  (cost=1.17..1.36 rows=7 width=8)
   Group Key: account
   ->  Sort  (cost=1.17..1.19 rows=7 width=76)
         Sort Key: account
         ->  Seq Scan on event  (cost=0.00..1.07 rows=7 width=76) 
```

* * *

Reddit 上的另一位[评论者](https://www.reddit.com/r/PostgreSQL/comments/cpskf7/how_to_get_the_first_or_last_value_in_a_group/ewu6juf/)建议结合`DISTINCT ON`使用窗口函数。这是原建议:

```
SELECT DISTINCT ON (account)
    account,
    FIRST_VALUE((data->'credit')::int) OVER w,
    LAST_VALUE((data->'credit')::int) OVER w,
    SUM((data->'credit')::int) OVER w
FROM
    event
WHERE
    type = 'credit_set'
WINDOW w AS (
    PARTITION BY account
    ORDER BY id
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
) 
```

该查询同时使用了`DISTINCT ON`和窗口函数。它的工作方式是使用 window 函数对整个集合(帐户的所有行)计算聚合，然后使用`DISTINCT ON`获取第一行或最后一行。

为了使窗口函数表现得像“group by”一样，并计算整个集合上的聚合，界限被定义为`BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`，意思是“对于整个分区”。

为了避免为每个聚合重复窗口，查询使用了一个窗口子句来定义一个可以在查询中多次使用的命名窗口。

然而，这个查询并没有真正起作用，因为 where 子句仅限于类型为`credit_set`的事件。为了获得账户的完整状态，我们还需要合计所有事件的*余额。*

为了让这种方法真正发挥作用，我们需要做以下调整:

```
db=# SELECT DISTINCT ON (account)
    account,
    LAST_VALUE((data->'credit')::int) OVER (
        PARTITION BY account
        ORDER BY (
            CASE
                WHEN type = 'credit_set' THEN happened_at
                ELSE null
            END
        ) ASC NULLS FIRST
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) as credit,
    SUM(COALESCE((data->'delta_balance')::int, 0)) OVER (PARTITION BY account) AS balance
FROM
    event;

 account | credit | balance
---------+--------+---------
       1 |    100 |     -30 
```

我们做了哪些改变:

*   我们必须放弃 where 子句，以便处理所有事件。
*   我们还必须做一些“创造性排序”来获得`last_credit` set 事件。
*   我们删除了命名窗口，因为它不再被重用。

计划也变得更加复杂:

```
 Unique  (cost=1.19..1.55 rows=7 width=24)
   ->  WindowAgg  (cost=1.19..1.54 rows=7 width=24)
         ->  WindowAgg  (cost=1.19..1.38 rows=7 width=48)
               ->  Sort  (cost=1.19..1.20 rows=7 width=44)
                     Sort Key: account, (CASE WHEN (type = 'credit_set'::text)
                                         THEN happened_at ELSE NULL::timestamp with time zone
                                         END) NULLS FIRST
                     ->  Seq Scan on event  (cost=0.00..1.09 rows=7 width=44) 
```

两个排序，和几个聚合。在我看来，底线是这种方法更难维护，并且它产生一个明显更复杂的计划。我不会在这种情况下使用它。然而，与前面的方法一样，它不受键和值的类型的限制。

* * *

在回复[我的推文](https://twitter.com/mdevanr/status/1161275759786418177)时，一位读者把我指向了[一个旧的维基页面](https://wiki.postgresql.org/wiki/First/last_(aggregate))，它实现了两个自定义聚合函数`FIRST`和`LAST`。

按照 wiki 页面中的说明在数据库中创建自定义聚合后，查询可能如下所示:

```
SELECT
    account,
    SUM(COALESCE((data->>'delta_balance')::int, 0)) AS balance,
    LAST((data->>'credit')::int) FILTER (WHERE type = 'credit_set') AS credit
FROM
    event
GROUP BY
    account; 
```

我发现这种方法的主要问题是顺序似乎是任意的。First 和 last 只能在某种顺序的上下文中定义。我无法找到一种方法来提供一个排序字段，所以我认为这种方法对于这个用例来说是有缺陷的。

* * *

正如我所怀疑的，这个用例非常适合定制聚合和扩展，事实上，[Reddit](https://www.reddit.com/r/PostgreSQL/comments/cpskf7/how_to_get_the_first_or_last_value_in_a_group/ewudq11/)上的另一位读者给我[指出了扩展“first _ last”](https://pgxn.org/dist/first_last)。该 API 大致类似于上面的自定义聚合，但它也提供了一种对结果进行排序的方法，因此第一个和最后一个不是任意的。

我没有安装扩展，但是查询应该是这样的:

```
SELECT
    account,
    SUM(COALESCE((data->>'delta_balance')::int, 0)) AS balance,
    LAST((data->>'credit')::int, 1 ORDER BY happened_at)
        FILTER (WHERE type = 'credit_set') AS credit
FROM
    event
GROUP BY
    account; 
```