# 使用 PostgreSQL 顾问锁控制并发性

> 原文：<https://dev.to/jkostolansky/using-postgresql-advisory-locks-to-control-concurrency-3j0d>

在 Ruby on Rails 应用程序中，我们经常需要控制对数据库的并发访问。我们需要确保一段代码同步运行，以保证多个非原子操作的原子性。

PostgreSQL 提供了各种技术来控制对数据的并发访问。例如，我们可以使用可序列化事务隔离级别。在这种情况下，一组可序列化事务的任何并发执行肯定会产生与按某种顺序一次运行一个事务相同的效果。

还有显式的行级锁，可以防止两个事务以冲突的方式修改资源。如果很可能发生数据库事务冲突，我们可以选择悲观锁定。它锁定记录，直到事务完成，任何其他试图获取相同锁的事务都必须等待，直到锁被释放。

否则，我们可以使用乐观锁定。它使用记录的版本号来检查自记录打开以来是否有其他进程对其进行了更改，如果发生了更改并且忽略了更新，则引发异常。

控制并发的另一种方法是顾问锁定。

## 咨询锁定

PostgreSQL 的咨询锁不依赖于数据库表或行。例如，我们可以使用它们来限制应用程序中处理特定表中记录的特定代码的并发性，而应用程序中访问该表的其他部分不会注意到这一点。

建议锁是应用程序强制的锁。这意味着 PostgreSQL 没有隐式地使用它们，而是由应用程序调用提供的语句来获取和释放这些锁，并赋予这些锁一个意义。

我们甚至可以使用顾问锁定来控制不管理数据库数据的代码的同步——例如，进行 API 调用、访问磁盘上的文件或其他任何事情。

咨询锁可以在会话级或事务级获得。PostgreSQL [文档](https://www.postgresql.org/docs/11/explicit-locking.html#ADVISORY-LOCKS)解释了不同之处:

> 一旦在会话级别获得了咨询锁，它将一直保持到显式释放或会话结束。与标准锁请求不同，会话级咨询锁请求不支持事务语义:在稍后回滚的事务期间获得的锁在回滚后仍将被持有，同样，即使调用事务稍后失败，解锁也是有效的。一个锁可以被它的拥有进程多次获取；对于每个完成的锁请求，在锁被实际释放之前，必须有一个相应的解锁请求。
> 
> 另一方面，事务级锁请求的行为更像常规锁请求:它们在事务结束时自动释放，并且没有显式的解锁操作。对于短期使用咨询锁，这种行为通常比会话级行为更方便。

PostgreSQL 提供了多种函数来获取和释放独占或共享、会话或事务级咨询锁。同样，查看官方文档了解更多信息。

以下是为会话级锁提供的一些函数:

*   `pg_advisory_lock(key bigint)`获得独占会话级顾问锁。如果另一个会话已经锁定了同一个资源标识符，那么这个函数将一直等到资源可用。多个锁请求堆叠在一起，因此如果资源被锁定三次，则必须解锁三次。
*   `pg_try_advisory_lock(key bigint)`获取独占会话级顾问锁(如果可用)。它类似于`pg_advisory_lock`，除了它不会等待锁变得可用——它要么获得锁并返回 true，要么如果不能立即获得锁则返回 false。
*   `pg_advisory_unlock(key bigint)`释放独占会话级咨询锁。

下面是一些事务级锁:

*   `pg_advisory_xact_lock(key bigint)`获得独占事务级顾问锁。它的工作方式与`pg_advisory_lock`相同，只是锁在当前事务结束时自动释放，不能显式释放。
*   `pg_try_advisory_xact_lock(key bigint)`获得独占事务级顾问锁(如果可用)。它的工作方式与`pg_try_advisory_lock`相同，只是锁在事务结束时自动释放，不能显式释放。

## 一个简单的锁管理器

我们可以通过创建一个负责获取和释放锁的小类，在 Ruby on Rails 中使用咨询锁。让我们称之为锁管理器。

一个小小的不便是 PostgreSQL 中的咨询锁使用一个数字作为参数。然而，锁定一个特定的数字并不容易理解。如果我们能用一根绳子来达到这个目的，那就更好了。

一个可能的解决方案是使用`Zlib.crc32`散列函数。它通常用于校验和，但我们可以用它将任何字符串转换成 PostgreSQL 所需的数字。

```
class LockManager
  def self.with_transaction_lock(lock_name)
    lock_id = Zlib.crc32(lock_name.to_s)
    ActiveRecord::Base.transaction do
      ActiveRecord::Base.connection.execute("SELECT pg_advisory_xact_lock(#{lock_id})")
      yield
    end
  end

  def self.with_session_lock(lock_name)
    lock_id = Zlib.crc32(lock_name.to_s)
    begin
      ActiveRecord::Base.connection.execute("SELECT pg_advisory_lock(#{lock_id})")
      yield
    ensure
      ActiveRecord::Base.connection.execute("SELECT pg_advisory_unlock(#{lock_id})")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

例如，我们可以这样使用这个类:

```
LockManager.with_transaction_lock("subscription_usage_#{user_id}") do
  user = User.find(user_id)
  new_usage = user.current_subscription_usage
  user.update_attributes!(subscription_usage: new_usage)
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

原文：<https://www.kostolansky.sk/posts/postgresql-advisory-locks/>