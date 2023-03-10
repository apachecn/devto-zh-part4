# 来自反应式 Spring 数据仓库的实时更新流

> 原文：<https://dev.to/lankydandev/streaming-live-updates-from-a-reactive-spring-data-repository-8f>

这篇文章详细介绍了一个简单的实现，将数据库中的*更新流式传输到对该数据感兴趣的任何其他组件。更准确地说，如何修改 Spring Data R2DBC 存储库，以便向相关的订阅者发出事件。*

稍微了解一下 R2DBC 和 Spring 的背景知识对这篇文章会有帮助。我以前的文章，用 Spring Data R2DBC 异步 RDBMS 访问和用于微软 SQL Server 的[Spring Data R2DBC](https://lankydan.dev/spring-data-r2dbc-for-microsoft-sql-server)应该在这方面有所帮助。

正如我提到的，这将是一个幼稚的实现。因此，代码不会是任何花哨的东西。

为了做到这一点，我劫持了一个`SimpleR2dbcRepository`来创建一个存储库实现，它在每次保存新记录时发出一个事件。新事件被添加到一个`DirectProcessor`中，并发送给任何订阅它的`Publisher`。这看起来像:

```
class PersonRepository(
  entity: RelationalEntityInformation<Person, Int>,
  databaseClient: DatabaseClient,
  converter: R2dbcConverter,
  accessStrategy: ReactiveDataAccessStrategy
) : SimpleR2dbcRepository<Person, Int>(entity, databaseClient, converter, accessStrategy) {

  private val source: DirectProcessor<Person> = DirectProcessor.create<Person>()
  val events: Flux<Person> = source

  override fun <S : Person> save(objectToSave: S): Mono<S> {
    return super.save(objectToSave).doOnNext(source::onNext)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

来自`SimpleR2dbcRepository`的唯一需要被覆盖的函数是`save` ( `saveAll`委托给`save`)。将`doOnNext`添加到原来的保存调用中，通过调用`onNext`将新事件推送到`source`(`DirectorProcessor`)。

`source`被转换为`Flux`,以防止存储库外部的类添加新事件。从技术上来说，他们仍然可以添加事件，但他们需要自己进行造型。

您可能已经注意到了，存储库正在加载参数，并将它们传递给`SimpleR2dbcRepository`。储存库的实例需要手动创建，因为它的一些依赖项不能自动注入:

```
@Configuration
class RepositoryConfiguration {

  @Bean
  fun personRepository(
    databaseClient: DatabaseClient,
    dataAccessStrategy: ReactiveDataAccessStrategy
  ): PersonRepository {
    val entity: RelationalPersistentEntity<Person> = dataAccessStrategy
      .converter
      .mappingContext
      .getRequiredPersistentEntity(Person::class.java) as RelationalPersistentEntity<Person>
    val relationEntityInformation: MappingRelationalEntityInformation<Person, Int> =
      MappingRelationalEntityInformation(entity, Int::class.java)
    return PersonRepository(
      relationEntityInformation,
      databaseClient,
      dataAccessStrategy.converter,
      dataAccessStrategy
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

至此，一切都已准备就绪，可以使用了。下面是它工作的一个例子:

```
personRepository.events
  .doOnComplete { log.info("Events flux has closed") }
  .subscribe { log.info("From events stream - $it") }
// insert people records over time
MARVEL_CHARACTERS
  .toFlux()
  .delayElements(Duration.of(1, SECONDS))
  .concatMap { personRepository.save(it) }
  .subscribe() 
```

Enter fullscreen mode Exit fullscreen mode

哪些输出:

```
29-08-2019 09:08:27.674 [reactor-tcp-nio-1] From events stream - Person(id=481, name=Spiderman, age=18)
29-08-2019 09:08:28.550 [reactor-tcp-nio-2] From events stream - Person(id=482, name=Ironman, age=48)
29-08-2019 09:08:29.555 [reactor-tcp-nio-3] From events stream - Person(id=483, name=Thor, age=1000)
29-08-2019 09:08:30.561 [reactor-tcp-nio-4] From events stream - Person(id=484, name=Hulk, age=49)
29-08-2019 09:08:31.568 [reactor-tcp-nio-5] From events stream - Person(id=485, name=Antman, age=49)
29-08-2019 09:08:32.571 [reactor-tcp-nio-6] From events stream - Person(id=486, name=Blackwidow, age=34)
29-08-2019 09:08:33.576 [reactor-tcp-nio-7] From events stream - Person(id=487, name=Starlord, age=38)
29-08-2019 09:08:34.581 [reactor-tcp-nio-8] From events stream - Person(id=488, name=Captain America, age=100)
29-08-2019 09:08:35.585 [reactor-tcp-nio-9] From events stream - Person(id=489, name=Warmachine, age=50)
29-08-2019 09:08:36.589 [reactor-tcp-nio-10] From events stream - Person(id=490, name=Wasp, age=26)
29-08-2019 09:08:37.596 [reactor-tcp-nio-11] From events stream - Person(id=491, name=Winter Soldier, age=101)
29-08-2019 09:08:38.597 [reactor-tcp-nio-12] From events stream - Person(id=492, name=Black Panther, age=42)
29-08-2019 09:08:39.604 [reactor-tcp-nio-1] From events stream - Person(id=493, name=Doctor Strange, age=42)
29-08-2019 09:08:40.609 [reactor-tcp-nio-2] From events stream - Person(id=494, name=Gamora, age=29)
29-08-2019 09:08:41.611 [reactor-tcp-nio-3] From events stream - Person(id=495, name=Groot, age=4)
29-08-2019 09:08:42.618 [reactor-tcp-nio-4] From events stream - Person(id=496, name=Hawkeye, age=47)
29-08-2019 09:08:43.620 [reactor-tcp-nio-5] From events stream - Person(id=497, name=Pepper Potts, age=44)
29-08-2019 09:08:44.627 [reactor-tcp-nio-6] From events stream - Person(id=498, name=Captain Marvel, age=59)
29-08-2019 09:08:45.631 [reactor-tcp-nio-7] From events stream - Person(id=499, name=Rocket Raccoon, age=30)
29-08-2019 09:08:46.637 [reactor-tcp-nio-8] From events stream - Person(id=500, name=Drax, age=49)
29-08-2019 09:08:47.639 [reactor-tcp-nio-9] From events stream - Person(id=501, name=Nebula, age=30) 
```

Enter fullscreen mode Exit fullscreen mode

每秒都会保存一条记录，该记录与来自存储库的事件相匹配。

这就是它的全部内容，至少对于这个基本的实现是如此。我确信还可以做更多的事情，但是我首先需要弄清楚如何去做…总之，通过一些补充，您可以将插入到数据库中的数据流式传输到对正在添加的记录感兴趣的组件。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！