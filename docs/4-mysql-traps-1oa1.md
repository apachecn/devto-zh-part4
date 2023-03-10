# 4 个 MySQL 陷阱

> 原文：<https://dev.to/michi/4-mysql-traps-1oa1>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/common-mysql-traps)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

注意:其中一些也可能存在于其他数据库系统中。

## utf8 其实不是 utf8

遇到过这个 SQL 异常吗？`Incorrect string value: ‘\xF0\x9F\x98\x83 <…’ for column...`

你可能和许多人一样陷入了同样的陷阱。原来 mySQL 的 utf8 字符集只能存储所有可能的 unicode 码位的 6%左右。这也排除了例如表情符号。

```
insert into emails(body) values ('🦄🦄🦄'); 
```

Enter fullscreen mode Exit fullscreen mode

这就是为什么你应该用`utf8mb4`来代替。

阅读更多信息以及如何迁移:[https://mathiasbynens.be/notes/mysql-utf8mb4](https://mathiasbynens.be/notes/mysql-utf8mb4)

## 比较`varchar_field`与`false`或 0

想象下面这个人为的例子

```
function normalizeEmail(email) {
    if (!validate(email)) {
        return false
    }

    return normalize(email)
}

// somewhere in the code
await User.where('email', normalizeEmail(email)).first() 
```

Enter fullscreen mode Exit fullscreen mode

假想的 ORM 将执行查询`select * from users where email = <prepared value> LIMIT 1`

如果`normalizeEmail`中的验证成功，查询将是`select * from users where email = 'normalized.email@test.com' LIMIT 1`。

如果验证不成功，查询将是`select * from users where email = false LIMIT 1`。

现在继续在你的数据库系统中运行类似`select * from users where <varchar field> = false`的东西。

由于字段不具有可比性，MySQL 会将一个字段转换为另一个字段，使它们匹配并返回所有用户。我们的 ORM 将简单地选择第一个用户，然后继续这个逻辑。😬相当危险。

> 同样的情况也发生在`field = 0`

## `insert on duplicate key update`创建主键孔

假设我们有一个表`statistics`，其中包含列`id`(AI)`fkid`、`title`。

```
INSERT INTO statistics (fkid, title) VALUES (1, 'first'); 
```

Enter fullscreen mode Exit fullscreen mode

这将插入一个带有`id` 1 的新记录。让我们想象有一批总是插入或更新标题。它可能会在一段时间内执行以下查询:

```
INSERT INTO statistics (fkid, title) VALUES (1, 'second') ON DUPLICATE KEY UPDATE title = 'second';
INSERT INTO statistics (fkid, title) VALUES (1, 'third') ON DUPLICATE KEY UPDATE title = 'third';
INSERT INTO statistics (fkid, title) VALUES (1, 'fourth') ON DUPLICATE KEY UPDATE title = 'fourth'; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们想插入一个带有新 fkid 的记录。

```
INSERT INTO statistics (fkid, title) VALUES (100, 'first') ON DUPLICATE KEY UPDATE title = 'first'; 
```

Enter fullscreen mode Exit fullscreen mode

这会在表中插入一条新记录，但是猜猜`id`是什么？您可能希望它是 2，但实际上，每次`insert on duplicate key update`插入和处理`update`失败时，它都会在内部增加自动增量。这意味着该记录的`id`将是 5。

id 是否真的是连续的并不太重要，但是您可能想知道它的原因是什么。

了解更多:[https://stack overflow . com/questions/38347110/prevent-innodb-auto-increment-on-duplicate-key](https://stackoverflow.com/questions/38347110/prevent-innodb-auto-increment-on-duplicate-key)

## int(2)不是你想的那个意思

整数的长度在 MySQL 中没有实际意义，您仍然可以插入像 9999999 这样的值。它们只限制命令行客户端中应该显示多少个字符。🤨

了解更多信息:[https://stack overflow . com/questions/5634104/what-the-size-of-column-of-int 11-in-MySQL-in-bytes](https://stackoverflow.com/questions/5634104/what-is-the-size-of-column-of-int11-in-mysql-in-bytes)

你还知道其他什么陷阱吗？