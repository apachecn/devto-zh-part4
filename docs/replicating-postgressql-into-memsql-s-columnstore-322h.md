# 将 PostgresSQL 复制到 MemSQL 的 columnstore

> 原文：<https://dev.to/oryanmoshe/replicating-postgressql-into-memsql-s-columnstore-322h>

## 化不可能为困难

[![](img/84577e0ca3336100f050e16beac9e25c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YsldeFUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6d6niyj52r6x5mijfezt.png)

##### 

<center>*我……觉得这不应该是*</center>

的样子

现在是*一年中的*时间**又到了**，我们需要升级我们的 MemSQL 集群并扩展我们的合同以适应新的集群拓扑。

这次我们真的超越了自己，扩展到 1TB 集群令人印象深刻，尤其是在完全不合理的情况下。

* * *

### 背景

#### 等待。1TB 集群？

是啊是啊，可以说我们被宠坏了，但是在 PostgresSQL(从现在开始 PG)上查询就不一样了。

当然，如果您使用正确的索引并优化您的查询，您可以获得 *ok* 的速度，但它甚至无法与您从基于内存的行存储或列存储的疯狂快速聚合中获得的性能相比。

#### Mem 不同存储类型的简短(简短)总结

所以在 Mem 中我们基本上有两种类型的存储，行存储和列存储。

行存储的存储方式与其他数据库非常相似，但是存储在内存中而不是磁盘中(非常快)。这意味着每行与其所有列存储在一起

columnstore 是一种转置的 rowstore，我们不是存储行，而是存储列(*谢谢船长显见的*)，这允许我们快速地进行聚合(想想看，不是转到每一行并对“成本”列求和，我们可以只转到“成本”列并求和)。列存储存储在磁盘上。

问题是 MemSQL 的许可成本更高，因为我们的集群中有更多的内存，更不用说机器本身的成本了(1TB 的内存并不便宜)

那么为什么不把所有东西都存储在 columnstore 中呢？许可证和基础设施都更便宜，而且速度很快！”你可能会问(如果你在阅读科技文章时自言自语)

这就是问题所在——数据存储在 columnstore 中的方式使得它在聚合查询中非常快，并且允许惊人的压缩，但是更新一行很慢。

有多慢？如果我们需要在某一天为行更新一些列，删除这一天的数据并重新插入更新的数据比更新现有的行更快。

## T2】

#### 那么我们如何存储我们的数据呢？

嗯，在我的团队中，我们使用 7 种类型的数据库(可能更多，这些天真的无法跟踪)*但是*主要的是 PostgresSQL，由 RDS 托管和管理(用于事务处理)和 MemSQL，由 EC2 托管和管理(用于分析处理) [*](#discl)

本能地，我们的大部分数据存储在 PG 中(不包括一些包含北 8B 记录的大型 columnstore 表)

问题是，一旦进入 Mem，就再也回不去了，所以我们创建了一个复制服务，可以实时地将一行从 PG 复制到 Mem 的 rowstore。这允许我们丰富我们的 columnstore only 表，创建 ETL，最重要的是，加速查询。

如果你在这里，你要么使用 Mem，因此知道它的性能，要么只是喜欢去 dev.to，阅读关于利基数据库的随机文章。如果你是后者，让我用一些数字来告诉你。

一个完全合理的查询，由 6 个连接组成，在 PG 上运行需要 30 分钟。在优化了 2-3 个小时，添加了索引，用头撞墙，祈祷快点结束之后，我把它缩短到了 3 分钟。

精确地获取原始查询(30 分钟的那个)并在 Mem 上运行它，花费了 *1.87 秒*。

## T2】

### 真正的交易

#### 问题定义，又名什么让我失眠

[![](img/0fa7225cf0036821272455223b3fdf4d.png)](https://i.giphy.com/media/D12CsrRNv7gL6/giphy.gif) 
所以 Mem 很贵，我们几乎达到了我们的**新**许可限制(在增加了一倍多之后)，我们不可能回到 PG 上专门查询。

解决方案似乎很简单，将大表移到 columnstore，释放一些内存，这样就不必增加许可和升级机器。

对于本文，我将使用我们的表`touch_points`作为例子，它是存储在 rowstore 中的最大的(在内存和行数方面)表——它有 180 多万行，重量超过 190GB。

为什么它在我们的行存储中？首先，因为我们从 PG 复制它，到目前为止，我们的服务只支持复制到行存储表，但是，更重要的是，它需要更新。在 30 列中，有 2 列可能会被更新- `visitor_id`和`cost`。

* * *

### 方案

#### 第一个方案

所以这是设计上“正确”的解决方案。

简而言之，使用 ActiveRecord 回调，我保持两个表是最新的，一个是 columnstore 中的`touch_points`表，包含除了之外的所有当前存在于`touch_points` **上的列，这两个列被更新。除了`touch_points`之外，我还在行存储中创建了一个名为`touch_points_extra_data`的表，包含 2 个缺失的列和 1 个 ID 列，它允许我连接这 2 个表。**

正如我所说，这是设计上的正确解决方案，问题是可能会出很多问题。有这么多移动部件，都依赖于轨道挂钩，我们肯定会在某个时候**失去同步**。更不用说我们必须编辑来自`touch_points`的所有查询来添加额外的`JOIN`了。

[![](img/4e9771f25d06bc9e44f7431e60cc95a7.png)](https://i.giphy.com/media/3otPoD3m0h16iRbgNG/giphy.gif)

#### 第二种解法，又名“暴力破解”

所以我们意识到我们的首要任务是保持数据的正确性，我们愿意做出一些妥协*(预示)*

我决定偶尔从 PG 复制整个表。这样，我们可以确保(直到复制时)我们的数据在两个数据库中是相同的。

折衷的办法是，我们习惯于实时更新这些数据，现在这些数据在下一次复制之前都会过时。这是我愿意接受的妥协。

* * *

### 技术部分

#### 说起来容易做起来难

显然，将整个表从一个数据库复制到另一个数据库并不像您想象的那样简单。尤其是当两个数据库在完全不同的引擎上运行时。

我尝试的第一件事是使用`pg_dump`，使用`plain`文件格式(本质上是创建一个加载了`INSERT`语句的文件)，然后将其转换为 mysql 语法并加载到 Mem。

听起来很棒，对吧？我启动了`pg_dump`，5 个小时后它还没有完成，而转储文件已经有 60GB 了。`pg_dump`与`plain`选项是最低效的数据存储方式。复制延迟 5 小时是不可接受的。

#### 如果一开始你没有成功..再次失败

我尝试的下一件事是使用 PG 的`COPY`命令，这个命令可以将一个表或一个查询复制到`FILE`、`PROGRAM`或`STDOUT`。

首先，我尝试使用 STDOUT 选项(最简单的选项，它不会创建一个巨大的转储文件)

```
psql -U read_user -h very-cool-hostname.rds.amazonaws.com -p 5432 -d very_cool_db -c\
"\COPY (SELECT * FROM touch_points) TO STDOUT\ WITH(DELIMITER ',', FORMAT CSV, NULL 'NULL', QUOTE '\"');" > touch_points.csv 
```

而且成功了！在不到 20 分钟的时间里，我从 PG 那里得到了一个包含我们整个`touch_points`表的“转储”文件。

现在我们只需要将它导入 Mem，但是为什么**我需要这个文件？我可以直接把结果从 PG 传到 Mem！**

所以我需要创建 Mem 接收这个类似 csv 的表并将其加载到 db 中的部分。幸运的是 Mem 与 MySQL 兼容，并为我们提供了`LOAD DATA`子句！

```
LOAD DATA LOCAL INFILE '/dev/stdin'
  SKIP DUPLICATE KEY ERRORS
  INTO TABLE touch_points_columnstore
  FIELDS
    TERMINATED BY ','
    ENCLOSED BY '"'
    ESCAPED BY ''
  LINES
    TERMINATED BY '\n'
  MAX_ERRORS 1000000; 
```

现在，正如我所说的，我们希望将数据直接传输到 Mem，所以我们需要创建一个到 DB 的连接:

```
mysql -h memsql.very-cool-hostname.com -u write_user -P 3306 -D very_cool_db\
-p'4m4z1nglyS3cur3P455w0rd' -A --local-infile --default-auth=mysql_native_password -e\
"LOAD DATA LOCAL INFILE '/dev/stdin' SKIP DUPLICATE KEY ERRORS\ INTO TABLE touch_points_columnstore FIELDS TERMINATED BY ','\ ENCLOSED BY '\\\"' ESCAPED BY '' LINES TERMINATED BY '\\n' MAX_ERRORS 1000000;" 
```

然后将数据从 PG 传输到那个连接！

```
psql -U read_user -h very-cool-hostname.rds.amazonaws.com -p 5432 -d very_cool_db -c\
"\COPY (SELECT * FROM touch_points) TO STDOUT\ WITH(DELIMITER ',', FORMAT CSV, NULL 'NULL', QUOTE '\"');" |\
mysql -h memsql.very-cool-hostname.com -u write_user -P 3306 -D very_cool_db\
-p'4m4z1nglyS3cur3P455w0rd' -A --local-infile --default-auth=mysql_native_password -e\
"LOAD DATA LOCAL INFILE '/dev/stdin' SKIP DUPLICATE KEY ERRORS\ INTO TABLE touch_points_columnstore FIELDS TERMINATED BY ','\ ENCLOSED BY '\\\"' ESCAPED BY '' LINES TERMINATED BY '\\n' MAX_ERRORS 1000000;" 
```

和...成功了！但是花了 2 个小时才完成。我确信我们可以做得更好。

#### 压缩是你的朋友

因此，关于将数据加载到 Mem 中，需要理解的两件很重要的事情是:

1.  当将数据文件插入 Mem 时，它会将文件复制到本地聚合器，并在集群的节点之间拆分文件，从而显著加快数据加载。
2.  Mem 支持接收 gzipped 压缩的数据文件。

结合这两条信息让我明白，在中间创建文件可能没有我想象的那么糟糕。

我可以压缩该文件，使存储不成问题，它还可以通过减少大部分网络相关延迟来加快文件向聚合器的传输(在拆分之前)，并允许 Mem 在节点之间拆分数据。

我们开始吧！

首先，我需要修改 PG 部分，因此它不是将内容传送到`STDIN`，而是将其传送到`PROGRAM`，在我们的例子中，是`gzip`T3】

```
psql -U read_user -h very-cool-hostname.rds.amazonaws.com -p 5432 -d very_cool_db -c\
"\COPY (SELECT * FROM touch_points) TO PROGRAM 'gzip > /data/tmp/replication/touch_points_columnstore.gz'\ WITH(DELIMITER ',', FORMAT CSV, NULL 'NULL', QUOTE '\"');" 
```

创建这个 tmp 文件后，我们需要加载它。幸运的是，我们唯一要做的就是改变输入文件的来源！
我们完成的脚本看起来像这样:

```
psql -U read_user -h very-cool-hostname.rds.amazonaws.com -p 5432 -d very_cool_db -c\
"\COPY (SELECT * FROM touch_points) TO PROGRAM 'gzip > /data/tmp/replication/touch_points_columnstore.gz'\ WITH(DELIMITER ',', FORMAT CSV, NULL 'NULL', QUOTE '\"');" &&\
mysql -h memsql.very-cool-hostname.com -u write_user -P 3306 -D very_cool_db\
-p'4m4z1nglyS3cur3P455w0rd' -A --local-infile --default-auth=mysql_native_password -e\
"LOAD DATA LOCAL INFILE '/data/tmp/replication/touch_points_columnstore.gz' SKIP DUPLICATE KEY ERRORS\ INTO TABLE touch_points_columnstore FIELDS TERMINATED BY ','\ ENCLOSED BY '\\\"' ESCAPED BY '' LINES TERMINATED BY '\\n' MAX_ERRORS 1000000;" 
```

就是这样！

创建的文件重 7GB，整个过程不到 20 分钟，所以我们可以每小时运行一次，并且有半实时数据！

[![](img/fe75346cc249cabd309000f2284bda52.png)](https://i.giphy.com/media/a0h7sAqON67nO/giphy.gif)

显然，这还不是结束，我将它包装在一个漂亮的 rails 模块中，该模块允许我轻松地将任何查询从 PG 复制到 Mem，包括截断旧数据和使用 2 个表来最小化复制期间的停机时间。

如有任何问题，请随时联系我！

**包括但不限于分析和交易。*