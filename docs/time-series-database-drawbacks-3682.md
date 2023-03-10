# 时间序列数据库的缺点

> 原文：<https://dev.to/saulsilver/time-series-database-drawbacks-3682>

## 用例

最近，我需要在应用程序上创建一个分析页面，以计算一家商店有多少双鞋。数据库设置是 MongoDB，所以我寻找一个适合分析的良好的数据库设计。

我的分析是

1.  鞋的年、月、日计数
2.  鞋子有制造商的名字和颜色

我搜索了一下，从这篇[文章](https://www.mongodb.com/blog/post/time-series-data-and-mongodb-part-1-introduction)发现时间序列可以在 MongoDB 上做。尽管如此，我还需要一点更可靠的产品级教程，幸运的是我在开发者社区上的[这里找到了令人敬畏的里卡多·卡丁的教程！](https://dev.to/riccardo_cardin/implementing-time-series-in-mongodb)

## 解

假设我们有鞋子柜台的数据集，我开始实现它，它已经准备好了。

```
Dataset

Makers: ['Nike', 'Adidas', 'Onitsuka Tiger']
Colors: ['White', 'Black', 'Red'] 
```

Enter fullscreen mode Exit fullscreen mode

我设置分析数据库的方法是让集合`Shoes`包含一个文档/年份。当文档首次创建时，所有计数器都设置为 0，然后每天递增。

```
DB Shoes Collection document 

{
  created_at: ISODate("2019-01-01T00:00:10.000Z"),
  year: 2019,
  nike: 15000,
  adidas: 10000,
  tiger: 8000,
  black: 25000,
  white: 1000,
  red: 8000,
  // months is an array of 12 elements
  months: [
    {
      created_at: ISODate("2019-01-01T00:00:10.000Z"),
      nike: 200,
      adidas: 1000,
      tiger: 80,
      black: 200,
      white: 1000,
      red: 80,
      // days is an array of days/month
      days: [
        {
          created_at: ISODate("2019-01-01T00:00:10.000Z"),
          nike: 20,
          adidas: 100,
          tiger: 8,
          black: 120,
          white: 8,
          red: 0,
        }
         // rest of days
      ]
    }
    // rest of months
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 优点

*   这种结构非常强大，因为文档大小在创建后不会改变，所以 IO 处理成本不高，文档大小大约为 35 Kbs
*   乍一看，获取文档并得到您需要的东西(例如，2019 年 2 月 8 日的耐克鞋号码)可能很棘手，但是由于函数式编程，创建一个函数并重用它
*   结构整洁，组织有序。

### 弊端

假设我们想在图表上使用一些过滤来查找 2019 年 4 月 15 日的黑色阿迪达斯鞋数。上面的数据库结构没有给我们任何关于重叠连接计数器的信息，它们只是原子计数器。

我能想到的唯一解决办法就是用每一个可能的关节创造新的计数器。这绝对是一个可怕的想法，因此，决定暂时放下时间序列数据库，为这个用例找到另一个设计。