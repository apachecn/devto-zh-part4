# Ktor - a Kotlin web framework

> 原文：<https://dev.to/lankydandev/ktor-a-kotlin-web-framework-3hoj>

Ktor 是一个异步 web 框架，用 Kotlin 编写并为其设计。允许 Kotlin 更令人印象深刻的特性，如协程，不仅被使用，而且作为一等公民得到支持。通常情况下，Spring 是我的首选通用框架，并且通常是我在需要组装 REST API 时使用的。但是，在最近参加了伦敦 Kotlin meetup 后，我决定尝试一次新的东西。这就是我为什么会在这里，写一篇关于 Ktor 的博客。所以，这篇文章对你我来说都是一次学习的经历。这篇文章的内容将缺乏有经验的建议，而是记录我第一次玩 Ktor 的旅程。

这里有一些关于 Ktor 的背景信息。它得到了 Jetbrains 的支持，他们也是 Kotlin 本身的创造者。有谁比从事语言工作的人更适合制作 Kotlin web 框架呢？

## 实现

### 依赖关系

```
buildscript {
  ext.kotlin_version = '1.3.41'
  ext.ktor_version = '1.2.2'

  repositories {
    mavenCentral()
  }
  dependencies {
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
  }
}

apply plugin: 'java'
apply plugin: 'kotlin'

// might not be needed but my build kept defaulting to Java 12
java {
  disableAutoTargetJvm()
}

// Ktor uses coroutines
kotlin {
  experimental {
    coroutines "enable"
  }
}

compileKotlin {
  kotlinOptions.jvmTarget = "1.8"
}
compileTestKotlin {
  kotlinOptions.jvmTarget = "1.8"
}

dependencies {
  // Kotlin stdlib + test dependencies

  // ktor dependencies
  compile "io.ktor:ktor-server-netty:$ktor_version"
  compile "io.ktor:ktor-jackson:$ktor_version"
  // logback for logging
  compile group: 'ch.qos.logback', name: 'logback-classic', version: '1.2.3'
  // kodein for dependency injection
  compile group: 'org.kodein.di', name: 'kodein-di-generic-jvm', version: '6.3.0'
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情。

*   Ktor 需要 Kotlin `1.3`的最低版本，以便可以利用协程。

*   引入了对`ktor-server-netty`和`ktor-jackson`的依赖。顾名思义，这意味着 [Netty](https://netty.io/) 将被用于这个职位。根据您选择导入的内容，可以使用不同的基础 web 服务器。目前剩下的选项是[突堤](https://www.eclipse.org/jetty/)和[雄猫](http://tomcat.apache.org/)。

*   [Logback](https://logback.qos.ch/) 用于处理日志记录。这并不包含在 Ktor 依赖项中，如果您计划进行任何类型的日志记录，这是必需的。

*   [Kodein](https://kodein.org/Kodein-DI/) 是一个用 Kotlin 编写的依赖注入框架。我在这篇文章中不严格地使用了它，由于代码示例的大小，我可能会将它完全删除。它存在的主要原因是为我提供了另一个使用 Spring 之外的东西的机会。记住这也是我尝试 Ktor 的原因之一。

### 启动 web 服务器

随着无聊的东西的方式，我现在可以运行您通过实现一个简单的 web 服务器。下面的代码就是你需要的全部:

```
fun main() {
  embeddedServer(Netty, port = 8080, module = Application::module).start()
}

fun Application.module() {
  // code that does stuff which is covered later
} 
```

Enter fullscreen mode Exit fullscreen mode

砰。这就是了。用 Ktor 和 Netty 运行的简单 web 服务器。好的，是的，它并没有真正做什么，但是我们将在下面的章节中对此进行扩展。代码非常简单明了。唯一值得强调的是`Application.module`函数。`embeddedServer`的`module`参数需要`Application`的扩展功能。这将是让服务器做事情的*主*函数。

在接下来的部分中，我们将扩展`Application.module`的内容，以便您的 web 服务器实际上做一些有价值的事情。

### 路由

目前，所有传入的请求都将被拒绝，因为没有端点来处理它们。通过设置路由，您可以指定请求可以经过的有效路径，以及当请求到达目的地时处理请求的函数。

这是在一个`Routing`块(或多个`Routing`块)内完成的。在一个街区内，建立了通往不同端点的路线:

```
routing {
  // all routes defined inside are prefixed with "/people"
  route("/people") {
    // get a person
    get("/{id}") {
      val id = UUID.fromString(call.parameters["id"]!!)
      personRepository.find(id)?.let {
        call.respond(HttpStatusCode.OK, it)
      } ?: call.respondText(status = HttpStatusCode.NotFound) { "There is no record with id: $id" }
    }
    // create a person
    post {
      val person = call.receive<Person>()
      val result = personRepository.save(person.copy(id = UUID.randomUUID()))
      call.respond(result)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`routing`是一个小小的方便函数，让代码流更流畅一点。`routing`中的上下文(又名`this`)属于`Routing`类型。另外，`route`、`get`和`post`都是`Routing`的扩展功能。

`route`设置一个基本路径到其后的所有端点。在这个场景中，`/people`。`get`和`post`本身不指定路径，因为基本路径足以满足他们的需求。如果需要，可以给每一个添加一个路径，例如:

```
routing {
  // get a person
    get("/people/{id}") {
      val id = UUID.fromString(call.parameters["id"]!!)
      personRepository.find(id)?.let {
        call.respond(HttpStatusCode.OK, it)
      } ?: call.respondText(status = HttpStatusCode.NotFound) { "There is no record with id: $id" }
    }
  // create a person
  post("/people) {
    val person = call.receive<Person>()
    val result = personRepository.save(person.copy(id = UUID.randomUUID()))
    call.respond(result)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在您进入下一节之前，我想向您展示我实际上是如何实现路由的:

```
fun Application.module() {
  val personRepository by kodein.instance<PersonRepository>()
  // route requests to handler functions
  routing { people(personRepository) }
}

// extracted to a separate extension function to tidy up the code
fun Routing.people(personRepository: PersonRepository) {
  route("/people") {
    // get a person
    get("/{id}") {
      val id = UUID.fromString(call.parameters["id"]!!)
      personRepository.find(id)?.let {
        call.respond(HttpStatusCode.OK, it)
      } ?: call.respondText(status = HttpStatusCode.NotFound) { "There is no record with id: $id" }
    }
    // create a person
    post {
      val person = call.receive<Person>()
      val result = personRepository.save(person.copy(id = UUID.randomUUID()))
      call.respond(result)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将代码提取到一个单独的函数中，以减少`Application.module`的内容。当你试图写一个更重要的应用程序时，这将是一个好主意。我如何着手这件事是否符合 T2 的方式，则是另一个问题。通过快速浏览 Ktor 文档，看起来这是一个不错的解决方案。我相信我看到了另一种方法，但我需要花更多的时间。

### 请求处理程序的内容

当请求被路由到请求处理程序时执行的代码显然非常重要。函数终究需要做点什么…

每个处理函数都在协程的上下文中执行。我没有真正利用这个事实，因为我展示的每个函数都是完全同步的。关于这方面的更多信息，Ktor 文档中有一个异步例子。

在这篇文章的剩余部分，我将尽量不要过多地提到协程，因为它们对于这个简单的 REST API 并不是特别重要。

在本节中，我们将更仔细地研究`get`函数:

```
get("/{id}") {
  val id = UUID.fromString(call.parameters["id"]!!)
  personRepository.find(id)?.let {
    call.respond(HttpStatusCode.OK, it)
  } ?: call.respondText(status = HttpStatusCode.NotFound) { "There is no record with id: $id" }
} 
```

Enter fullscreen mode Exit fullscreen mode

`{id}`表示请求中需要一个路径变量，其值将存储为`id`。可以包含多个路径变量，但是本例中只需要一个👍。`id`的值是从`call.parameters`中获取的，它接受你想要访问的变量的名称。

*   `call`表示当前请求的上下文。
*   `parameters`是请求的参数列表。

使用路径变量中的`id`,数据库搜索相应的记录。在这种情况下，如果它存在，记录将与适当的`200 OK`一起返回。如果没有，将返回一个错误响应。`respond`和`respondText`都改变了当前`call`的底层`response`。您可以手动完成此操作，例如，使用:

```
call.response.status(HttpStatusCode.OK)
call.response.pipeline.execute(call, it) 
```

Enter fullscreen mode Exit fullscreen mode

您可以这样做，但是没有必要这样做，因为这实际上只是`respond`的实现。`respondText`有一些额外的逻辑，但委托给`response`来完成一切。这个函数中对`execute`的最后一次调用代表了函数的*返回*值。

### 安装额外功能

在 Ktor 中，额外的*功能*可以在需要时插入。例如，可以添加 [Jackson JSON 解析](https://github.com/FasterXML/jackson)来处理并从应用程序返回 JSON。下面是安装到示例应用程序中的特性:

```
fun Application.module() {
  install(DefaultHeaders) { header(HttpHeaders.Server, "My ktor server") }
  // controls what level the call logging is logged to
  install(CallLogging) { level = Level.INFO }
  // setup jackson json serialisation
  install(ContentNegotiation) { jackson() }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `DefaultHeaders`将服务器名称添加到每个响应的头中。

*   `CallLogging`记录关于传出响应的信息，并指定记录它们的级别。要做到这一点，需要包含一个日志库。输出将类似于:

```
INFO ktor.application.log - 200 OK: GET - /people/302a1a73-173b-491c-b306-4d95387a8e36 
```

Enter fullscreen mode Exit fullscreen mode

*   `ContentNegotiation`告诉服务器使用 Jackson 处理传入和传出请求。记住这需要包含`ktor-jackson`作为依赖项。如果你愿意，你也可以使用 [GSON](https://ktor.io/samples/feature/gson.html) 。

关于 Ktor 包含的其他特性的一长串列表，这里有一个到他们的[文档](https://ktor.io/servers/features.html)的便捷链接。

安装特性可以一直追溯到之前完成的路由。`routing`委托到其实现内部的`install`。所以你可以写:

```
install(Routing) {
  route("/people") {
    get {
      // implementation
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

只要能让你高兴，我就坚持使用`routing`。希望这能帮助你理解引擎盖下发生了什么，即使只是一点点。

### 简略提为 Kodein

因为我在这篇文章中使用了 Kodein，所以我想简单地看一下它。Kodein 是一个用 Kotlin 编写的依赖注入框架，用于 Kotlin。下面是我在示例应用程序中使用的超少量 DI:

```
val kodein = Kodein {
  bind<CqlSession>() with singleton { cassandraSession() }
  bind<PersonRepository>() with singleton { PersonRepository(instance()) }
}
val personRepository by kodein.instance<PersonRepository>() 
```

Enter fullscreen mode Exit fullscreen mode

在`Kodein`块中，创建了应用程序类的实例。在这种情况下，每个类只需要一个实例。调用`singleton`表示这一点。`instance`是 Kodein 提供的占位符，用来传递给构造函数，而不是实际的对象。

在`Kodein`块之外，检索`PersonRespository`的一个实例。

是的，我知道，在这里使用 Kodein 没有太多意义，因为我可以用一行代码来代替它…

```
val personRepository = PersonRepository(cassandraSession()) 
```

Enter fullscreen mode Exit fullscreen mode

相反，让我们把它看作一个非常简洁的例子来理解👍。

## 关闭思绪

作为一个极度偏爱 Spring 的人，我发现和 Ktor 一起工作和我以前所习惯的很不一样。我花了比平时多一点的时间来完成一些我满意的示例代码。也就是说，我认为结果看起来不错，我需要花更多的时间和 Ktor 在一起，更好地理解如何更好地利用它。目前，我相信 Ktor 还能挤出更多的利润。要了解更多关于 Ktor 的信息，我必须再次让你参考他们的[文档](https://ktor.io/)，那里有大量的示例和教程。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。