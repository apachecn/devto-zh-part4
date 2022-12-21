# Rails 6 中的高级多数据库技术

> 原文：<https://dev.to/prathamesh/advanced-multi-db-techniques-in-rails-6-14mb>

Rails 6 就在眼前。候选版本 2 最近发布了。这篇文章是 _**Rails 6 之路**系列的一部分，它将帮助你为 Rails 6 做好准备。

查看这篇文章的第一部分，我们看到了[如何在 Rails 6 中设置一个具有多个数据库的应用程序，并将其部署到 Heroku](https://dev.to/prathamesh/setting-up-rails-6-multiple-databases-on-heroku-j0k) 。

在本文中，我们不会讨论多个数据库，但是我们将讨论同一个数据库的读写实例，以及如何在一个 Rails 应用程序中管理它们。

> 在本文中，我将数据库的读取副本和主/主实例称为读取和写入实例。这意味着读取操作应由读取复制副本实例处理，写入操作应由写入/主/主实例处理，并应同步到复制副本实例以供后续读取。

### 读取副本

我们之前看到，我们可以连接到`animals`数据库进行读写操作。

```
class AnimalBase < ApplicationRecord
  connects_to database: { writing: :animals }
end 
```

Enter fullscreen mode Exit fullscreen mode

通过将一个`reading`角色传递给上面的`connects_to`调用，我们可以开始使用一个读取副本来从 animals 数据库进行读取操作。

```
 class AnimalBase < ApplicationRecord
  connects_to database: { writing: :animals, reading: :animals_replica }
end 
```

Enter fullscreen mode Exit fullscreen mode

> 这个`role`不同于作为主要 SQL 数据库一部分的数据库角色。这个`role`指定了您想要在数据库上执行哪种操作，读还是写，它特定于 Rails 如何实现读写操作。

我们需要在`database.yml`中指定`animals_replica`数据库配置来完成这项工作。

```
production:
  primary:
    adapter: postgresql
  animals:
    migrations_paths: db/animal_migrate
    adapter: postgresql
    url: <%= ENV["HEROKU_POSTGRESQL_OLIVE_URL"] %>
  animals_replica:
    adapter: postgresql
    url: <%= ENV["HEROKU_POSTGRESQL_PURPLE_URL"] %>
    replica: true 
```

Enter fullscreen mode Exit fullscreen mode

> 确保您没有对`reading`和`writing`角色使用相同的数据库实例。Rails 建议副本数据库实例是独立的，并创建一个只读用户。Rails 假设这是由您手动完成的，您只是提供连接信息供 Rails 使用。

我们没有为副本数据库添加`migrations_paths`配置，但是我们为它添加了`replica: true`。`replica: true`配置很重要，它向 Rails 传达了这个数据库配置应该被视为副本数据库的信息。

既然我们已经指定了哪个数据库应该用于写操作，哪个数据库应该用于读操作，Rails 将根据请求类型在它们之间进行切换。

### 读取副本和写入数据库之间的自动连接切换

如果我们的应用程序收到任何修改数据库的请求，比如 POST、PUT、DELETE、PATCH，那么这个请求就会被 Rails 自动发送到`write`数据库。如果应用程序收到任何只是从数据库中读取内容的请求，它将转到副本。

但是，如果我们在收到写请求后 2 秒内立即收到读请求，那么它将转到主数据库。如果您的应用程序不保证向用户显示实时数据，那么您可以在读副本中不立即更新实时数据，但是 Rails 提供了一种方法来确保至少在写操作后的前两秒钟内，读请求由写实例处理。这假定您的读取副本将在 2 秒内用实时数据更新。该设置可通过以下配置进行控制。

```
config.active_record.database_selector = 2.seconds 
```

Enter fullscreen mode Exit fullscreen mode

我们可以根据副本延迟在`environments/production.rb`中配置它。

上次写操作的时间戳由 Rails 存储在应用程序会话中，并由`config.active_record.database_resolver_context`控制。

这个自动连接作为一个机架中间件包含在 Rails 应用程序中，因此它只对应用程序的 HTTP 请求可用。对于后台作业或 rake 任务，默认情况下，Rails 会将所有查询发送给 write 实例。Rails 还允许在读取和写入数据库之间手动切换，我们接下来会看到这一点。

### 写和读数据库之间的手动切换

Rails 提供了在读写数据库之间切换的`ActiveRecord::Base.connected_to`方法。

```
Post.connected_to(role: :reading) do
  post_ids = Post.all.pluck(:id)
  ProcessBulkPostRepliesWorker.perform(post_ids: post_ids)
end 
```

Enter fullscreen mode Exit fullscreen mode

这确保了块使用读取副本。它查找连接到`reading`角色的所有连接，然后使用该连接在块中执行查询。如果连接不存在，它将在执行块之前创建一个连接。

我们也可以将`database`传递给`connected_to`方法。

```
ActiveRecord::Base.connected_to(database: animals) do
  CollectLionsInformationWorker.perform(Lion.all.pluck(:id, :continent, :age))
end 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以通过`database`键下的`role`。

```
ActiveRecord::Base.connected_to(database: { reading: :animals }) do
  CollectLionsInformationWorker.perform(Lion.all.pluck(:id, :continent, :age))
end 
```

Enter fullscreen mode Exit fullscreen mode

这将使用到`animals`数据库的读取副本的连接，并使用它。

但是我们不能将`role`和`database`都传递给`connected_to`方法。我们只能通过其中一个。这意味着，当我们想要将角色从阅读转换到写作再到阅读时，我们需要在正确的类上调用`connected_to`。我们可以使用任何像`Post`这样的模型，它连接到我们想要切换角色的数据库。或者，如果我们想要连接到`primary`数据库的读取副本，我们也可以使用`ActiveRecord::Base`并传递`reading`角色。

当我们将`database`键传递给`connected_to`方法时，我们会在该块的持续时间内将数据库一起从`primary`切换到`animals`。如果我们没有为新的数据库传递角色，那么将自动与`writing`角色建立连接。

通过这种方式，我们可以管理连接到特定脚本、后台作业甚至特定请求中的不同数据库，这些请求不能依赖 Rails 提供的自动连接切换。

### Rails 6 中的多 db 特性没有提供什么？

Rails 6 不支持诸如分片、负载平衡副本、跨多个数据库连接等现成的特性。将来可能会添加其中的一些功能。

### 想留在通往 Rails 6 的路上？

订阅我的[时事通讯](https://www.prathamesh.tech/mailing-list)，每周收到一封电子邮件，了解如何让你的应用为 Rails 6 做好准备。