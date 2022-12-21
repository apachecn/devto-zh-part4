# 用 Kotest 测试 Spring Boot 应用程序

> 原文：<https://dev.to/kotest/testing-a-spring-boot-application-with-kotlintest-pgd>

TL；如何使用 Kotest 的 SpringListener 来测试 Spring Boot 应用程序，包括一些示例

# 你好 Kotliners！

在本文中，我们将看到如何使用 [Kotest 框架](https://github.com/Kotest/Kotest)测试 Spring Boot 应用。我们将看到一些例子，展示我们如何使用 JUnit 进行测试，以及如何轻松地迁移到 Kotest 并享受它的特性。

## 3 个简单的步骤

1.  将 Kotest 的 Spring 扩展添加到您的`build.gradle`
2.  将您的 JUnit 测试转换成 Kotest 格式(或者从头创建一个新的测试)
3.  将 SpringListener 添加到您的类中

## 添加 Kotest 弹簧扩展

在您的`build.gradle`文件中，我们将添加 Spring 扩展依赖项作为`testImplementation`。我假设您已经配置了 Kotest。如果你没有，[看一下关于如何做的文档](https://github.com/Kotest/Kotest#use)。

* * *

我喜欢让 Spring 扩展定义接近我的 Kotest 定义

build.gradle

```
dependencies {
    // ... Your dependencies ... 

    // Kotest
    testImplementation("io.kotest:kotest-runner-junit5:{version}")
    testImplementation("ioio.kotest.extensions:kotest-extensions-spring:{version}")

} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/dc0fe17a66054b144eccab845c4a90bd.png)](https://search.maven.org/artifact/io.kotest.extensions/kotest-extensions-spring)

## 将 JUnit 测试转换为 Kotest 规范

对于这一部分，我们假设我们的应用程序中已经有了一个组件:

```
@Component
class SimpleComponent {
    fun foo() = "Bar"
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，是的，它非常简单，非常虚假...但是和我在一起，我们会一步一步走下去！

我们通常为此编写一个 JUnit + Spring 集成测试，验证这个 bean 是否工作正常。

[那里](https://www.baeldung.com/spring-boot-testing)。[是](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html)。[多了](https://spring.io/guides/gs/testing-web/)。指导我们如何做的指南。让我们采取这个基本的方法:

```
@RunWith(SpringRunner::class)
@SpringBootTest
class SimpleComponentTest  {

    @Autowired
    private lateinit var simpleComponent: SimpleComponent

    @Test
    fun fooShouldReturnBar(){
        Assert.assertEquals("Bar", simpleComponent.foo())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Kotest 规范中转换测试

Kotest 中可以使用的风格有[多种，我们用`FunSpec`试试吧。](https://github.com/Kotest/Kotest/blob/master/doc/styles.md)

让我们按原样转换:

```
@RunWith(SpringRunner::class)
@SpringBootTest
class SimpleComponentTest : FunSpec() {

    @Autowired
    private lateinit var simpleComponent: SimpleComponent

    init {
        test("foo should return Bar") {
            simpleComponent.foo() shouldBe "Bar"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试崩溃了。

> 科特林。UninitializedPropertyAccessException:late init 属性 simpleComponent 尚未初始化

没事的，没事的！我们需要告诉 Kotest 这是一个春季测试！

## 将 SpringListener 添加到类中

为了修复上面的错误，我们需要将`SpringListener`添加到我们类的监听器:

```
@SpringBootTest
class SimpleComponentTest : FunSpec() {

    override fun listeners(): List<TestListener> {
        return listOf(SpringListener)
    }

    @Autowired
    private lateinit var simpleComponent: SimpleComponent

    init {
        test("foo should return Bar") {
            simpleComponent.foo() shouldBe "Bar"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们还删除了`@RunWith(SpringRunner::class)`，因为我们不再使用 JUnit 运行了。

#### *瞧*，成功了！

当然这是一个非常谦虚的例子。其他所有配置呢？概要文件、上下文配置、测试配置？

不要担心！它们的工作方式和我们在 JUnit 中习惯的一样。注释构造函数之类的。

比如搞乱简介:

```
@SpringBootTest(classes = [Components::class])
@ActiveProfiles("test-profile")
class ActiveProfileSpringTest : FunSpec() {

    override fun listeners() = listOf(SpringListener)

    @Value("\${test-foo}")
    lateinit var testFoo: String

    init {
        test("Should load active profile properties correctly") {
            testFoo shouldBe "bar"
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

[来自 Kotest 测试套件的测试](https://github.com/kotest/kotest-extensions-spring/blob/master/src/test/kotlin/io/kotest/extensions/spring/ActiveProfileSpringTest.kt)

* * *

这就是我们今天的全部内容！继续用 Kotest 测试你的 Spring 应用吧！关于这个扩展的更多信息可以在[文档](https://kotest.io/docs/extensions/spring.html)中找到

疑惑？建议？给我们留言吧！