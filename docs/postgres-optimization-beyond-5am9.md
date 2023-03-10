# Postgres:优化与超越

> 原文：<https://dev.to/sathyasarathi90/postgres-optimization-beyond-5am9>

广泛使用的关系数据库管理系统之一 Postgres 由于其处理不同工作负载(如 web 服务、仓库等)的能力而被广泛采用。

> ***好玩的事实:****Postgres 这个名字来源于它的前身起源于 UC Berkley 的 Ingres 数据库(_ 交互式图形 iterchangE 系统；意思是后 INGRES* )。_

有些时候，性能是直截了当的，而在其他情况下，则没有达到预期的性能；数据库需要一些调整，如表的结构修改、查询调优、配置改进等。

这篇文章将为成为优化 Postgres 的超级用户提供一些有用的指导和行动计划。

当查询速度很慢时该怎么办？

<figure>[![](img/e7cd4a74d33e07dba781dc825bf40170.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zNRpr4eO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2Audxpi-nT0n98jkcu.gif) 

<figcaption>查询速度慢时该怎么办？</figcaption>

</figure>

在大多数情况下，出现缓慢的查询是因为缺少索引，这些索引用于查询的 where 子句中的那些字段。

这应该解决了问题，对不对？对吗？

你:

<figure>[![](img/bd7f3f86c57aa3994975d354d33eefc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f83H7hDE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2Ajjb-Ad2kwbCn-yAx.gif) 

<figcaption>根本顾不上。！！</figcaption>

</figure>

我听到了；生活是不公平的，不是吗？

并非 WHERE 子句中所有字段的索引都有帮助；这完全取决于优化器准备的适当的查询计划:在查询前面加上`EXPLAIN ANALYZE`,并运行它来查找查询计划。

**专业提示:**使用[https://explain.depesz.com/](https://explain.depesz.com/)来可视化和分析您的查询计划。彩色格式给出了一个直接的输出来调试缓慢的原因。

查询计划本身可以提供大量关于资源溢出的信息。下面给出了一些可以在查询计划中找到的关键字，以及它们对您和查询性能的意义。

**顺序扫描:**是的，你没看错。扫描顺序进行；过滤器对整个表运行，并返回符合条件的行，这可能会非常昂贵和详尽。在单页/小表的情况下，顺序扫描非常快。

但是对于较大的表；为了加快查询速度，顺序扫描需要改为**索引扫描**。这可以通过在 where 子句中的**列上创建索引来实现。**

**索引扫描/仅索引扫描:**索引扫描表示索引被正确使用。只要确保分析&吸尘偶尔发生一次。这将所有的死元组排除在外，并允许优化器为扫描选择正确的索引。

**位图索引扫描:**这就是令人失望的地方。位图索引扫描伴随着位图堆扫描。根据 where 子句中的多个逻辑条件，当试图检索多行而不是所有行时，通常会发生这些扫描。

它基本上是根据所提供的条件，从表的页面中创建一个位图(因此在顶部进行位图堆扫描)。可以通过创建一个**复合索引(也称为多列索引**)来加速查询；这会将该扫描更改为索引扫描。

**注意:**复合索引中各列的顺序需要与 where 子句中的顺序保持一致。

[![](img/6a0a38b57321b0fec33d344701a4270e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---UVj3GId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2A9GzLaUAPbJKNGJ_2cBwMUg.png)

#### 总结:

指数良好；未使用的索引是坏的；

索引太多没关系，只要它们在某个时刻被使用。

更多的内存用于数据库是好的。真空&分析表太好了！！！

旧数据存档→做个好公民你就牛逼了！！

为了获得最佳性能，需要对位于以下位置的 postgresql conf 文件进行以下设置(需要重启服务器):`**_/etc/postgresql/10/main/postgresl.conf_**`

| 参数 | 允许值 |
| --- | --- |
| 共享缓冲区 | 75%的内存 |
| 工作记忆 | 内存的 25% |
| 维护 _ 记忆 | 最小:256MB 最大值:512MB |

考虑这样一个场景，Postgres 服务器有 160g RAM:

| 参数 | 允许值 |
| --- | --- |
| 共享缓冲区 | 120GB |
| 工作记忆 | 40GB |
| 维护 _ 记忆 | 256MB |

#### 优化查询的步骤:

1)对您的查询运行解释分析，如果花费的时间太长；对您的查询运行 Explain。

2)复制输出并粘贴到对话框@[https://explain.depesz.com/](https://explain.depesz.com/)上

3)检查您的查询的统计数据:

**索引扫描/仅索引扫描**是最好的，并且**不需要进行任何更改**。

**顺序扫描，**可以通过**为 where 子句中的特定列**创建索引而被**转换为** **索引扫描**。

**位图堆扫描，**可以通过**创建复合索引**转换为索引扫描，也称为多列索引，其顺序**与 where 子句的顺序**相同，如下:

```
CREATE INDEX $indexName ON $tableName ($Field1, $Field2); 
```

> 自我提醒:索引和优化。！！

[![](img/e3d0641355bf28d0be034ae23c5072eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qgcrDAtF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/448/1%2AXs4J9Eyfxo_fvZSRp5HHFg.gif)

*原载于 2019 年 8 月 10 日*[*https://www . data wrangler . in*](https://www.datawrangler.in/2019/08/postgres-optimization-beyond-level-up.html)*。*

* * *