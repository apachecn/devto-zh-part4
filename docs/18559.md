# 从 Rails 控制台删除数据库索引

> 原文：<https://dev.to/devteam/removing-a-database-index-from-rails-console-5ffi>

如果您熟悉 Rails，您可能会在阅读本文时感到疑惑，“为什么我需要从控制台中删除数据库列呢？难道我不应该通过适当的 Rails 迁移来实现吗？”嗯，你说得对！这应该可以通过 Rails 迁移安全地完成；然而，我们在 DEV 发现自己处于一个有趣的境地。

我们刚刚合并了一个 pull 请求，该请求会向我们的通知表添加一些索引。由于这个表非常大，我们通常必须增加数据库语句超时来处理这个迁移。有时迁移需要一段时间才能完成，但当事情进展顺利时，这通常是唯一的问题。这一次，我们决定使用 PostgreSQL 的[并发索引创建](https://www.postgresql.org/docs/9.6/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY)。

通常在构建索引时，Postgres 必须锁定正在被索引的表，防止新的写入。这可能意味着几分钟内没有人会收到任何新的通知。通过并发索引构建，Postgres 允许在构建索引时写入数据库。然而，使用并发性的一个后果是构建索引的时间要长得多。

嗯，花了点时间，这是肯定的。事实上，即使我们先发制人地增加了数据库的语句超时，它也花费了太长时间以至于超时。我们增加了语句超时，再次运行迁移，但是这一次，我们得到了一个意外的错误消息:

```
ArgumentError: Index name 'index_notifications_on_user_notifiable_and_action_not_null' on table 'notifications' already exists 
```

Enter fullscreen mode Exit fullscreen mode

嗯等等什么？这很奇怪；该索引不应该已经存在，因为迁移失败了。现在，一般来说，这不是一个大问题，但是迁移仍然需要完成。我提出了一个可能解决这个问题的方案:

```
heroku run rails db:migrate VERSION=20190531094609
# and then
heroku run rails db:migrate VERSION=20190531094926 
```

Enter fullscreen mode Exit fullscreen mode

然而，这完全是错误的，并回滚到该点之前的所有迁移。呀。这里缺少一个关键字:`up`。应该是这样的:

```
heroku run rails db:migrate:up VERSION=20190531094609
# and then
heroku run rails db:migrate:up VERSION=20190531094926 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，在最老的失败版本(20190531094609)之前的迁移几乎都是索引和一个列的更改。不过在其他任何时候，情况都可能会非常糟糕。因此，我们争先恐后地正确迁移那些首先启动的迁移，然后着手解决两个失败的迁移。

在想出如何删除索引的几分钟后， [@ben](https://dev.to/ben) 想出了如何通过 Rails 控制台来删除索引。在开发测试之后，需要的命令是:

```
ActiveRecord::Migration.remove_index :notifications, %i[user_id notifiable_id notifiable_type action]
ActiveRecord::Migration.remove_index :notifications, %i[user_id notifiable_id notifiable_type] 
```

Enter fullscreen mode Exit fullscreen mode

两个命令都完成了，并得到了一个应该是成功的响应，所以我们再次运行了迁移。这一次，我们有一个更大的语句超时。和...成功了！除了一些性能问题和几百个错误之外，一切都很正常。虽然这对我们来说有点紧急，但可能不需要太夸张。

关键是弄清楚如何正确地删除索引(并且不运行不正确的命令😅).这个问题很具体，但它是 Rails-Postgres 特有的问题，与我们如何处理业务逻辑没有直接关系。希望这篇文章对其他有这种特殊问题的人有所帮助。