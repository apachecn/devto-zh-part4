# 如何给 Rails 上的大型工作 app 添加索引？

> 原文：<https://dev.to/jetrockets/how-to-add-index-to-a-large-working-app-on-rails-5apl>

众所周知的事实是，PostgreSQL 和许多其他 RDBMS 在创建索引时锁定对表的写访问。当你的项目大到允许停工来进行像新指数这样的小调整时，这是不可接受的。有一种方法可以避免写锁。您可以同时创建索引。但是如何使用 Rails 迁移呢？
首先你可以通过`:algorithm`选项:

```
class AddIndexOnBatchIdToFundTrades < ActiveRecord::Migration[5.0]
  def change
    add_index :fund_trades, :batch_id, algorithm: :concurrently
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

但是当您尝试运行这样的迁移时，您会得到以下错误:

```
PG::ActiveSqlTransaction: ERROR:  CREATE INDEX CONCURRENTLY cannot run inside a transaction block
: CREATE  INDEX CONCURRENTLY "index_fund_trades_on_batch_id" ON "fund_trades"  ("batch_id") 
```

Enter fullscreen mode Exit fullscreen mode

这是因为默认情况下，任何迁移都是在事务内部执行的。谢天谢地，有一种方法可以通过它——使用`disable_ddl_transaction!`来运行您的迁移，而不需要事务:

```
class AddIndexOnBatchIdToFundTrades < ActiveRecord::Migration[5.0]
  disable_ddl_transaction!

  def change
    add_index :fund_trades, :batch_id, algorithm: :concurrently
  end
end 
```

Enter fullscreen mode Exit fullscreen mode