# 更好的助手方法

> 原文：<https://dev.to/earroyoron/kotlin-for-better-helper-methods-256c>

与 Java 相比，在 Kotlin 中实现 helper 类时，您可以利用更少的冗长和更强大的惯用风格。

让我们来看看这个简单的*但是冗长的* Java 例子；方法体并不重要...

```
package io.earroyoron.helpers;

public class EarroyoronHelper {

/* A private constructor to avoid class instantiation */
 private EarroyoronHelper() {}

/* Just some helper method */
 public static String doSomething
 (final String a, final String b) {
 //.... whatever
   return a + " and then " + b;
 }
} 
```

这么简单的事情就太多了吧？

科特林版本只是展示了*什么是现代语言* :

```
package io.earroyoron.helpers

/* Just some helper method */
 fun andThen (a: String, b: String): String {
 return"$a and then $b"
 }
} 
```

我们不需要实际上不会增加价值或提高意愿或准备程度的东西。

*   当然了。那是没用的
*   类，因为我们不会实例化它。Kotlin 将函数(方法)添加到包名称空间中。您可以导入为:

`import io.earroyoron.helpers.andThen`

该方法实际上可以直接返回响应，如下所示:

`fun andThen (a: String, b: String) = "$a and then $b"`

## 带扩展功能更好

更好的版本使用类扩展:

```
package io.earroyoron.helpers

fun String.doSomeTransformation (b: String) = "$this and then $b" 
```

因此，您可以将此助手称为:

`"original string".andThen ("more letters")`

## 中缀

或者如果在函数中加入中缀(infix fun)改善为 DSL 风格:

```
infix fun String.doSomeTransformation (b: String) = "$this and then $b"

val sillyString = "original string" andThen "more letters" 
```

这是科特林最惯用的方式；你可以使用 object 实例化一个单例类，或者在一个非单例类中有一个静态块的伴生类，但是这两种方法都不是完美的。

## 功能型风格

因为您不是在创建对象，方法只是一个静态方法，只依赖于输入参数来产生结果，所以您使用的是函数样式。是加分项。