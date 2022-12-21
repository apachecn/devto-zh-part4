# 用 Kotest 测试 Koin 应用程序

> 原文：<https://dev.to/kotest/testing-koin-applications-with-kotlintest-1iip>

TL；DR——使用来自 [Kotest](https://github.com/kotest/kotest) 的`KoinListener`,编写依赖于 Kotest 规范中 [Koin](https://insert-koin.io/) 模块的测试

# Kotliners 你好！

在本文中，我们将看到如何在来自 [Kotest](https://github.com/kotest/kotest) 框架的`Spec`中测试使用 [Koin DI 框架](https://insert-koin.io/)的应用程序。我们将从 Koin 的文档中获取 JUnit 示例，并用 Kotest 增强它。

## 3 个简单的步骤

1.  将 Kotest 的 Koin 扩展添加到您的 build.gradle 中
2.  将您的 JUnit 测试转换成 Kotest 规范(或者从头创建一个)
3.  将 KoinListener 添加到您的类中

## 将 Koin 扩展添加到您的 build.gradle 中

在您的`build.gradle`文件中，我们将添加 Koin 扩展依赖项作为`testImplementation`。我假设您已经配置了 Kotest。如果你没有，[看一下关于如何做的文档](https://github.com/kotest/kotest#use)。

* * *

我喜欢让 Koin 扩展定义接近我的 Kotest 定义

build.gradle

```
dependencies {
    // ... Your dependencies ... 

    // Kotest
    testImplementation("io.kotest:kotest-runner-junit5:{version}")
    testImplementation("io.kotest:kotest-extensions-koin:{version}")

} 
```

## 将 JUnit 测试转换为 Kotest 规范

对于这个例子，我们将使用 Koin 文档 :
中的 [JUnit 例子](https://insert-koin.io/docs/2.0/quick-references/testing-your-components/)

```
class MyTest : KoinTest {

    // Lazy inject property
    val componentA : ComponentA by inject()

    // use it in your tests :)
    @Test
    fun `make a test with Koin`() {
        startKoin { modules(appModule) }

        // use componentA here!
    }
} 
```

### 转换测试

Kotest 中可以使用的[风格](https://github.com/Kotest/Kotest/blob/master/doc/styles.md)很多，我们试试`FunSpec`

```
class MyTest : FunSpec(), KoinTest {

    // Lazy inject property
    val componentA : ComponentA by inject()

    init {
        test("Make a test with Koin") {
            startKoin { modules(appModule) }

            // use componentA here!
        }
    }

} 
```

这个已经可以用了！

这个例子的工作方式与 JUnit 代码相同。如果我们向类中添加更多的测试，我们将不得不清除 Koin 的上下文。在 JUnit 中，我们创建了一个`@After`方法。让我们看看如何在 Kotest 中做到这一点

* * *

## 将 KoinListener 添加到类中

为了解决这个问题，我们可以覆盖`afterTest`函数，它的工作方式与`@After`类似，或者我们可以使用`KoinListener`为我们自动完成这项工作！让我们试试这种方法:

```
class MyTest : FunSpec(), KoinTest {

    override fun listeners() = listOf(KoinListener(appModule))

    // Lazy inject property
    val componentA : ComponentA by inject()

    init {
        test("Make a test with Koin") {

            // use componentA here!
        }
    }

} 
```

用`KoinListener`我们将让 Kotest 处理 Koin 的两次启动(注意我们在测试中去掉了那条线！)和 Koin 的停。

### 就是这样！

通过这个简单的设置，您将能够用 Kotest 测试您的 Koin 应用程序，并使用它们所有可用的特性！

如果你还有任何疑问，请查看`KoinListener`上的[完整文档](https://github.com/Kotest/Kotest/blob/master/doc/extensions.md#koin)