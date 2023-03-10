# 科特林和杰克逊的无痛 JSON

> 原文：<https://dev.to/sirech/painless-json-with-kotlin-and-jackson-fk>

似乎许多提供 REST API 的后端最终都变成了将 JSON 从一个地方移动到另一个地方的代理。如果你想让这些后端尽可能简单，那就更是如此了([微服务](https://microservices.io/patterns/microservices.html)有人知道吗？).因此，拥有正确的工具来解析和生成 JSON 可以在保持代码整洁和紧凑方面产生很大的影响。为此我想谈谈我使用[科特林](https://kotlinlang.org/)和[杰克森](https://github.com/FasterXML/jackson)的经历。

我记得在过去，用 Java 处理 JSON 非常痛苦，因为必须编写大量代码来映射对象。这就是最初让我使用 Ruby 的原因。事情发生了很大的变化(变好了！)从此。如今，使用*科特林*和*杰克森*你可以用最小的努力处理 JSON。杰克逊是一个强大的图书馆，但你很容易迷路。我有一堆例子展示了如何解析不同的类，还有一些代码将它集成到您的工作流中。在这种情况下，我将使用[跳靴](https://spring.io/)。

### 序列化/反序列化

我们将使用[数据类](https://kotlinlang.org/docs/reference/data-classes.html)来表示将从 JSON 转换成的实体。它们相当于使用 *Lombok* 中的 [@Value](https://projectlombok.org/features/Value) 注释，并拥有该语言的一流支持。它们是不可改变的(耶！)并有现成的便利方法，如`equals`和`toString`。

您可以使用一个[对象映射器](https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/ObjectMapper.html)来进行解析，尽管您可以配置 *SpringBoot* 来自动完成这项工作，我将在后面展示。我有一个带有两个字段的`User`实体，我想将它们转换成 JSON，然后再转换回来。

```
data class User(val id: String, val age: Int)

fun User.toJson(): String = ObjectMapper().writeValueAsString(this)
fun String.toUser(): User = ObjectMapper().readValue(this) 
```

Enter fullscreen mode Exit fullscreen mode

对于简单的情况，只要定义数据类就够了，只要你有[右模块](https://github.com/FasterXML/jackson-module-kotlin)。不过，您可以在此基础上做一些额外的配置。其中许多可以用注释来控制，这使得代码更加紧凑。但是，滥用它们会使您的代码变得不可维护。

#### 为空性

如果某些字段是可选的，则提供默认值。

```
data class User(
  val id: String = ""
) 
```

Enter fullscreen mode Exit fullscreen mode

你也可以允许他们是`null`。

```
data class User(
  val id: String?
) 
```

Enter fullscreen mode Exit fullscreen mode

不做任何事情会导致解析失败并出现异常，我认为这是一件好事。

#### 走样

如果您从使用不同属性名称的不同来源解析您的对象，但是仍然希望保持一个*规范的*表示，`@JsonAlias`是您的朋友。

```
data class User(
  @JsonAlias("userId")
  val id: String
) 
```

Enter fullscreen mode Exit fullscreen mode

这将正确解析类似于
的内容

```
{  "userId":  "123"  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 忽略属性

也许您正在解析一个包含大量您不需要的字段的对象。如果你不打算在你的代码中使用它，你真的应该避免添加它们，因为那会使你更难理解什么是需要的，什么是不需要的。`@JsonIgnoreProperties`可用于此。

```
@JsonIgnoreProperties(ignoreUnknown = true)
data class User(val id: String) 
```

Enter fullscreen mode Exit fullscreen mode

#### 不同的表象

如果您的后端充当代理，您将从某个地方读取数据并将其传递给客户端。在这种情况下，您可能希望跳过序列化中的一些字段，以便准确地为客户端提供它需要的字段。您可以通过自定义 access 属性来实现这一点。

```
data class User(
  val id: String = "",
  @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
  val age: Int
) 
```

Enter fullscreen mode Exit fullscreen mode

这个对象的序列化将不包含`age`，但是它在我们的代码中是可用的。然而，这种方法不能很好地扩展。如果您发现同一个实体有两种不同的表示，并且要添加大量的注释来使用一个类，那么最好将它分成两个不同的类，并为一个类提供一个方法。

这强调了重要的一点。您不需要对所有事情都使用注释和隐式转换。在某些地方，拥有专用的转换器更具可读性，如果你想在这个过程中加入一些逻辑，就更是如此。

#### 如果你想要更多…

这篇文章仅仅展示了可能做到的事情的一小部分。您可以控制序列化/反序列化过程的每个方面。如果你想了解其他选择，可以看看这篇文章。

* * *

### 远离无类型字符串

在 JSON 中，您倾向于使用字符串来表示许多实体。任何 id 类型，比如用户 id，或者语言代码。我更喜欢在我的代码中将它们映射到专用类。我见过许多错误，其中使用了错误的实体，而编译器可以直接阻止这种错误。以一个`UserId`为例，我喜欢这样建模:

*   它应该是一个不可变的数据类
*   它不应该强制改变 JSON 的结构(即没有嵌套)
*   序列化/反序列化应该开箱即用

```
data class UserId(private val value: String) {
    companion object {
        @JvmStatic
        @JsonCreator
        fun create(value: String) = UserId(value.toLowerCase())
    }

    @JsonValue
    override fun toString() = value
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用一个数据类，我们得到了一个代表这个实体的不可变对象。我们能做的相对较少。事实上，我们甚至不想访问内部字段。我们将直接比较实例，如果我们需要获得一个字符串表示，我们将通过`toString`方法来完成。

序列化是通过`@JsonValue`注释进行的，在这里我们直接使用值。如果我们修改我们之前一直在使用的`User`类，它会是这样的。

```
data class User(val id: UserId, val age: Int) 
```

Enter fullscreen mode Exit fullscreen mode

该类序列化为这个 JSON

```
{  "id":  "123",  "age":  20  } 
```

Enter fullscreen mode Exit fullscreen mode

这种表示符合大多数客户端(尤其是前端)对这种结构的预期，而不会牺牲后端的任何安全性。

反序列化会自动发生。然而，我喜欢定义一个静态构造函数(使用`@JvmStatic`和`@JsonCreator`注释),这样我就可以在生成实例之前处理输入。这有助于确保我们的模型处于一致的状态。

从 Kotlin 1.3 开始，引入了一个名为[内联类](https://kotlinlang.org/docs/reference/inline-classes.html)的新概念，这可能更适合这个用例。从 16/06/19 开始，Jackson 在嵌套对象中很难正确地反序列化它，所以到目前为止我还不能用它替换我的数据类。Github 中有一个[未解决的问题需要关注。](https://github.com/FasterXML/jackson-module-kotlin/issues/199)

* * *

### 跳靴集成

这里我们得到了拼图的最后一块。我们可以手动使用一个`ObjectMapper`并显式地转换事物。如果它自己发生，事情就简单多了。好消息是，除了添加 [jackson-module-kotlin](https://github.com/FasterXML/jackson-module-kotlin) 作为依赖项之外，这里没什么可做的

```
implementation("com.fasterxml.jackson.module:jackson-module-kotlin:${jacksonVersion}") 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的都是最新版本，那就足够了。如果 spring 魔术不是自己发生的(spring 做了很多我不太懂的魔术)，你可以手动做。您可以使用一个`@Configuration`以便您的控制器可以自动映射到 JSON 和从 JSON 映射。

```
@Configuration
class JacksonConfiguration {
    @Bean
    fun mappingJackson2HttpMessageConverter(): MappingJackson2HttpMessageConverter {
        return MappingJackson2HttpMessageConverter().apply {
            this.objectMapper = ObjectMapper().apply {
                registerModule(KotlinModule())
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您向另一个服务发出 REST 请求，您可以构建一个自定义的`RestTemplate`来做同样的事情:

```
open class DefaultRestTemplate(baseUrl: String) :
        RestTemplate(HttpComponentsClientHttpRequestFactory(
                HttpClientBuilder.create().useSystemProperties().build())) {
    init {
        uriTemplateHandler = DefaultUriBuilderFactory(baseUrl)
        messageConverters = jacksonConverter(messageConverters)
    }

    private fun jacksonConverter(converters: MutableList<HttpMessageConverter<*>>): List<HttpMessageConverter<*>> {
        val jsonConverter = MappingJackson2HttpMessageConverter()
        jsonConverter.objectMapper = jacksonObjectMapper().apply {
            registerModule(KotlinModule())
        }
        converters.add(jsonConverter)
        return converters
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，所有这些都应该通过将库添加到类路径中来实现。如果由于某种原因不工作，使用这个作为后备。此外，该模板可以扩展为使用基本 URL、接收环境变量(例如包括 keystore)或自动向请求添加某些头。

#### 路径变量不是 JSON

现在我们已经深入到自动化 JSON 映射中，我开始变得雄心勃勃。如上所述，我们不再使用普通字符串，而是使用适当的域类。假设你有一条类似`GET /users/:userId`的路线。控制器应该是这样的:

```
@RestController
@RequestMapping("/users", produces = [MediaType.APPLICATION_JSON_VALUE])
class HelloController {
    @GetMapping("{userId}")
    fun user(@PathVariable("userId") userId: UserId): ResponseEntity<User>
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你发送一个请求到这个路由，`userId`将被自动解析，但是我们的自定义`create`方法不会被调用，因为这是一个 URL，而不是 JSON。我们走到这一步并不是为了再次手动解析字符串。让我们通过使用自定义转换器来解决这个问题。

```
@Configuration
class ConverterConfiguration : WebMvcConfigurer {
    override fun addFormatters(registry: FormatterRegistry) {
        registry.addConverter(userId())
    }

    private fun userId(): Converter<String, UserId> {
        return Converter<String, UserId> { source -> UserId.create(source) }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。现在我们可以确定，在请求流的任何一点上，这些讨厌的字符串都不会在我们的应用程序中流动。

### 总结

Jackson 是一个非常强大的库，老实说，你可以用所有的注释来过度使用它。但是，如果您明智地使用它们，使用 JSON 会变得非常容易，同时在这个过程中保持很好的类型安全性。对于测试来说，这与使用 WireMock 的[记录的 API 配合得很好。](https://hceris.com/recording-apis-with-wiremock/)

*编辑 25/12/2019:* 语法复习