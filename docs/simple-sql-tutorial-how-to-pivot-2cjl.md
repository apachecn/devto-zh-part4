# 简单 SQL 教程:如何透视

> 原文：<https://dev.to/erikwhiting88/simple-sql-tutorial-how-to-pivot-2cjl>

透视数据只是以不同的方式显示已有数据的一种练习。它非常有用，但也会让人感到困惑。我希望任何努力编写完美旋转查询的人都能阅读这篇文章，并尽快得到他们需要的答案，所以让我们开始吧！

## 但是为什么呢？

我们为什么要这样做？我准备的这个例子看起来有点奇怪，但是我用一些容易理解的想法来说明一些复杂的事情。现实世界中你会遇到这样的问题，你就忍忍吧。

## 数据

好的，我有一个名为`FoodEaten`的表格，记录我每天吃的食物。我的数据是这样的:

```
SELECT * FROM FoodEaten;
-- Results:
Id  | Date       | FoodName | AmountEaten |
--- |------------|----------|-------------|
1   | 2019-08-01 | Sammich  |   2         |
2   | 2019-08-01 | Pickle   |   3         |
3   | 2019-08-01 | Apple    |   1         |
4   | 2019-08-02 | Sammich  |   1         |
5   | 2019-08-02 | Pickle   |   1         |
6   | 2019-08-02 | Apple    |   4         |
7   | 2019-08-03 | Cake     |   2         |
8   | 2019-08-04 | Sammich  |   1         |
9   | 2019-08-04 | Pickle   |   2         |
10  | 2019-08-04 | Apple    |   3         |
----|------------|----------|-------------| 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以看到，我的饮食没有太大变化，除了第三天我吃了两整块蛋糕。我们姑且称之为作弊日吧😉

如果我想每天都看到这些信息，而不是单个的食物消费记录，那该怎么办？如果不是现在的呈现方式，我更愿意看到三明治、泡菜、苹果作为栏目呢？即使这些东西是列中的值，我也可以这样做吗？你赌你可爱的脸我可以，用一个小魔术叫`PIVOT`
[![image](img/0e92cd7c163bd6438dd9c793ff6ad81b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6GBdHDJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hips.hearstapps.com/hmg-prod.s3.amazonaws.cimg/ross-geller-pivot-1534246982.jpg%3Fcrop%3D1xw:0.9881266490765171xh%3Bcenter%2Ctop%26resize%3D480:%2A)

## 枢轴语法

下面的语法将出现在`T-SQL`中，这是微软的 SQL Server 使用的 SQL 方言。我有大约 98.7%的把握这在 SQL 的 Oracle/MySQL 风格的`PL-SQL`中也能工作。

语法背后的基本概念是:

```
SELECT [Data You Break Up The Other Datas With] AS 'a cool name',
[DataPoint1], [DataPoint2], [DataPointEtc]
FROM (
    SELECT [The], [Columns], [With], [Data]
) AS SourceTable
PIVOT (
    AGGREGATE_FUNCTION([Column with data you want displayed in rows])
    FOR [Column in which "The Columns With Data" live] IN (
        [DataPoint1], [DataPoint2], [DataPointEtc]
    )
) AS PivotTable 
```

Enter fullscreen mode Exit fullscreen mode

没有任何意义吗？没事，看个真的:

```
SELECT [Date] AS 'Day',
[Sammich], [Pickle], [Apple], [Cake]
FROM (
    SELECT [Date], FoodName, AmountEaten FROM FoodEaten
) AS SourceTable
PIVOT (
    MAX(AmountEaten)
    FOR FoodName IN ([Sammich], [Pickle], [Apple], [Cake])
) AS PivotTable 
```

Enter fullscreen mode Exit fullscreen mode

易脆柠檬 SQ(L)ueezy。看看结果:

```
|     DAY     | Sammich | Pickle | Apple | Cake |
|-------------|---------|--------|-------|------|
| 2019-08-01  |    2    |    3   |   1   | NULL |
| 2019-08-02  |    1    |    1   |   4   | NULL |
| 2019-08-03  |  NULL   |  NULL  | NULL  |   2  |
| 2019-08-04  |    1    |    2   |   3   | NULL |
|-------------|---------|--------|-------|------| 
```

Enter fullscreen mode Exit fullscreen mode

你明白这是怎么回事了吗？它接受您提供的数据点并从中生成列，并显示您指定为行值的另一列的值。

注意，在透视后的列上需要一个聚合函数(以确保返回一条记录)。如果你不做任何数学运算，使用`MAX()`通常是相当安全的。您还可以在包含日收入、月平均销售额等数字的列上使用`AVG()`或`SUM()`。但这次我们只是在谈论我糟糕透顶的饮食。

我不太喜欢这张桌子的一点是所有的`NULL` s。如果我没有吃任何蛋糕，我不想看到“NULL ”,如果我没有吃任何苹果，我应该被羞辱为 0。下面是最基本的解决方法:

```
SELECT [Date] AS 'Day',
    ISNULL([Sammich], 0) AS Sammich,
    ISNULL([Pickle],  0) AS Pickle, 
    ISNULL([Apple],   0) AS Apple,
    ISNULL([Cake],    0) AS Cake
FROM (
    SELECT [Date], FoodName, AmountEaten FROM FoodEaten
) AS SourceTable
PIVOT (
    MAX(AmountEaten)
    FOR FoodName IN (
        [Sammich], [Pickle], [Apple], [Cake]
    )
) AS PivotTable 
```

Enter fullscreen mode Exit fullscreen mode

你问的是什么样子？看哪！

```
|     DAY     | Sammich | Pickle | Apple | Cake |
|-------------|---------|--------|-------|------|
| 2019-08-01  |    2    |    3   |   1   |   0  |
| 2019-08-02  |    1    |    1   |   4   |   0  |
| 2019-08-03  |    0    |    0   |   0   |   2  |
| 2019-08-04  |    1    |    2   |   3   |   0  |
|-------------|---------|--------|-------|------| 
```

Enter fullscreen mode Exit fullscreen mode

## 好了

这是你的基本原则，如果你有任何问题，请告诉我。