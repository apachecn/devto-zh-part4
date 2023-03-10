# 使用 SQL 窗口函数的时间序列可视化

> 原文：<https://dev.to/homam/time-series-visualization-173c>

我们这个月销售了多少？

回答这个问题的一种方法是找出我们在当前日历月的销售总额:

```
SELECT COUNT(*) FROM sales 
WHERE DATE_TRUNC('month', sale_date) = DATE_TRUNC('month', NOW()) 
```

该查询返回`sales`表中其`sale_date`值在当前日历月中的行数。

同样，我们可以计算上个月的销售额:

```
SELECT COUNT(*) FROM sales 
WHERE DATE_TRUNC('month', sale_date) = DATE_TRUNC('month', NOW() - '1 month' :: INTERVAL) 
```

该查询返回`sales`表中其`sale_date`值在前一个日历月内的行数。

根据今天是哪个月的哪一天，你可能会注意到上个月我们的销售额比这个月多得多。我们当然可以解释它，因为我们总是在这个月的中旬，而这个月还没有结束。

如果将当前月份的含义解释为过去 30 天，而不是一个日历月，我们可以避免这个问题

```
SELECT COUNT(*) FROM sales 
WHERE sale_date >= NOW() - '1 month' :: INTERVAL 
```

在这篇文章中，我认为这种解释更适合数据可视化和趋势分析。

我们还探索了一些生成样本数据集和使用 SQL 窗口函数的技术。

* * *

## 生成数据

让我们从生成一个(虽然很无聊)样本数据集开始。

我们使用 PostgreSQL 的`generate_series(lower, upper, step)`函数来创建样本。例如，这个代码片段生成了一个从一年前到现在每小时一次的时间序列:

```
SELECT * FROM generate_series(NOW(), NOW() - '1 year' :: INTERVAL , '-1 hour') AS sale_date; 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34649)

这里，我们假设在过去的一年中，我们每小时都有一次销售(这种假设使这个数据集变得乏味，不过我们将在后面检查一种更高级的数据生成技术),但这足以让我们指出不同月份的长度。:

```
WITH sales AS (
   SELECT generate_series(NOW(), NOW() - '1 year' :: INTERVAL , '-1 hour') AS sale_date
)

SELECT 
  DATE_TRUNC('month', sale_date) AS sale_month
, EXTRACT(DAY FROM DATE_TRUNC('month', sale_date + '1 month' :: INTERVAL) - DATE_TRUNC('month', sale_date)) AS month_length
, COUNT(*) 
FROM sales 
GROUP BY sale_month, month_length
ORDER BY sale_month 
```

表达式被称为公共表表达式或 cte。我们用它们来封装我们的逻辑。

我避免在本文中创建临时表，这是 cte 派上用场的地方。

这里，`WITH sales AS ...`创建了一个虚拟的，`sales`表，其中有一列:`sale_date`，在查询执行期间，这个表驻留在内存中。

这是查询的结果(在 [SQL Fiddle](http://sqlfiddle.com/#!17/9eecb/34591) 中试试)

```
+------------+--------------+---------+
| sale_month | month_length | count   |
|------------+--------------+---------|
| 2018-08-01 | 31           | 659     |
| 2018-09-01 | 30           | 720     |
| 2018-10-01 | 31           | 745     |
| 2018-11-01 | 30           | 720     |
| 2018-12-01 | 31           | 744     |
| 2019-01-01 | 31           | 744     |
| 2019-02-01 | 28           | 672     |
| 2019-03-01 | 30           | 743     |
| 2019-04-01 | 30           | 720     |
| 2019-05-01 | 31           | 744     |
| 2019-06-01 | 30           | 720     |
| 2019-07-01 | 31           | 744     |
| 2019-08-01 | 31           | 86      |
+------------+--------------+---------+ 
```

忽略第一个月和最后一个月，每个月的销售数量与该月的天数直接相关。二月通常是有问题的。

[https://codepen.io/homam_/embed/xvPmoO?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/xvPmoO?height=600&default-tab=result&embed-version=2)

为了改进我们的分析，让我们利用我们对“月”的另一种解释，即过去 30 天。

以下查询在数据集中查找过去 30 天每天的平均销售额:

```
WITH sales AS (
   SELECT generate_series(NOW(), NOW() - '1 year' :: INTERVAL , '-1 hour') AS sale_date
)
, 
daily_sales AS (
  SELECT 
    DATE_TRUNC('day', sale_date) AS sale_day
  , COUNT(*) AS sales 
  FROM sales 
  GROUP BY sale_day
  ORDER BY sale_day
)
, 
daily_avgs as (
  SELECT 
    sale_day
  , SUM(sales) OVER W
  , AVG(sales) OVER W
  FROM daily_sales
  WINDOW W as (ORDER BY sale_day ROWS BETWEEN 29 PRECEDING AND CURRENT ROW)
)

SELECT * FROM daily_avgs
ORDER BY sale_day DESC 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34598)

这里有很多东西要解开，但我们先来看看结果:

```
+------------+-------+------+
| sale_day   | sum   | avg  |
|------------+-------+------|
| 2019-08-05 | 710   | 23.7 |
| 2019-08-04 | 720   | 24.0 |
| 2019-08-03 | 720   | 24.0 |
| 2019-08-02 | 720   | 24.0 |
| 2019-08-01 | 720   | 24.0 |
| 2019-07-31 | 720   | 24.0 |
| 2019-07-30 | 720   | 24.0 |
| 2019-07-29 | 720   | 24.0 |
| 2019-07-28 | 720   | 24.0 |
... 
```

每一行的总和都是`24 * 30 = 720`，但是最新的一行。问题当然是因为今天还没完。我们的数据集中从来没有完整的 24 小时。

注意，我们首先创建了一个`daily_sales` CTE:

```
daily_sales AS (
  SELECT 
    DATE_TRUNC('day', sale_date) AS sale_day
  , COUNT(*) AS sales 
  FROM sales 
  GROUP BY sale_day
  ORDER BY sale_day
) 
```

这基本上是我们每天销售数量的时间序列。

我们正在统计下一届 CTE ( `daily_avgs` ):
中的`daily_sales` CTE

```
daily_avgs as (
  SELECT 
    sale_day
  , SUM(sales) OVER W
  , AVG(sales) OVER W
  FROM daily_sales
  WINDOW W as (ORDER BY sale_day ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) 
```

`WINDOW W as (ORDER BY sale_day ROWS BETWEEN 29 PRECEDING AND CURRENT ROW)`创建一个 30 行宽的窗口(名为`W`)。我们可以将我们的窗口命名为类似(`W30`或`my_window`或别的什么)我选择一个简单的字母`W`，因为在这个查询中我们只有一个窗口。

这里我们说，对于我们的数据集中的每一行，选择该行的`sale_day`，并且`sum`选择在当前行之前 29 行并且包括当前行(总共 30 行)的`sale_day`之间发生的所有`sales`。

我们可以大致把这个窗口表达式翻译成类似 C 的语言如下:

```
const indexed_daily_sales = daily_sales
  .sortBy(r => r.sale_day) // ORDER BY sale_day
  .map((r, index) => ({...r, index}))
foreach(row in indexed_daily_sales) {
  yield indexed_daily_sales
    .filter(r => r.index >= row.index - 29 and r.index <= row.index) // ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
    .sum(x => x.sales) // SUM(sales)

 // similarly for AVG
} 
```

如果您想从结果中排除今天的数据，请在最后过滤掉今天。

这是一个时间序列的可视化结果:

[https://codepen.io/homam_/embed/xvPMVR?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/xvPMVR?height=600&default-tab=result&embed-version=2)

* * *

## SQL 窗口函数

让我们更深入地探讨一下 SQL 中窗口的概念。

如果我想对 1 到 10 之间的所有整数求和，我使用这个查询:

```
SELECT SUM(id) FROM generate_series(1, 10) id 
```

结果只是一个只有一个数字的行:55。

但是如果我想找出从 1 到 N 的所有整数的和

```
SELECT id, SUM(id) OVER (ORDER BY id) FROM generate_series(1, 10) id 
```

结果有 10 行:

```
+------+-------+
| id   | sum   |
|------+-------|
| 1    | 1     | = 1
| 2    | 3     | = 1 + 2
| 3    | 6     | = 1 + 2 + 3
| 4    | 10    | = 1 + 2 + 3 + 4
| 5    | 15    | = 1 + 2 + 3 + 4 + 5
| 6    | 21    | = 1 + 2 + 3 + 4 + 5 + 6
| 7    | 28    | = 1 + 2 + 3 + 4 + 5 + 6 + 7
| 8    | 36    | = 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8
| 9    | 45    | = 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9
| 10   | 55    | = 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10
+------+-------+ 
```

每行中的 sum 字段对应于该行中从 1 到`id`的整数之和。

这里`OVER (ORDER BY id)`创建一个包含当前行的所有行的窗口。它相当于:`OVER (ORDER BY id ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)`

如果我们只使用一次窗口，就不必给它命名。

```
SELECT 
  SUM(id) OVER (ORDER BY id)
FROM ... 
```

相当于:

```
SELECT 
  SUM(step_size) OVER W
FROM ...
WINDOW W AS (ORDER BY id) 
```

让我们修改这个查询来返回每个数字周围的三个整数的和，例如 5 的行应该返回:`4 + 5 + 6 = 15`

```
SELECT 
  id
, SUM(id) OVER (ORDER BY id ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) 
FROM generate_series(1,10) id 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34765)

```
+------+-------+
| id   | sum   |
|------+-------|
| 1    | 3     | =   + 1 + 2 = 3
| 2    | 6     | = 1 + 2 + 3 = 6
| 3    | 9     | = 2 + 3 + 4 = 9
| 4    | 12    | = 3 + 4 + 5 = 12
| 5    | 15    | = 4 + 5 + 6 = 15
| 6    | 18    | = 5 + 6 + 7 = 18
| 7    | 21    | = 6 + 7 + 8 = 21
| 8    | 24    | = 7 + 8 + 9 = 24
| 9    | 27    | = 8 + 9 + 10 = 27
| 10   | 19    | = 9 + 10 +   = 19
+------+-------+ 
```

除了结果中的第一项和最后一项之外，窗口的大小为 3。

这通常是这样的情况，结果的开始和/或结尾的窗口尺寸小于中间的。

在 windows 上运行的聚合函数知道这一点。例如，检查三个相邻数字的平均值(必须是中间的那个):

```
SELECT 
  id
, SUM(id) OVER W
, AVG(id) OVER W
FROM generate_series(1, 10) id 
WINDOW W AS (ORDER BY id ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34727)

```
+------+-------+-----+
| id   | sum   | avg |
|------+-------+-----|
| 1    | 3     | 1.5 | = (    1 + 2) / 2 = 1.5 (note division by 2)
| 2    | 6     | 2.0 | = (1 + 2 + 3) / 3 = 2.0 (note division by 3)
| 3    | 9     | 3.0 | = (2 + 3 + 4) / 3 = 3.0
| 4    | 12    | 4.0 | = (3 + 4 + 5) / 3 = 4.0
| 5    | 15    | 5.0 | = (4 + 5 + 6) / 3 = 5.0
| 6    | 18    | 6.0 | = (5 + 6 + 7) / 3 = 6.0
| 7    | 21    | 7.0 | = (6 + 7 + 8) / 3 = 7.0
| 8    | 24    | 8.0 | = (7 + 8 + 9) / 3 = 8.0
| 9    | 27    | 9.0 | = (8 + 9 + 10) / 3 = 9.0
| 10   | 19    | 9.5 | = (9 + 10    ) / 2 = 9.5 (note division by 2)
+------+-------+-----+ 
```

根据你的分析，你可能需要考虑这个事实。

对我们来说，这意味着我们的时间序列中的平均销售额在开始时有较少的数据点，因此`AVG(sales) OVER W`在图表的左侧会更嘈杂。

一个简单的解决方法是通过偏移我们的结果来忽略开始时的数据点。在查询末尾使用`OFFSET 29`([SQL Fiddle](http://sqlfiddle.com/#!17/9eecb/34734))。

* * *

## 随机漫步

我们需要一个更现实的数据集，以便将我们上面讨论的一切投入使用。到目前为止，我们的销售数据集非常枯燥，因为我们使用均匀分布(每小时一次销售)来创建我们的样本数据集。

在这里，我们探索一种生成更真实的销售数据集的方法。

[随机漫步](https://en.wikipedia.org/wiki/Random_walk)就是你想的那样。在二维空间中，你可以想象一只小海龟在水面上完全随机地选择下一步的方向。

[https://codepen.io/homam_/embed/QeOeVV?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/QeOeVV?height=600&default-tab=result&embed-version=2)

在一维空间中，我们只能上下移动。

[https://codepen.io/homam_/embed/RXxwBw?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/RXxwBw?height=600&default-tab=result&embed-version=2)

```
SELECT step_id, (FLOOR((RANDOM() * 3) - 1)) AS step_size
FROM generate_series(1,6000) step_id 
```

```
+-----------+-------------+
| step_id   | step_size   |
|-----------+-------------|
| 1         | 1.0         |
| 2         | 0.0         |
| 3         | 1.0         |
| 4         | 1.0         |
| 5         | 0.0         |
| 6         | -1.0        |
... 
```

这个代码片段生成了-1、0 和 1`step_size`的统一分布。

这些数字模拟了我们的光标在每一步向下、向上或根本不移动的情况。

每一步前面所有`step_size`的总和决定了我们从原点出发所走过的总距离。

```
SELECT 
  SUM(step_size) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS pos
  FROM (
    SELECT step_id, floor((random() * 3) - 1) AS step_size
    FROM generate_series(1,6000) step_id
  ) _a 
```

```
+-----------+-------------+-------+
| step_id   | step_size   | pos   |
|-----------+-------------|-------|
| 1         | 1.0         | 1.0   |
| 2         | 0.0         | 1.0   | = 1 + 0
| 3         | 1.0         | 2.0   | = 1 + 0 + 1
| 4         | 1.0         | 3.0   | = 1 + 0 + 1 + 1
| 5         | 0.0         | 3.0   | = 1 + 0 + 1 + 1 + 0
| 6         | -1.0        | 2.0   | = 1 + 0 + 1 + 1 + 0 - 1
... 
```

如果你想指定一个起点，使用`UNION`:

```
SELECT 0 as step_id, 600 as step_size
UNION
SELECT step_id, floor((random() * 3) - 1) AS step_size
FROM generate_series(1,6000) step_id
ORDER BY step_id 
```

我们使用随机漫步来生成看起来更真实的数据集。这里的想法是，每天的销售额不是完全随机的，但它实际上接近于我们前一天的销售额加上或减去一些随机值，我们称之为噪声。

```
WITH noise AS (
  SELECT 
    step_id
  , date_trunc('day', NOW()) - (step_id || ' day') :: INTERVAL AS sale_day
  , SUM(step_size) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS pos
    FROM (
      SELECT step_id, (FLOOR((RANDOM() * 3) - 1)) * FLOOR(RANDOM() * 100) AS step_size
      FROM generate_series(1,1000) step_id
    ) _a
)
,
daily_sales AS (
  SELECT
    sale_day
  , (CASE WHEN EXTRACT(DAY FROM sale_day) < 8 
      THEN FLOOR(RANDOM() * 200) 
      ELSE 0 END
    ) + (SELECT ABS(MIN(pos)) FROM noise) + pos AS sales

  FROM noise  
  ORDER BY step_id DESC
)

SELECT 
  sale_day
, sales
, AVG(sales) OVER (ORDER BY sale_day ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS avg_daily_sales
FROM daily_sales 
```

`noise`是介于-99 和 99 之间的一系列随机数:

```
SELECT * FROM noise

+-----------+------------+--------+
| step_id   | sale_day   | pos    |
|-----------+------------+--------|
| 1         | 2019-08-04 | 48.0   |
| 2         | 2019-08-03 | 48.0   |
| 3         | 2019-08-02 | 48.0   |
| 4         | 2019-08-01 | 72.0   |
| 5         | 2019-07-31 | 72.0   |
| 6         | 2019-07-30 | 159.0  |
| 7         | 2019-07-29 | 252.0  |
... 
```

在这个模型中，我们期望我们的销售额与前一天相比随机变化 99。

`daily_sales`通过首先确保所有数据点都大于零来调整噪声系列:`+ (SELECT ABS(MIN(pos)) FROM noise)`。我们还为这个系列增加了一些季节性(我们假设我们的销售额在这个月的第一周每天最多增加 200 件):

```
(CASE WHEN EXTRACT(DAY FROM sale_day) < 8 
   THEN FLOOR(RANDOM() * 200) 
   ELSE 0 END
) 
```

让我们来看看最后的结果:

```
+------------+---------+-------------------+
| sale_day   | sales   | avg_daily_sales   |
|------------+---------+-------------------|
| 2016-11-08 | 1074.0  | 1074.0            |
| 2016-11-09 | 1068.0  | 1071.0            |
| 2016-11-10 | 1118.0  | 1086.66666666667  |
| 2016-11-11 | 1118.0  | 1094.5            |
| 2016-11-12 | 1112.0  | 1098.0            |
| 2016-11-13 | 1177.0  | 1111.16666666667  |
| 2016-11-14 | 1145.0  | 1116.0            |
| 2016-11-15 | 1117.0  | 1116.125          |
... 
```

健全性检查:

`avg_daily_sales`在`2016-11-11` = `(1074 + 1068 + 1118 + 1118) / 4 = 1,094.5`

任何窗口中的最大行数是 30。当然，在开始的时候，我们在窗口中只有不到 30 行，因为在`2016-11-08`之前没有行。

[https://codepen.io/homam_/embed/GVyJqd?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/GVyJqd?height=600&default-tab=result&embed-version=2)

将我们最新的图表与我们最初天真的尝试进行比较。

如果您喜欢按月统计，可以过滤掉查询末尾的行:

```
...
daily_avgs AS (
  SELECT 
    sale_day
  , sales
  , SUM(sales) OVER (ORDER BY sale_day ROWS BETWEEN 29 PRECEDING AND 
  CURRENT ROW) AS avg_monthly_sales
  FROM daily_sales
)

SELECT sale_day, avg_monthly_sales 
FROM daily_avgs
WHERE EXTRACT(DAY FROM sale_day) = EXTRACT(DAY FROM NOW() - '1 day' :: INTERVAL)
ORDER BY sale_day DESC 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34610)

[https://codepen.io/homam_/embed/qepdzo?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/qepdzo?height=600&default-tab=result&embed-version=2)

每个条形代表与条形相关联的日期之前 30 天(包括该日期)内的销售总额。

* * *

## 缺失值

通常我们需要处理时间序列中的间隙。在我们的例子中，如果那天没有销售，那么`daily_sales`中的行将会丢失。

到目前为止，在我们生成的数据集中，我们通过用随机行走生成的最小距离的绝对值填充数据来避免零销售和负销售。

为了产生一些间隙，让我们首先将这个填充减少到一半`+ (SELECT ABS(ROUND(MIN(pos)/2)) FROM noise)`，然后过滤掉所有零销售额或负销售额的行:`SELECT * FROM daily_sales_1 WHERE sales > 0` :

```
WITH noise AS (
  SELECT 
    step_id
  , DATE_TRUNC('day', NOW()) - (step_id || ' day') :: INTERVAL as sale_day
  , SUM(step_size) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS pos
    FROM (
      SELECT 0 as step_id, 0 as step_size
      UNION
      SELECT step_id, (floor((random() * 3) - 1)) * floor(random() * 100) AS step_size
      FROM generate_series(1,1000) step_id
      ORDER BY step_id
    ) _a
)
,
daily_sales_1 AS (
  SELECT
    sale_day
  , (CASE WHEN EXTRACT(DAY FROM sale_day) < 8 
      THEN floor(random() * 200) 
      ELSE 0 END
    ) + (SELECT ABS(ROUND(MIN(pos)/2)) FROM noise) + pos AS sales

  FROM noise  
  ORDER BY step_id DESC
)
,
daily_sales AS (
  SELECT * FROM daily_sales_1 where sales > 0
)
,
calendar AS (
  SELECT generate_series(
      (SELECT min(sale_day) from daily_sales_1)
    , (SELECT max(sale_day) from daily_sales_1)
    , '1 day' :: INTERVAL
  ) as sale_day
)

SELECT 
  calendar.sale_day
, COALESCE(sales, 0) AS sales
, AVG(COALESCE(sales, 0)) OVER (ORDER BY calendar.sale_day ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS avg_daily_sales
FROM calendar
LEFT JOIN daily_sales ON calendar.sale_day = daily_sales.sale_day 
```

[SQL 拨弄](http://sqlfiddle.com/#!17/9eecb/34759)

这是结果:

[https://codepen.io/homam_/embed/vopLgG?height=600&default-tab=result&embed-version=2](https://codepen.io/homam_/embed/vopLgG?height=600&default-tab=result&embed-version=2)

`calendar` CTE 生成一系列日期

```
calendar AS (
  SELECT generate_series(
      (SELECT min(sale_day) from daily_sales_1)
    , (SELECT max(sale_day) from daily_sales_1)
    , '1 day' :: INTERVAL
  ) as sale_day
) 
```

我们从`calendar`中选择`sale_day` s，并在最后一步将其与`daily_sales`表连接。

`sales`是由于左连接而在`daily_sales`表中缺失的行的`null`。这就是为什么我们使用`COALESCE(sales, 0)`将缺失数据点的空值转换为 0。

一般来说，null 不等于 0。所以要小心。但是，当我们处理时间序列中的间隙时，我们可以将空值或缺失数据点转换为 0。