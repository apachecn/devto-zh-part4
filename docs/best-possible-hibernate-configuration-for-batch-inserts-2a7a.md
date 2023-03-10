# 批量插入的最佳休眠配置

> 原文：<https://dev.to/smartyansh/best-possible-hibernate-configuration-for-batch-inserts-2a7a>

# 问题

通常，hibernate 实体(域)被设置为使用数据库序列作为 Id 生成器。在这种情况下，每次插入 hibernate 都会对数据库进行两次往返。它将首先往返获取序列的下一个值，以设置记录的标识符。然后再往返一次以插入记录。

假设，我们想在数据库中插入 1000 条记录。这将导致对数据库的总共 2000 次往返(每次往返 1000 次，以获得序列的下一个值并插入记录)。

即使在网络延迟非常低的情况下，执行几千次插入也可能需要大量时间。

第一个主要问题是分别执行每个插入。
第二个主要问题是每次都要往返一次，以获得序列的下一个值。

# 理论解

我们应该通过批量处理插入来减少批量插入到数据库的网络往返。与此同时，减少网络往返，以获得每次插入的序列的下一个值。

# 冬眠方案

**第一个问题:**

我们知道显而易见的事情。

*使用 Hibernate 提供的 [JDBC 批处理](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#batch)。*

在 hibernate 配置文件中设置以下属性。

```
<property name="hibernate.jdbc.batch_size" value="100"/>
<property name="hibernate.order_inserts" value="true"/> 
```

现在，hibernate 将制作一批 100 个插件并订购它们。然后，它将对数据库进行一次网络往返，以插入 100 条记录。

因此，最初插入记录的 1000 次往返将减少到 10 次。

**第二个问题:**

我们将使用增强序列标识符和一个[优化器](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#identifiers-generators-optimizer)策略，比如 pooled 或 pooled-lo，它提供内存中的标识符。因此，它们在内存中保留 id 以备后用。

让我们看看“池化”优化器策略能做什么！

要启用它，我们需要将数据库序列的“增量”设置为 100。

然后，在实体
中设置 *increment_size = 100* 的池化优化器策略

```
@Id
@GenericGenerator(
    name = "sequenceGenerator",
    strategy = "org.hibernate.id.enhanced.SequenceStyleGenerator",
    parameters = {
        @Parameter(name = "sequence_name", value = "hibernate_sequence"),
        @Parameter(name = "optimizer", value = "pooled"),
        @Parameter(name = "initial_value", value = "1"),
        @Parameter(name = "increment_size", value = "100")
    }
)
@GeneratedValue(
    strategy = GenerationType.SEQUENCE,
    generator = "sequenceGenerator"
)
private Long id; 
```

现在，hibernate 将对数据库进行一次往返，并将序列 nextval 设置为下一个 100 值。并且，在内存中保留 100 个值来设置插入的 id。使用这种方法，我们将每插入 100 条记录，进行一次往返获取序列的下一个值。

因此，获取序列的下一个值的最初 1000 次往返将减少到 10 次。

因此，应用这两种解决方案，我们可以将 1000 次插入的往返次数减少到 20 次。

我们可能都知道批量插入优化。但是，序列优化器是一个大赢家。我们当中没有多少人知道这种优化策略已经可用。

听起来是不是很酷很聪明。

伙计们，请和我分享你们的想法。