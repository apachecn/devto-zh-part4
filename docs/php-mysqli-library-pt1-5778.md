# PHP MySQLi 库 Pt1

> 原文：<https://dev.to/godsgood33/php-mysqli-library-pt1-5778>

我真的很喜欢 WordPress WPDB 类的工作方式。

```
$wpdb->insert('table', ['field' => 'value']); 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

它创建语句并自动转义“有问题的”字符。但是没有简单的方法将它添加到您的项目中。还有其他变量，比如“posts”，它包含了 posts 表的表名，对于大多数情况来说，这些变量是不必要的。所以我决定创建 [Php_Db](https://github.com/godsgood33/php-db) 来做几乎所有相同的事情。我考虑过使用 WPDB 所基于的 ezSQL 库，但是它没有包含一些非常基础的东西，我想要一个扩展的插入、替换和更新功能，以及一些 DDL 功能。还有其他东西，但我看到了对这些东西的巨大需求。
我要花几天时间来谈谈 Php_Db 是如何工作的，因为我在里面放了很多功能。完整的文档你可以看看我的[文档](https://php-db.readthedocs.io/en/stable/)或[来源](https://github.com/godsgood33/php-db)

所以安装...
`composer require godsgood33/php_db`

现在您需要运行设置来生成一些定义的变量。导航到{ project _ dir }/vendor/gods good 33/PHP _ db。现在，如果您想加密 Php_Db 使用的 DB 密码，运行`php bin/setup.php --algorithms`来显示您的系统支持的所有 OpenSSL 算法的列表。一旦你决定了一个，重新运行`php bin/setup.php -a={algorithm}`如果你不想加密密码，你可以运行`php bin/setup.php`同样的问题。

然后它会问你几个问题，MySQL 服务器名称/IP、用户名、密码和模式。然后它打印出一个需要添加到项目中的常量列表。一旦你包含了这些，你就可以开始使用 Php_Db 了。把它们放在你的引导或索引文件中是最好的。只要它是在实例化 db 类之前完成的。

如果你曾经改变你的用户名或密码，或者你想改变你的加密算法，你可以重新运行 setup.php 获得新的值，更新你的常数，你很好。**警告**如果你使用 Php_Db 加密你插入数据库的任何数据，你必须首先解密，然后改变你的算法，然后你可以重新加密它。你所要做的就是在适当的时候调用`encrypt` / `decrypt`方法。

我推荐使用 Php_Db 来扩展你的数据库类
`class db extends \Godsgood33\Php_Db\Database {}`

现在，一些基本的功能。

字段列表、WHERE 子句和“标志”的操作都是相同的，与您正在运行的查询(select、insert、delete...)

就像 WPDB 一样，查询中的第一个参数是表名。这只是一个字符串，如果您愿意，您可以放入单个或多个表和/或添加别名。它只需要看起来像是您在编写 SQL 查询本身。
`"table"`或`"table1 t1, table2 t2"`

中的下一个参数是字段列表。这只是您希望在结果中获取的一组字段。同样，就像 SQL 查询一样。
`['id', 'name']`或`['t1.id', 't2.name']` **注意**:`selectCount`方法中没有字段列表参数。

第三个参数是 WHERE 子句列表。这是一个\Godsgood33\Php_Db\DBWhere 对象的数组。

定义:

`$where = new DBWhere($field = null, $value = null, $operator = '=')`

假设您想要查找一个具有特定 ID 的用户

```
$where = new DBWhere('id', 1234);
$this->select('users', null, $where); 
```

Enter fullscreen mode Exit fullscreen mode

这个查询类似于`SELECT * FROM users WHERE id = '1234'`。那么，当您想要提供多个 where 子句时，该怎么做呢？

```
$where1 = new DBWhere('fname', 'Fred');
$where2 = new DBWhere('lname', 'Flintstone');
$this->select('users', null, [$where1, $where2]); 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT * FROM users WHERE fname = 'Fred' AND lname = 'Flintstone'`

默认情况下，DBWhere 将在子句之间使用“AND”运算符。通过更新`DBWhere::$sqlOperator`属性，您可以使用任何其他 SQL 支持的操作符。所以这可能会变成...

```
$where1 = new DBWhere('lname', 'Flintstone');
$where2 = new DBWhere('lname', 'Jetson');
$where2->sqlOperator = 'OR';
$this->select('users', null, [$where1, $where2]); 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT * FROM users WHERE lname = 'Flintstone' OR lname = 'Jetson'`

如果您想寻找多个值并使用 IN 运算符，您可以调用

```
$where = new DBWhere('id', [1,2,3], DBWhere::IN)
$this->select('users', null, $where); 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT * FROM users WHERE id IN ('1','2','3')`

也可以用`NOT_IN`、`BETWEEN`、`LIKE`、`NOT_LIKE`、`IS`或`IS_NOT`。
也可以使用普通运算符`=`、`!=`、`>`、`<`、`<=`、`>=`。如果未指定“operator”参数，它将使用“=”作为默认运算符。

DBWhere 还对以下内容进行自动转换和转义:

*   `null` = NULL
*   DateTime = 'Y-m-d H:i:s '
*   布尔= '0 '或' 1 '
*   array =每个元素都被转义
*   objects =如果对象有一个可调用的' _escape '方法，它将调用该方法并返回结果

最后，还有旗帜、`join`、`having`、`order`、`group`和`limit`。

`join` =连接键是连接字符串的数组
`having` =连接键是 DBWhere 对象的数组，就像 Where 子句
`order` =可以是字符串，也可以是排序子句的数组列表
`group` =可以是字符串，也可以是数组，就像`order`
`limit` =包含要限制的数字的字符串(可以包含起始行)

因此，潜在的标志数组可能如下所示:

```
[
    'joins' => [
        'JOIN table2 t2 ON t2.id = t1.id',
        'JOIN table3 t3 ON t3.id = t2.id'
    ],
    'having' => [
        new DBWhere('age', 18, '>=')
    ],
    'order' => 't1.name DESC',
    'group' => 't1.id',
    'limit' => '10'
] 
```

Enter fullscreen mode Exit fullscreen mode

同样，这些是大多数查询类型允许的主要参数。我将在下一篇文章中更详细地介绍不同的查询。一如既往，在[完整文档](https://php-db.readthedocs.io/en/stable/)中有更多详细信息