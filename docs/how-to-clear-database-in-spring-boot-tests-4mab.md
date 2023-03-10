# Spring Boot 考试如何清空数据库？

> 原文：<https://dev.to/brightdevs/how-to-clear-database-in-spring-boot-tests-4mab>

如今，在*单元* <sup>[1](#sup-1)</sup> 测试中使用类似生产的数据库是一种常见的做法。调用一个真实的数据库可以增加我们对一个被测试的代码实际工作的信心。已经说过，数据库，就其本质而言，会将外部状态带入到测试中，这会影响它的行为，因此我们需要特别注意准备测试执行。有几种方法可以处理测试中的数据库状态，我将描述一种我最喜欢的方法。

# Spring Boot 事务性测试的问题

Spring Boot 提供了许多助手，使测试应用程序更容易。在众多选项中，您可以使用 [`@DataJpaTest`](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html#boot-features-testing-spring-boot-applications-testing-autoconfigured-jpa-test) ，默认情况下，它将配置一个内存嵌入式数据库。您可以通过添加`@AutoConfigureTestDatabase(replace=Replace.NONE)`在测试中使用生产类型的数据库，比如:

```
@RunWith(SpringRunner.class)
@DataJpaTest
@AutoConfigureTestDatabase(replace=Replace.NONE)
public class ExampleRepositoryTests {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

`@DataJpaTest`使用引擎盖下的`@Transactional`。测试被包装在事务中，事务在结束时被回滚。这意味着当使用 Hibernate 时，需要特别注意测试代码是如何编写的。[如下面的 Java 示例](https://docs.spring.io/spring/docs/4.3.11.RELEASE/spring-framework-reference/htmlsingle/#testcontext-tx-enabling-transactions)所示，确实需要手动刷新:

```
@RunWith(SpringRunner.class)
@ContextConfiguration(classes = TestConfig.class)
@Transactional
public class HibernateUserRepositoryTests {
    ...
    @Test
    public void createUser() {
        // track initial state in test database:
        final int count = countRowsInTable("user");

        User user = new User(...);
        repository.save(user);

        // Manual flush is required to avoid false positive in test
        sessionFactory.getCurrentSession().flush();
        assertNumUsers(count + 1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在测试中使用`@Transactional`注释当然很容易，但是**我仍然不使用它**,原因如下:

*   生产代码正在使用不同范围的事务。
*   很容易忘记同花顺，从而在测试中出现假阳性。
*   在失败和调试时，很难看到 db 中实际保存了什么值。
*   编写需要提交事务的生产代码测试要困难得多。
*   测试代码需要与生产代码更紧密地耦合，我们都知道它会阻碍重构。

# 用 SQL 清理数据库

在涉及数据库的测试中，我使用普通的旧 SQL 在每次测试之前重置其状态**。这使得测试代码不太依赖于事务在生产代码中的范围。此外，在测试失败**后，用户可以轻松查看保存的**值。事实证明，编写一个 JUnit `@Rule`或 [`BeforeEachCallback`](http://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/extension/BeforeEachCallback.html) 来删除所有表中的所有行是很容易的。此外，我们可以这样做，而不需要对表名进行硬编码，硬编码会增加维护成本。**

让我们从在 Kotlin 中定义一个`@Rule`开始，它将在每次测试之前被调用:

```
import org.junit.rules.ExternalResource
import org.springframework.stereotype.Component
import javax.sql.DataSource

@Component
class DatabaseCleanerRule(private val dataSource: DataSource) : ExternalResource() {

    override fun before() {
        if (databaseCleaner == null) {
            // Consider inspecting dataSource to check if we are connecting to test database
            databaseCleaner = DatabaseCleaner(dataSource::getConnection)
        }
        databaseCleaner!!.reset()
    }

    companion object {
        internal var databaseCleaner: DatabaseCleaner? = null
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑检查`dataSource`以检查我们是否将要连接到测试数据库，以及**是否不是用于开发的数据库。**使用不正确的 Spring Profile 并删除您的开发数据是非常容易的。问我怎么知道的？

我们可以在启用 spring 的测试中使用`DatabaseCleanerRule`作为任何其他 JUnit 规则，例如`@Rule @Inject lateinit var cleanerRule: DatabaseCleanerRule`。

注意，我们已经将实际的重要工作委托给了下面 Kotlin 中定义的`DatabaseCleaner`类。

```
import com.practi.util.iterator
import org.slf4j.LoggerFactory
import java.sql.Connection
import java.sql.PreparedStatement
import java.sql.SQLException

class DatabaseCleaner(private val connectionProvider: () -> Connection) {
    private val tablesToExclude = mutableSetOf<String>()
    private var tablesForClearing: List<TableRef>? = null

    fun excludeTables(vararg tableNames: String) {
        tablesToExclude += tableNames.flatMap { listOf(it, it.toLowerCase()) }
    }

    fun reset() {
        if (notPrepared) {
            prepare()
        }
        executeReset()
    }

    private val notPrepared get() = tablesForClearing == null

    private fun prepare() {
        connectionProvider().use { connection ->
            val metaData = connection.metaData
            val tableRefs = metaData.getTables(connection.catalog, null, null, arrayOf("TABLE")).use { tables ->
                iterator(tables::next) { tables.getString("TABLE_NAME") }
                    .asSequence()
                    .filterNot(tablesToExclude::contains)
                    .map(::TableRef)
                    .toList()
            }

            tablesForClearing = tableRefs

            LOG.info("Prepared clean db command: {}", tablesForClearing)
        }
    }

    private fun executeReset() {
        try {
            connectionProvider().use { connection ->
                val reset = buildClearStatement(connection)
                val result = reset.executeBatch()
                result
            }
        } catch (e: SQLException) {
            val status = engineInnoDbStatus()
            LOG.error("Failed to remove rows because {}. InnoDb status: {}", e, status)
            throw e
        }
    }

    private fun engineInnoDbStatus(): String { ... }

    private fun buildClearStatement(connection: Connection): PreparedStatement {
        val reset = connection.prepareStatement("")
        reset.addBatch("SET FOREIGN_KEY_CHECKS = 0")
        tablesForClearing?.forEach { ref ->
            reset.addBatch("DELETE FROM `${ref.name}`")
        }
        reset.addBatch("SET FOREIGN_KEY_CHECKS = 1")
        return reset
    }

    data class TableRef(val name: String)

    companion object {
        private val LOG = LoggerFactory.getLogger(DatabaseCleaner::class.java)!!
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们已经定义了允许我们省略某些表的`tablesToExclude`集合。当您使用将状态存储在一些表中的数据库迁移工具时，这很方便。

[JDBC 元数据](https://docs.oracle.com/javase/7/docs/api/java/sql/DatabaseMetaData.html)允许我们在不考虑数据库供应商的情况下自省模式。`iterator`是一个很小的 Kotlin 函数，帮助消费类似迭代器的对象:

```
inline fun <T> iterator(crossinline next: () -> Boolean, crossinline value: () -> T): AbstractIterator<out T> = object : AbstractIterator<T>() {
    override fun computeNext() {
        if (next()) {
            setNext(value())
        } else {
            done()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`buildClearStatement`构建了一个大型查询，这个查询从每个相关的表中提取所有的行。上面的例子使用了 MySQL，在这里很容易禁用外键检查。这很重要，因为外键会阻止行被删除，除非我们特别注意删除的顺序。在[重生项目](https://github.com/jbogard/Respawn)中可以找到一个更一般的例子，说明如何在清除数据库时处理参照完整性。

最后但同样重要的是，当抛出一个`SQLException`时，我们记录伴随着 [`SHOW ENGINE INNODB STATUS`](https://dev.mysql.com/doc/refman/5.7/en/show-engine.html) 的异常。状态信息可以提示我们失败的原因，例如，对同一个数据库执行的另一个测试过程，或者锁定一些行的恶意的、失控的线程。

```
private fun engineInnoDbStatus(): String {
    return connectionProvider().use { connection ->
        connection.createStatement().executeQuery("SHOW ENGINE INNODB STATUS ").use {
            iterator(it::next) { it.getString("Status") }.asSequence().toList()
        }.joinToString(System.lineSeparator())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上述示例表明，手动重置数据库并不难。我发现使用这种方法使我的测试更加可信，并且与底层持久层的耦合性更低。事实上，我们可以很容易地在一个性能关键的代码区域从 JPA 切换到`JdbcTemplate`,而不需要改变测试。

*<sup>1</sup>* 其实无论是单元还是集成测试都是不同的话题。