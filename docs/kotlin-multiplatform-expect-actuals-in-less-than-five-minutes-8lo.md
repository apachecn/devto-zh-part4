# Kotlin 多平台:在不到五分钟的时间内完成预期/实际

> 原文：<https://dev.to/benwhitley/kotlin-multiplatform-expect-actuals-in-less-than-five-minutes-8lo>

如果你正在读这篇文章，你可能听说过很多关于 Kotlin 多平台的事情，但是不知道从哪里开始。该平台发展迅速，这意味着许多文档已经过时，并且/或者可能有点令人不知所措。但是没有人喜欢阅读说明，所以我写了一系列的帖子，每篇大约花 5 分钟，介绍 Kotlin 多平台的基础知识。

第 1 部分让您从零开始。这是第 2 部分，重点是`expect` / `actual`语句。Kotlin 的本机互操作是其最显著的特性之一。与其他“跨平台”生态系统不同，Kotlin 旨在拥抱它正在集成的原生平台。`expect` / `actual`语句是它提供的主要机制之一。

* * *

*五分钟？嗯，是的，如果你已经阅读了[之前的 Kotlin 多平台文章](https://medium.com/@ben_98270/two-apps-in-less-than-five-minutes-with-kotlin-multiplatform-bedf454a4a7b)并完成了它。我们从第一部分停止的开始[。](https://github.com/touchlab/KotlinMultiplatformStarter/tree/starter/expect/actuals)*

* * *

需要特定于平台的逻辑的一个领域是处理日期。我们将创建一个简单的`expect`类`KMPDate`，它接受一个日期格式字符串，并允许您创建一个表示今天日期的格式化字符串。

首先，打开`/app/src/commonMain/kotlin/sample/Sample.kt`。在`Greeting`类下面，创建一个新的`expect`类:

```
expect class KMPDate(formatString: String) {
    fun asString(): String
} 
```

`expect`关键字声明公共代码可以期望每个平台有不同的`actual`实现。在这种情况下，`expect`适用于一个阶级。然而，它也可以应用于函数、对象、注释等。查看[此处](https://kotlinlang.org/docs/reference/platform-specific-declarations.html)了解更多。

转到`/app/src/main/java/sample/SampleAndroid.kt`创建`actual` Android 实现。在`MainActivity`类下面，添加以下内容:

```
actual class KMPDate actual constructor(formatString: String) { // 1

    private val dateFormat = SimpleDateFormat(formatString) // 2

    actual fun asString(): String {
        return dateFormat.format(Date()) // 3
    }
} 
```

1.  `KMPDate`的 expect 声明声明它需要一个`formatString`，因此一个`actual constructor`实现伴随着`actual class`实现。

2.  使用`formatString`创建一个`SimpleDateFormat`对象。iOS 有一个`NSDateFormatter`类，类似于`SimpleDateFormat`，不过我们稍后会讲到。目前没有通用的 Kotlin 实现，但对于大多数事情，Android 和 iOS 都有等效的功能可用。

3.  `SimpleDateFormat`对象将`Date()`格式化为一个字符串，这将产生一个代表今天日期的`Date`对象。

接下来转到`app/src/iosMain/kotlin/sample/SampleIos.kt`，目前是空的。粘贴以下内容:

```
actual class KMPDate actual constructor(formatString: String) { // 1

    private val dateFormatter = NSDateFormatter().apply { // 2
        this.dateFormat = formatString
    }

    actual fun asString(): String {
        return formatter.stringFromDate(NSDate()) // 3 
    }
} 
```

1.  与 Android 端的`actual class`声明相同。

2.  将`formatString`传递给一个 iOS `NSDateFormatter`对象，如前所述，该对象获得了与 Android 上的`SimpleDateFormat`相同的结果。

3.  `NSDateFormatter`对象根据`NSDate()`格式化一个字符串——产生一个代表今天的`NSDate`对象。

最后，回到`/app/src/commonMain/kotlin/sample/Sample.kt`。在`Greeting`类内部，创建一个`KMPDate`对象，它采用“MMM dd”作为日期格式:

```
private val date = KMPDate("MMM dd") 
```

最后但同样重要的是，使用字符串插值将每个平台的格式化日期字符串插入到`hello()` :
的值中

```
fun hello(): String =  "Hello, Android and iOS worlds, today is ${date.asString()}!" 
```

全部完成！运行您的 Android 和 iOS 应用程序，并查看每个平台上显示的日期！

如果你来自 iOS 背景，想要更深入地了解如何使用 Kotlin 多平台来制作一个更复杂的应用程序，那么你可能想看看我写的这篇文章。

如您所见，Kotlin 多平台的一切都是从小事做起。我们最近推出了 Touchlab RefactoryKMP 提供的增量重构服务，一次只关注一个特性，不会中断您当前的开发工作流程。点击了解更多[。](https://touchlab.co/refactory/)

我们在招人！