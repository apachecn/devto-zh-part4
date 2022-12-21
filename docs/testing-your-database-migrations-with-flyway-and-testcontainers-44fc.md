# 用 Flyway 和 Testcontainers 测试您的数据库迁移

> 原文：<https://dev.to/frosnerd/testing-your-database-migrations-with-flyway-and-testcontainers-44fc>

# 为什么要进行数据库迁移？

*数据库迁移*通常是数据库中模式和数据迁移的组合。*模式迁移*表示对现有数据库模式的更改，例如添加列或创建新索引。*数据迁移*涉及改变数据库中的现有数据，例如通过归一化列中缺失值的表示，例如将`null`、`""`和`"EMPTY"`转换为`null`。

模式迁移通常与某种类型的数据迁移密切相关，因为您很可能必须接触现有数据，以使其适应新的模式。数据库迁移非常重要，尤其是在需求经常变化的敏捷软件开发环境中。您不希望花费几个月的时间来规划您的模式，而是与您的代码一起增量地更改它。

该职位的其余部分结构如下。下一节将讨论执行数据库迁移的不同策略。之后，我们将了解数据库迁移工具 Flyway。然后，我们将介绍 Testcontainers 项目，作为在真实数据库上测试数据库迁移的一种便捷方式。我们将通过总结主要发现来结束这篇文章。

# 迁移策略

最简单的策略是登录数据库，手动执行迁移*。这对于您个人喜爱的项目来说可能没什么问题，但是很可能无法扩展到多开发人员或多环境的设置。*

 *下一步可以是将您的迁移写在*操作手册*中。这样，其他开发人员(包括您未来的自己)就可以了解应用了哪些迁移，执行了哪些迁移。

如果您想避免从 runbook 复制粘贴，您可以创建*迁移脚本*，例如以 SQL 文件的形式。当拥有一个数据库不在服务之间共享的架构时，将那些迁移脚本放在服务的源代码旁边就变得很自然了。

当使用 [Flyway](https://flywaydb.org/) 或 [Liquibase](https://www.liquibase.org/) 等*迁移工具*自动执行迁移脚本时，可以实现最终形式的自动化。通过将迁移工具与版本控制以及自动化和可重复的部署结合使用，您将能够

*   从头开始创建新的数据库，
*   确保您的所有数据库在不同环境中处于相同、一致的状态，并且
*   以确定的方式将任何数据库迁移到新版本。

让我们在下一节看一个使用 Flyway 的具体例子。

# 迁徙飞行路线

Flyway 迁移可以用 SQL(支持特定于数据库的语法)编写，也可以用 Java 编写。迁移脚本通常是版本化的，应该是不可变的。一旦部署了迁移，任何新的更改都需要通过引入新版本来完成。迁移脚本与源代码一起组织，例如在`src/main/resources` :
中

```
.
└── src
    └── main
        └── resources
            └── db_migrations
                ├── V1.0.0__Customer_table.sql
                ├── V1.0.1__Customer_id_index.sql
                └── V2.0.0__Product_table.sql 
```

Enter fullscreen mode Exit fullscreen mode

每个迁移脚本都包含在迁移到新版本时应该应用到数据库的 SQL 语句。创建客户表，然后向客户 ID 添加一个索引，可能如下所示:

```
-- V1.0.0__Customer_table.sql
create table customers (
    id int,
    last_name varchar(255),
    first_name varchar(255)
); 
```

Enter fullscreen mode Exit fullscreen mode

```
-- V1.0.1__Customer_id_index.sql
create unique index customer_id
  on customers (id) 
```

Enter fullscreen mode Exit fullscreen mode

可以从命令行调用 Flyway，通过 Gradle 或 Maven 等构建工具，或者使用 Flyway Java API。可用的命令有*迁移*、*清理*、*信息*、*验证*、*撤销*、*基线*和*修复*。migrate 命令是最重要的命令，因为它向现有数据库发出新的迁移命令。在这一点上，我们不会深入讨论其他命令的细节。请随意查阅[文档](https://flywaydb.org/documentation/)了解更多信息。

以下 Kotlin 代码显示了如何执行 PostgreSQL 数据库的迁移。

```
val (host, port, dbName, username, password) = getConnectionDetails()
val jdbcUrl = "jdbc:postgresql://$host:$port/$dbName"

Flyway.configure()
    .dataSource(jdbcUrl, username, password)
    .load()
    .migrate() 
```

Enter fullscreen mode Exit fullscreen mode

# 用 Testcontainers 测试迁移

既然我们已经定义了我们的迁移，那么我们如何将它们作为自动化测试的一部分进行测试呢？当我开始开发 web 应用程序时，使用内存数据库(如 [H2](https://www.h2database.com/html/main.html) )进行本地开发并连接到生产环境中的 PostgreSQL 安装是最先进的。

为了测试我们的迁移，我们可以启动内存中的数据库，执行迁移，然后执行 SQL 语句来验证迁移是否按预期执行。但是，如果我们这样做，我们就不能在迁移中使用 PostgreSQL 特定的语法或功能。在许多使用案例中，这是一个节目停止器。我们需要的是一个新的 PostgreSQL 数据库，为每个测试用例自动创建。这可能吗？

[Testcontainers](https://www.testcontainers.org/) 来救援！Testcontainers 是一个 Java 库，它与 JUnit 集成，以 Docker 容器的形式提供数据库和其他服务的一次性实例。如果您正在使用 JUnit 5，您可以简单地使用 Testcontainers 扩展。下面的 Kotlin 代码演示了如何在真实的 PostgreSQL 数据库上测试我们的迁移。

```
@Testcontainers
class DatabaseMigrationsTest {

  @Container
  val postgresContainer = PostgresContainer("postgres:10.6")
    .withDatabaseName("db")
    .withUsername("user")
    .withPassword("password")

  @Test
  fun testSomethingOnYourCustomerTable() {
    testAfterMigration(postgresContainer) { pgClient ->
      val one = pgClient.preparedQueryAwait("select 1 from customers")
        .first()
        .getInt(0)
      assertThat(one).isEqualTo(1)
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

`testAfterMigration`函数是一个助手函数，它首先应用迁移，创建一个[反应式 PostgreSQL 客户端](https://github.com/vietj/reactive-pg-client/)，并将其传递给测试函数。下面的代码展示了它的要点。为了保持代码简短，我分别在`migrate`和`createPgClient`函数中隐藏了运行迁移和创建数据库客户端的细节。容器对象的`connectionDetails`属性是定制代码，用于以方便的方式访问数据库连接细节。

```
fun testAfterMigration(postgresContainer: PostgresContainer, testFunction: suspend (PgClient) -> Unit) {
  migrate(postgresContainer.connectionDetails)
  val pgClient = createPgClient(postgresContainer.connectionDetails)
  runBlocking {
      testFunction(pgClient)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

在这篇文章中，我们讨论了敏捷软件开发环境中数据库迁移的概念和重要性。通过将迁移工具与版本控制迁移脚本和自动化部署结合使用，您可以从头开始创建新的数据库，并以确定的方式迁移现有的数据库实例。感谢 Testcontainers，开发人员可以方便地测试他们的迁移，作为他们自动化测试的一部分，在测试执行期间连接到真实的数据库。

您是如何进行数据库迁移的？你用过 Flyway 或者 Liquibase 这样的工具吗？你遇到过什么问题吗？或者你有什么成功的故事想分享吗？请在评论中告诉我你的想法！

* * *

由 Christophe Benoit 拍摄的封面图片*