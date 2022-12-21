# 通过委托扩充 Spring 数据库

> 原文：<https://dev.to/lankydandev/augmenting-a-spring-data-repository-through-delegation-5efp>

最近写了几篇关于科特林代表团的帖子。在这样做的过程中，我意识到了一种将它应用于 Spring 数据仓库的有用方法。这将允许 Spring 数据在提供定制路线的同时继续发挥一些魔力。本文中显示的代码是用 Kotlin 编写的，但仍然与 Java 相关。

这篇文章使用 R2DBC，但是内容足够普通，可以适用于任何 Spring 数据模块。

如果你在这些领域没有太多的背景知识，阅读 Kotlin 中的[异步 RDBMS 访问与 Spring 数据 R2DBC](https://lankydan.dev/2019/02/16/asynchronous-rdbms-access-with-spring-data-r2dbc) 和[类委托将会很有帮助。](https://dev.to/lankydandev/class-delegation-in-kotlin-4dpi)

作为总结。Spring 数据提供的魔力是什么？

Spring Data 允许您编写一个接口，您只需要定义您需要的查询。然后，它将为您完成创建实现和注入依赖项的所有工作。这看起来有点像:

```
@Repository
interface PersonRepository : R2dbcRepository<Person, Int> {

  @Query("SELECT * FROM people WHERE age > $1")
  fun findAllByAgeGreaterThan(age: Int): Flux<Person>
} 
```

> 由于使用的是 Spring Data R2DBC，因此还不完全支持完全推断查询。这就是为什么查询是手动写出的。

这样做的缺点是它创建了一个基于接口的实现。因此，如果您想进行任何定制，您将需要自己创建一个接口实例，注入它的依赖项并实现每个查询。例如:

```
class PersonRepositoryImpl(
  private val entity: RelationalEntityInformation<Person, Int>,
  private val databaseClient: DatabaseClient,
  converter: R2dbcConverter,
  private val accessStrategy: ReactiveDataAccessStrategy
) : SimpleR2dbcRepository<Person, Int>(entity, databaseClient, converter, accessStrategy),
  PersonRepository {

  override fun findAllByAgeGreaterThan(age: Int): Flux<Person> {

    val mapper: StatementMapper.TypedStatementMapper<Person> =
      accessStrategy.statementMapper.forType(entity.javaType)

    val selectSpec: StatementMapper.SelectSpec = mapper
      .createSelect(entity.tableName)
      .withProjection(accessStrategy.getAllColumns(entity.javaType))
      .withCriteria(Criteria.where("age").greaterThan(age))

    val operation: PreparedOperation<*> = mapper.getMappedObject(selectSpec)

    return databaseClient.execute().sql(operation).`as`(entity.javaType).fetch().all()
  }
} 
```

> 是的，那个查询代码可能很糟糕，我相信您可以做得更好。不过，你明白我的意思。

创建这个类的痛苦可以通过委托给基于您的接口实现的库 Spring 来消除。然后，您可以添加所有您需要的定制。

在科特林，这看起来像:

```
@Repository
class DelegatingPersonRepository(private val delegate: PersonRepository) :
  PersonRepository by delegate {

  override fun <S : Person> save(objectToSave: S): Mono<S> {
    // override `save` implementation
  }

  // any other overrides (kotlin provides delegated implementations)
} 
```

在 Java 中，这有点麻烦，但仍然很容易实现:

```
@Repository
public class DelegatingPersonRepository implements PersonRepository {

  private final PersonRepository delegate;

  public DelegatingPersonRepository(PersonRepository delegate) {
    this.delegate = delegate;
  }

  @Override
  public Flux<Person> findAllByAgeGreaterThan(int age) {
    return delegate.findAllByAgeGreaterThan(age);
  }

  @Override
  public <S extends Person> Mono<S> save(S entity) {
    // override `save` implementation
  }

  // all other implementations of `PersonRepository` functions
} 
```

> 在这两个版本中，`DelegatingPersonRepository`调用在`PersonRepository`中定义的`findAllByAgeGreaterThan`的实现。到目前为止，还没有人直接写一个函数来查询数据库。

当使用`DelegatingPersonRepository`时，所有未被覆盖的函数调用将委托给 Spring 创建的`PersonRepository`的实现。

对于像我这样不喜欢将 SQL 查询放在一起并编写所有转换代码的人来说。以这种方式使用委托确实可以让您利用 Spring 数据的优势，同时还能让您有空间定制结果。您节省的代码量实际上可能并不多。但是，将它们组合在一起所需的工作量却大大减少了。让春天为你做所有的重活吧！

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！