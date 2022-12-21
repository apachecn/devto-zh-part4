# SQL:像老板一样汇总

> 原文：<https://dev.to/griffinator76/rollup-like-a-boss-3dkl>

所以，在读完海伦的伟大文章后:

[![helenanders26 image](img/b2665a386504462a087e6bc1a2927708.png)](/helenanders26) [## 我的 52 周记录:好的，坏的，丑陋的

### 海伦安德森 9 月 25 分钟阅读

#devjournal #motivation #writing #showdev](/helenanders26/my-52-week-streak-the-good-the-bad-the-ugly-2nmj)

我决定是时候给 DEV 写一两篇文章了，所以我的第一个努力是——看看 SQL 的一个未被充分利用的特性，使用 ROLLUP 和 CUBE 操作符向结果添加总计和小计的能力。

首先，免责声明。SQL 语言是由 ISO 标准(9075)定义的，但是和许多“标准”一样，很少有实现完全遵循这个标准。一些数据库系统省略了一些特性，另一些添加了自己的扩展。也就是说，以下内容适用于您可能使用的许多常见数据库平台，如 Microsoft SQL Server、Oracle、PostgreSQL 和 Presto/AWS Athena。

# SQL 分组通过 101

一个简单的`SELECT * FROM <table>;` SQL 语句将从表中返回数据行，其形式在概念上与这些数据行和列在表中的存储方式相匹配。当设计一个使用数据库的*应用程序*时，您通常会希望像这样处理数据，查询要显示的行，或者用新信息添加或更新行。

然而，当使用 SQL 进行*分析*时，我们通常不希望表中的每一行都有信息。我们希望对其进行汇总，例如检索其中一列中值的总和或平均值:

```
SELECT SUM(UnitsSold) AS TotalUnitsSold
FROM Orders; 
```

| TotalUnitsSold |
| --- |
| Five hundred and fifty |

太好了！所以现在我们有了整个表的合计值。但是更实际的是，我们不仅仅想要表的总数，我们想要其他列中每个值的总数，比如`Supplier`。所以我们这样做:

```
SELECT Supplier, SUM(UnitsSold)  AS TotalUnitsSold
FROM Orders
GROUP BY Supplier; 
```

| 供应者 | TotalUnitsSold |
| --- | --- |
| 旺卡的巧克力工厂 | One hundred |
| 快马独立交易 | Two hundred |
| 快易购 | Two hundred and fifty |

我们已经使用 SQL 语句的`GROUP BY`子句告诉数据库对于`Supplier`的每个唯一值，将`UnitsSold`列的值告诉给`SUM`。

我们可以将它扩展到按两列或更多列分组，这样我们就可以得到分组列中每个唯一值组合的总数:

```
SELECT Supplier, City, SUM(UnitsSold)  AS TotalUnitsSold
FROM Orders
GROUP BY Supplier, City; 
```

| 供应者 | 城市 | TotalUnitsSold |
| --- | --- | --- |
| 旺卡的巧克力工厂 | 慕尼黑 | One hundred |
| 快马独立交易 | 纽约 | Ten |
| 快马独立交易 | 巴黎 | Twenty |
| 快马独立交易 | 佩卡姆 | One hundred and seventy |
| 快易购 | 斯普林菲尔德 | One hundred and fifty |
| 快易购 | 谢尔比维尔 | One hundred |

很好。现在我们已经有了快速报告或数据摘录的基础。

等等,“快马独立交易”卖出了多少台？现在我们已经添加了多个分组级别(供应商->城市)，我们必须再次将它们相加，以获得更高分组级别(例如供应商)的总计。

如果您要将此查询放入 Tableau、Power BI 或 MicroStrategy 等报告或可视化工具中，那么您的工作可能已经完成，因为这些工具可以配置为在显示数据时为您计算总数。

然而，如果你只是想从数据库中快速得到一些答案(老板想要那些每月的销售数据！)然后你要么将数据导出到另一个工具如 Excel 来计算，或者，可怕的是，你自己手动计算总数来附加到结果中(我见过这样做)。如果您更有信心，您可以尝试创建具有不同分组级别的多个 SQL 语句，并使用`UNION ALL`将它们附加在一起。

# 汇总，汇总！马戏团在城里

不要惊慌，SQL 在这里通过经常被忽略的 ROLLUP 和 CUBE 子句来拯救您的理智。本质上，这些 SQL 语句子句允许您做的是让数据库为您完成繁重的工作，并在同一组结果中计算总计和小计。

让我们回到我们的例子，假设我们想要得到每个`Supplier`的小计和所有订单的总计，那么我们将`ROLLUP`添加到我们的`GROUP BY`子句:

```
SELECT Supplier, City, SUM(UnitsSold)  AS TotalUnitsSold
FROM Orders
GROUP BY ROLLUP(Supplier, City); 
```

| 供应者 | 城市 | TotalUnitsSold |
| --- | --- | --- |
| 旺卡的巧克力工厂 | 慕尼黑 | One hundred |
| 旺卡的巧克力工厂 | 空 | One hundred |
| 快马独立交易 | 纽约 | Ten |
| 快马独立交易 | 巴黎 | Twenty |
| 快马独立交易 | 佩卡姆 | One hundred and seventy |
| 快马独立交易 | 空 | Two hundred |
| 快易购 | 斯普林菲尔德 | One hundred and fifty |
| 快易购 | 谢尔比维尔 | One hundred |
| 快易购 | 空 | Two hundred and fifty |
| 空 | 空 | Five hundred and fifty |

注意，我们现在为每个`Supplier`增加了一行，在`City`列中有一个`NULL`值。这表示每个供应商销售的所有单位的总和。底部还有一行包含供应商和城市的`NULL`值，这是所有供应商和所有城市的 UnitsSold 合计(即整个表的合计)——注意这与我们编写的第一个简单 SUM 查询中的合计相匹配。

# 原来如此？

这很好，因为我们现在有了我们的小计，但是所有这些`NULL`值使它有点难以阅读(并且会使任何非 SQL 人感到困惑)。幸运的是*大多数*数据库平台也实现了一个**功能**叫做分组(抱歉 Presto/Athena 用户，你在这一点上是不幸的)。该函数接受一个列名，并简单地返回 1 (true)或 0 (false)来指示当前行是否包含该列的小计，例如，如果该行包含供应商的小计，则`GROUPING(Supplier)`将返回 1，如果是常规行，则返回 0。

我们可以用这个分组函数做的一件事是，通过用更有意义的东西来替换那些`NULL`值，使我们的结果看起来更清晰

```
SELECT 
   CASE 
      WHEN GROUPING(Supplier) = 1 
         THEN '-All Suppliers-' 
      ELSE Supplier 
   END AS Supplier
   , CASE 
      WHEN GROUPING(City) = 1
         THEN '-All Cities-'
      ELSE City
    END AS City
   , SUM(UnitsSold)  AS TotalUnitsSold
FROM Orders
GROUP BY ROLLUP(Supplier, City); 
```

| 供应者 | 城市 | TotalUnitsSold |
| --- | --- | --- |
| 旺卡的巧克力工厂 | 慕尼黑 | One hundred |
| 旺卡的巧克力工厂 | -所有城市- | One hundred |
| 快马独立交易 | 纽约 | Ten |
| 快马独立交易 | 巴黎 | Twenty |
| 快马独立交易 | 佩卡姆 | One hundred and seventy |
| 快马独立交易 | -所有城市- | Two hundred |
| 快易购 | 斯普林菲尔德 | One hundred and fifty |
| 快易购 | 谢尔比维尔 | One hundred |
| 快易购 | -所有城市- | Two hundred and fifty |
| -所有供应商- | -所有城市- | Five hundred and fifty |

现在更容易阅读了！是时候把数字用电子邮件发给老板，然后去酒吧喝一杯庆祝了。

# 等等，你不是提到过什么立方体吗？

啊是的，立方体。老实说，CUBE 的用例比 ROLLUP 更少，但是为了以防万一，了解它是很好的。

CUBE 基本上是 roll up——roll up 根据分组列的顺序为分组的每个子级别创建小计(例如，在我们的示例中，有对`Supplier`和“所有行”的小计，但没有对`City`的小计，因为那是分组的“底部”级别)。

另一方面，CUBE 为分组列中值的每个组合创建分类汇总。

这有什么用？让我们再来看看我们的例子。到目前为止，这些例子一直假设供应商和城市之间有一个自然的层次结构，即一个供应商对多个城市(一对多或 1:N 关系)，但让我们说，实际上这些是订单的独立属性，因此一个供应商可以有多个城市，一个城市可以与多个供应商相关(多对多或 M:N 关系)。在这种情况下，我们不仅想要供应商的小计，我们还想要每个城市的小计*。*

只需在我们的 SQL 语句中用`CUBE`替换`ROLLUP`，我们现在得到这个结果:

```
SELECT 
   CASE 
      WHEN GROUPING(Supplier) = 1 
         THEN '-All Suppliers-' 
      ELSE Supplier 
   END AS Supplier
   , CASE 
      WHEN GROUPING(City) = 1
         THEN '-All Cities-'
      ELSE City
    END AS City
   , SUM(UnitsSold)  AS TotalUnitsSold
FROM Orders
GROUP BY CUBE(Supplier, City); 
```

| 供应者 | 城市 | TotalUnitsSold |
| --- | --- | --- |
| 旺卡的巧克力工厂 | 慕尼黑 | One hundred |
| 旺卡的巧克力工厂 | -所有城市- | One hundred |
| 快马独立交易 | 纽约 | Ten |
| 快马独立交易 | 巴黎 | Twenty |
| 快马独立交易 | 佩卡姆 | One hundred and seventy |
| 快马独立交易 | -所有城市- | Two hundred |
| 快易购 | 斯普林菲尔德 | One hundred and fifty |
| 快易购 | 谢尔比维尔 | One hundred |
| 快易购 | -所有城市- | Two hundred and fifty |
| -所有供应商- | 慕尼黑 | One hundred |
| -所有供应商- | 纽约 | Ten |
| -所有供应商- | 巴黎 | Twenty |
| -所有供应商- | 佩卡姆 | One hundred and seventy |
| -所有供应商- | 斯普林菲尔德 | One hundred and fifty |
| -所有供应商- | 谢尔比维尔 | One hundred |
| -所有供应商- | -所有城市- | Five hundred and fifty |

现在我们有了每个`Supplier`的小计，也有了每个`City`的单独小计，再加上总计！

# 妙极了！现在我要将 ROLLUP 添加到我的所有查询中！

哇哦，牛仔！正如我简单提到的，如果你正在编写一个将被 Tableau 之类的报告工具使用的查询，那么这种技术并没有真正的帮助，因为这些工具通常会为你做总计。事实上，这通常是一个[反模式](https://en.wikipedia.org/wiki/Anti-pattern)并且不可取，因为您的报告工具将无法区分小计行和常规行，这将使格式化您的报告更加困难。

但是，如果您直接在 SQL 中工作，并且只想为快速的即席报告或分析生成一些数据，那么 ROLLUP 或 CUBE 可以为您节省一些时间和精力来获得您需要的结果。