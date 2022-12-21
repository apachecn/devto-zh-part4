# 从所有人中选择初学者；

> 原文：<https://dev.to/mindyzwan/select-beginner-from-all-3okn>

#### 导航 SQL 的初级命令集合& PostgreSQL

对于刚开始使用 SQL 的人来说，这可以作为你练习时的参考。

<figure>[![](img/6a80bd248f06f210c1544b7ac1ddb909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--36JEmADX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap5j6BcCiUO3-efdUu1HFBQ.jpeg) 

<figcaption>索引卡片。图片来源:Pixabay</figcaption>

</figure>

用实际的表名/列名/约束/数据等替换< >中的任何内容…

*注:从未在 Google Sheets 或 Excel 中花费太多时间？从那里开始！我建议花 1-3 个小时在 Google Sheets 中使用查询功能——a)它非常有趣，b)它可能会帮助你形象化地理解这些概念。在查询功能* *上查看大卫·克雷维特的* [*课程(或者如果你宁愿不花 49 美元，就去他的*](https://learn.codingisforlosers.com/the-google-sheets-query-function) [*博文*](https://codingisforlosers.com/google-sheets-query-function/) *了解一下)。*

### 终端命令

在打开 psql 控制台之前，当您在终端中时会用到这些。

`createdb <table name>`:创建表格

`dropdb <table name>`:丢弃(删除)一个表格

`psql <table name>`:打开一个基于终端的控制台，与 PostgreSQL 和其中的数据库进行交互——类似于 irb for Ruby。

`postgres`是安装 PostgreSQL 时创建的默认数据库。您可以随时使用`psql postgres`打开控制台。然后，您可以从 psql 控制台中移动到其他数据库。

`psql <database name> < <file name>.sql`:将文件导入数据库。

### 元命令

始终以反斜杠()开头。这些是在 psql 控制台中使用的。

`\q`:退出

`\c <database name>`:连接到不同的数据库

`\l`:列出所有的数据库

`\dt`:描述当前数据库中的表格

`\d <table name>`:描述表格

### SQL 命令— DDL

总是以分号(；).这些是在 psql 控制台中使用的。

#### 创建/删除

`CREATE DATABASE <database name>;`

创建新的数据库。

`DROP DATABASE <database name>;`

删除数据库。小心这个——不可逆的！

`CREATE TABLE <table name>(<column setup>);`

在当前数据库内创建一个新表。括号中是列设置信息。

列设置包括三项:列名、数据类型和约束。下面的示例创建了一个包含三个新列的表，每个列都有一个数据类型和一个约束。

```
CREATE TABLE a_new_table(
 name data_type constraint,
 name2 data_type2 constraint,
 name3 data_type3 constraint
 ); 
```

`DROP TABLE <table name>;`

删除表格。小心这个——不可逆的！

#### **涂改:表格**

`ALTER TABLE <table name> RENAME TO <new name>;`

重命名一张表。

#### 改变:列

`ALTER TABLE <table name> RENAME COLUMN <column name> TO <new name>;`

重命名一列。

`ALTER TABLE <table name> ALTER COLUMN <column name> TYPE <new type>;`

改变一列的数据类型。

`ALTER TABLE <table name> ADD COLUMN <column setup>;`

创建新列，`<column setup>`与创建新表一样:`<column name> <data type> <constraints>`。

`ALTER TABLE <table name> DROP COLUMN <column name>;`
删除一列。小心这个——不可逆的！

#### 改变:约束

`ALTER TABLE <table name> ADD CONSTRAINT <constraint name> <constraint>;`

添加一个表约束，你编约束名。

`ALTER TABLE <table name> DROP CONSTRAINT <constraint name>;`

删除一个表格约束。

`ALTER TABLE <table name> ADD UNIQUE (<column name>);`

给给定列添加唯一约束。

`ALTER TABLE <table name> ADD CHECK (<expression>);`

给表格添加一个检查约束。可以在表达式中使用多列。SQL 函数(下面解释)是有用的工具。

`ALTER TABLE <table name> ALTER COLUMN <column name> SET <constraint>;`

添加列约束。

`ALTER TABLE <table name> ALTER COLUMN <column name> DROP CONSTRAINT <constraint>;`

删除一列约束。

`ALTER TABLE <table name> ALTER COLUMN <column name> DROP DEFAULT;`

删除一个默认条款。

### SQL 命令— DML

总是以分号(；).这些是在 psql 控制台中使用的。

#### 插入

`INSERT INTO <table name> (<column name>) VALUES (<data>);`

将数据插入表格。可以使用逗号添加更多列，并且可以通过在第一组数据后的括号中添加更多组值来添加多行数据。

```
INSERT INTO <table name> (<col 1>, <col 2>)
 VALUES (<data-1a>, <data-1b>),
 (<data-2a>, <data-2b>),
 (<data-3a>, <data-3b>); 
```

#### 更新

`UPDATE <table name> SET <column name> = <new value> WHERE <expression>;`

将数据更新为给定列中的<新值>，其中表达式为真。WHERE 子句是可选的——如果您不使用它，该命令将用`<new value>`更新表中每行的**。**

#### 删除

`DELETE FROM <table name> WHERE <expression>;`

删除表达式为真的数据。WHERE 子句是可选的——如果您不使用它，该命令将删除表中的每一个行**。**

#### 选择

`SELECT * FROM <table name>;`

显示列表中的所有数据。*注意:* **是一个通配符——获取给定表中的所有列。*

`SELECT <column name>, <column name> FROM <table name>;`

选择后显示具体的列。可以通过用逗号分隔名称来显示一个或多个。

**选择条款**

*   通过添加一个表达式来指定你想要的数据行。请参见下面的 SQL 运算符。
*   `... ORDER BY <column name>;`根据列对数据进行排序。可以添加`ASC`或`DESC`来指定是升序还是降序。也可以使用函数进行排序。
*   `... LIMIT <number>;`将结果限制为给定的数量。
*   根据给定的数字，删除一些最初的结果。
*   `... GROUP BY <column name>;`根据给定列中的公共值组合数据。在`SELECT`命令中经常需要这个来使用聚合函数(参见下面的 SQL 函数)。

### SQL 运算符/比较

`=`:等于

`<>`或`!=`:不等于

`>`、`<`:大于，小于

`>=`、`<=`:大于等于、小于等于

`BETWEEN` / `NOT BETWEEN`:介于或不介于两个值之间

`IS NULL` / `IS NOT NULL`:为或不为空

`AND`、`OR`:添加多个子句的逻辑运算符

`LIKE` / `NOT LIKE`:与`%`(多字符通配符)或`_`(单字符通配符)一起使用来匹配子字符串(例如，`...LIKE %Smith;`将匹配“哈里·史密斯”和“莱恩·史密斯”)

### SQL 函数

#### 字符串

`length()`:返回给定字符串参数的长度。

#### 日期/时间

`now()`:返回当前日期和时间。

`date_part()`:接受两个参数，日期部分(例如“年份”)和数据来源(例如列名)。

`age()`:采用时间戳参数，计算时间戳和当前时间之间经过的时间。

#### 聚合

`count()`:返回指定数据中值的个数。

`sum()`:返回指定数据中值的总和。

`min()`:返回指定数据中的最小值。

`max()`:返回指定数据中的最大值。

`avg()`:返回指定数据的平均值。

除了上面所描述的，还有很多其他的命令/功能/用途，所以当你需要的时候，大胆的在网上搜索更多的信息吧。第一个求助的好地方？PostgreSQL 文档:[postgresql.org/docs](https://www.postgresql.org/docs/)。

现在就这些——好好编码吧，我的朋友们！

`\q`