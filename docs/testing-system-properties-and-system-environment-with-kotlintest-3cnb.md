# 使用 Kotest 测试系统属性和系统环境

> 原文：<https://dev.to/kotest/testing-system-properties-and-system-environment-with-kotlintest-3cnb>

TL；DR:用 [Kotest](https://github.com/Kotest/Kotest) 测试使用`System.getenv()`或`System.getProperty()`的具有易用功能的代码

# 你好 Kotliners！

在本文中，我们将讨论一下 Kotest 的两个特性:

*   系统环境扩展
*   系统属性扩展

它们的实现和使用非常相似，而且非常容易使用！

## 问题

在 JVM 的世界里，我们的代码很多时候依赖于系统提供给我们的东西，通常是通过传递给 JVM 的*环境变量*或*属性*。

然而，当我们想使用它时，两者都不容易测试。

*   `System.getenv()`地图[是不可变的](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/solaris/classes/java/lang/ProcessEnvironment.java#l78)。我们不能用普通的 Java 来设置它
*   手动处理`System.setProperty()`迫使我们做所有的设置和拆卸，变得非常冗长和麻烦

| [![](img/c833145b239a04c156a63f80c009e7a4.png)](https://unsplash.com/@vheath) |
| --- |
| 照片由[维多利亚·希斯](https://unsplash.com/@vheath?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上拍摄 |

## 解

Kotest 提供了一些扩展和监听器来处理这些场景。完整的文档可以在[这里](https://github.com/Kotest/Kotest/blob/master/doc/extensions.md#system-extensions)找到

### 系统环境扩展

如果被测试的代码足够小并且只使用一次，我们可以使用`withSystemEnvironment`。它有过载，但我们将使用`key`和`value`的一个。有一个超载期待一个`Pair<K,V>`和一个期待一个`Map<K,V>`

```
class EnvironmentTest : FunSpec() {
    init {
        test("Changing environment") {
            withSystemEnvironment("fooKey", "barValue") {
                System.getenv("fooKey") shouldBe "barValue"
            }
        }
    }
} 
```

### 系统属性扩展

与上面的方式相同，我们有函数`withSystemProperties`，它的工作方式非常相似:

```
class PropertyTest : FunSpec() {
    init {
        test("Changing System Properties") {
            withSystemProperties(mapOf("fooKey" to "barValue", "booKey" to "batValue")) {
                System.getProperty("fooKey") shouldBe "barValue"
                System.getProperty("booKey") shouldBe "batValue"
            }
        }
    }
} 
```

### 清理

执行后，之前的值将被设置为之前的值。在执行过程中，地图中已经存在的内容将保持不变。

### 测试监听器

也许您的代码在每个测试中都需要特定的属性。您可以为此配置`SystemPropertyTestListener`和`SystemEnvironmentTestListener`:

```
class SystemEnvironmentTestListenerTest : FunSpec() {

    override fun listeners() = listOf(
            SystemEnvironmentTestListener("fooKeyEnv", "barValueEnv"),
            SystemPropertyTestListener("fooKeyProp", "barValueProp")
        )

    init {
        test("System Property") {
            System.getProperty("fooKeyProp") shouldBe "barValueProp"
        }
        test("System Environment") {
            System.getenv("fooKeyEnv") shouldBe "barValueEnv"
        }
    }
} 
```

原始值将在测试执行后重置。

### 项目监听器

您也可以通过使用`SystemEnvironmentProjectListener`和`SystemPropertyProjectListener`来进行全局设置。关于项目配置的更多信息可以在 [Kotest 的文档](https://github.com/Kotest/Kotest/blob/master/doc/reference.md#project-config)
中找到

```
class ProjectConfig : AbstractProjectConfig() {
    override fun listeners(): List<TestListener> = listOf(SystemEnvironmentProjectListener("foo", "bar"))
} 
```

### 超驰模式

以上所有扩展都接受枚举的一个参数`OverrideMode`。

*   SetOrOverride——如果我们想要设置的键已经在 properties/environment 中，我们想要为测试覆盖它
*   SetOrIgnore——如果键已经存在，我们不会覆盖它，测试将继续
*   SetOrError(默认)——如果键已经存在，用一个异常中断测试

例如，如果您需要在全局监听器中使用一个属性，但是想要在特定的测试中覆盖它，那么这些就非常有用。

```
withSystemProperty("foo", "bar", OverrideMode.SetOrOverride) {
    // foo is overrided here
} 
```

### 分机在动作

我已经发布了两个小而简单的库来测试这些监听器和扩展:

*   从环境和属性中读取值。[测试类](https://github.com/Kerooker/SimpleProperties/blob/master/src/test/kotlin/com/kerooker/simpleproperties/internal/profile/SystemProfileLoaderTest.kt)。甚至有可能看到两个扩展[同时使用](https://github.com/Kerooker/SimpleProperties/blob/a5650444029050a80f5fdc6128a45f73f8a299b6/src/test/kotlin/com/kerooker/simpleproperties/internal/profile/SystemProfileLoaderTest.kt#L53)
*   类似的方法可以在[这个测试类](https://github.com/Kerooker/SimpleFeatureThrottler/blob/master/src/test/kotlin/com/kerooker/simplefeaturethrottler/internal/SystemPercentageFetcherTest.kt)中看到