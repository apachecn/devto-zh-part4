# 数据库视图以及如何在基于 Rails 的应用程序中使用它们？

> 原文：<https://dev.to/netguru/database-views-and-how-to-use-them-in-a-rails-based-application-5dih>

大多数时候，当我们需要在基于 Rails 的应用程序中查询数据库中的一些数据时，我们只需使用 *ActiveRecord* 查询接口。当查询更复杂时，我们使用 SQL 语言编写它。有时可能需要在多个地方使用这样的 SQL 查询。在这种情况下(为了更好地坚持 DRY 原则)，我们可以考虑使用数据库视图。在这篇博文中，我将介绍什么是数据库视图，以及如何在一个*风景*宝石的帮助下，在 Ruby on Rails 应用程序中轻松使用它们。

## 数据库视图

数据库视图是存储在数据库管理系统(DBMS)中的查询结果集。每次需要访问数据时，都必须执行存储查询。尽管数据库视图没有连接到任何持久化的数据表，但是可以将它作为常规表进行查询(这就是为什么视图通常被称为*虚拟表*)。

有几种类型的数据库视图:

*   **只读视图** -只能用于查询目的。
*   **可更新视图**——可用于查询和数据管理(*插入*、*更新*、*删除*操作)目的，这类视图有一些限制，比如子句中只有一个*或者视图的查询定义中没有聚合函数。并非所有 DBMS 都支持这种类型的视图。*
*   **物化视图**——在访问视图时提供数据的静态快照，以提高查询性能。与该视图相关的查询仅在视图创建和按需(物化视图刷新操作)后执行。物化视图也可以作为常规数据表进行索引，以获得更好的性能。并非所有 DBMS 都支持这种类型的视图。

数据库视图给我们带来许多好处，例如:

*   **更多干巴巴的数据库查询**——我们可以将许多地方使用的子查询提取到一个视图中，并使用简单的*连接*将视图的数据包含在主查询中。
*   **一个额外的抽象层次**——我们可以将非常复杂的 SQL 查询封装成一个简单易用的视图。
*   **更好的查询性能** -当我们知道数据将会比更新更频繁地被读取，或者有可能继续使用一些缓存数据而不是最新的数据时，我们可以使用物化视图(如果需要，可以使用额外的索引)。这将使查询过程更加高效。

## *景区*宝石

*Scenic* 是一个 gem，它允许在基于 Ruby on Rails 的应用程序中轻松使用数据库视图，而不必将数据库模式切换到 SQL 格式。它支持视图的版本控制，并为 PostgreSQL 提供现成的支持。对于 SQLite、MySQL 或 SQL Server 等其他 DBMS，还有其他可用的适配器。下面，我们将看看创业板提供的主要功能。

### 创建新视图

在将 Scenic 包含在*gem 文件*中之后，我们可以使用 gem 提供的生成器。假设我们有一个*用户*表，其中一列被称为*活动的*，表示用户是活动的。我们希望创建一个只返回活动用户的视图(该视图的*活动*字段等于*真*)。首先，我们需要通过在控制台中键入:
来创建视图

```
$ rails generate scenic:view active_users
   create  db/views/search_results_v01.sql # this is a view's SQL query file
   create  db/migrate/[TIMESTAMP]_create_active_users.rb # this is a migration file 
```

作为这个命令的效果，我们得到两个文件。首先，让我们看一下迁移文件:

```
class CreateActiveUsers < ActiveRecord::Migration
  def change
    create_view :active_users
  end
end 
```

这里，我们使用 gem 提供的 *create_view* 函数。作为参数，我们需要提供视图的名称。我们不需要在这个文件中做更多的事情。现在让我们转到 SQL 查询文件。在这里，我们需要提供数据库视图的 SQL 查询。让我们编写一个简单的查询来从数据库中获取所有活动用户。

```
SELECT * FROM users WHERE active = TRUE; 
```

值得一提的是，所有视图的 SQL 查询都是版本化的。如果我们想修改视图背后的逻辑，我们需要创建一个新版本。每个查询版本都可以在许多迁移中使用(例如，前面提到的 *create_view* 函数带有一个可选参数 *version* ，如果没有给定值，则默认为 1)。

在提供视图的 SQL 查询后，我们可以运行迁移。之后，我们就可以使用新创建的数据库视图了。假设我们在数据库中有这样的记录。

```
irb(main):001:0> User.all
=> #<ActiveRecord::Relation [#<User id: 1, active: true>, #<User id: 2, active: false>]> 
```

在调用我们的数据库视图(通过执行一个原始 SQL 查询)之后，我们得到了唯一一个 id 为*1 的用户(因为只有这个用户是活动的)。* 

```
irb(main):002:0> ActiveRecord::Base.connection.exec_query('SELECT * FROM active_users').rows
  SQL (1.2ms)  SELECT * FROM active_users
=> [["1", "t"]] 
```

### 作为活动记录模型查看

好的，但是我们是否仅限于在 SQL 查询中使用视图？幸运的是，答案是:不。我们可以基于我们的视图创建一个 *ActiveRecord* 模型，它将像常规 ar 的模型一样工作。只有一个例外:这种模型提供的数据是只读的。根据我们的观点，最简单的模型可以是这样的:

```
class ActiveUser < ActiveRecord::Base
  self.primary_key = :id

  def readonly?
    true
  end
end 
```

在这段代码中，我们将模型的*主键*设置为视图返回的 *id* 。这不是必需的，但有助于更好地将我们的视图映射到 AR 的模型，如果没有它，我们将得到带有 *id* 字段的对象总是等于 *nil* 。然后，我们将我们的模型标记为只读，这样当我们在这样一个模型的实例上意外调用 *save* 时，AR 甚至不会尝试访问数据库。

现在，我们可以回到 Rails 控制台，尝试使用新定义的模型:

```
irb(main):003:0> ActiveUser.all
=> #<ActiveRecord::Relation [#<ActiveUser id: 1, active: true>]> 
```

感觉很好，不是吗？

### 更新视图

假设我们在*用户*表中添加了一个新列(名为*全名*)。现在，我们的数据看起来像这样:

```
irb(main):004:0> User.all
=> #<ActiveRecord::Relation [#<User id: 1, active: true, full_name: 'Jan Kowalski'>, #<User id: 2, active: false, full_name: 'James Bond'>]> 
```

让我们调出活跃用户视图:

```
irb(main):005:0> ActiveUser.all
=> #<ActiveRecord::Relation [#<ActiveUser id: 1, active: true>]> 
```

如您所见，新的 *full_name* 列没有反映在数据库视图中。这是因为一些 DBMS(比如在准备这个例子时使用的 PostgreSQL)冻结了视图返回的列。因此，即使我们使用了 *** 选择器，视图创建后添加的列也不会包含在结果集中。为了获得*全名*列，我们需要更新视图。为此，我们可以使用创建视图时使用的现有 SQL 查询版本，但是这一次(出于学术目的)我们将创建一个新的查询版本(这一次没有使用 *** 选择器，以避免将来混淆)。

让我们执行第一次生成视图时使用的命令:

```
$ rails generate scenic:view active_users
   create  db/views/search_results_v02.sql # new SQL query version
   create  db/migrate/[TIMESTAMP]_update_active_users_to_version_2.rb # update migration file 
```

如您所见，我们得到了与上次执行命令后不同的输出。 *Scenic* gem 已经识别出所请求视图的存在，因此它创建了一个更新迁移文件(而不是创建)以及一个新的 SQL 查询版本文件。我们来看看迁移文件:

```
class UpdateActiveUsersToVersion2 < ActiveRecord::Migration
  def change
    update_view :active_users, version: 2, revert_to_version: 1
  end
end 
```

为了更新视图，我们使用了 *update_view* 函数。它将首先删除现有的视图版本，然后重新创建它。作为参数，我们传递当前版本( *revert_to_version* 参数)和更新后视图的期望版本( *version* 参数)。

为了更新视图，但不首先删除它，我们可以使用 *replace_view* 函数。它接受与 *update_view* 相同的参数，但是使用该函数有一些限制(例如，我们只能在视图返回的列列表的末尾添加额外的新列)。您可以在*景区*文档中找到关于此功能的更多信息

现在，让我们转到新生成的 SQL 查询文件:

```
SELECT * FROM users WHERE active = TRUE; 
```

新的查询文件已经填充了以前版本的 SQL 查询。让我们修改它，这样我们的视图将返回 *id* 、*活动*和*全名*列，但不使用 *** 选择器:

```
SELECT id, active, full_name FROM users WHERE active = TRUE; 
```

现在，当我们迁移数据库时，我们应该在视图的结果集中获得 *full_name* 列。让我们在 Rails 控制台中测试它:

```
irb(main):006:0> ActiveUser.all
=> #<ActiveRecord::Relation [#<ActiveUser id: 1, active: true, full_name: 'Jan Kowalski'>]> 
```

一切都在按预期运行。

### *景区*中的物化视图

如前所述，物化视图可以提供某种缓存数据，而不是每次引用视图时都执行查询，从而提高性能。它们也可以作为常规数据库表进行索引。Scenic 提供了对此类视图的支持。

比方说，我们希望将活动用户视图迁移到一个物化形式，并在其上添加一些索引。创建视图步骤中出现的生成器命令和函数接受一个可选参数来创建实体化视图。然而，让我们尝试迁移现有的视图，而不是删除现有的视图并创建另一个版本(复制 SQL 查询)。为此，让我们创建一个新的迁移文件:

```
class MigrateActiveUsersToMaterializedView < ActiveRecord::Migration
  def up
    drop_view :active_users
    create_view :active_users, version: 2, materialized: true

    add_index :active_users, :full_name
  end

  def down
    remove_index :active_users, :full_name

    drop_view :active_users, materialized: true
    create_view :active_users, version: 2
  end
end 
```

如您所见，我们定义了两个方法: *up* (运行迁移时执行)和 *down* (恢复迁移时执行)。在 *up* 方法中，我们首先从数据库中删除现有视图，然后以物化形式重新创建它(使用之前提供的相同 SQL 查询版本)。在这个阶段，DBMS 执行与视图相关的查询并存储其结果。当物化视图准备好时，我们还在 *full_name* 列上添加一个索引。

在 *down* 方法中，我们做一组相反的操作。首先，我们删除了 *full_name* 列上的索引，然后我们删除了物化视图，最后我们将活动用户重新创建为常规数据库视图。

在执行这样的迁移之后，活动用户视图应该被迁移到物化形式。这个视图的查询性能提升可能不太明显，因为这是一个非常简单的视图。对于更复杂的应用程序，性能的提高可能更显著。

## 总结

数据库视图是关系型 DBMS 提供的强大工具。如果使用得当，我们会受益匪浅。gem 允许我们在基于 Ruby on Rails 的应用程序中轻松使用视图。我们还可以基于视图创建一个 *ActiveRecord* 模型。

### 进一步阅读

[风景宝石文档](https://github.com/scenic-views/scenic)

[PostgreSQL 文档-数据库视图](https://www.postgresql.org/docs/9.3/tutorial-views.html)

[PostgreSQL 文档-物化视图](https://www.postgresql.org/docs/current/rules-materializedviews.html)

凯文·Ku 在 [Unsplash](https://unsplash.com/search/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

**这篇博文最初发表在[网络大师的代码故事](https://www.netguru.com/codestories/database-views-and-how-to-use-them-in-a-ror-based-app)T3 上**