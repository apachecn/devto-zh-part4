# #教学大纲第 1 部分-类和对象以及它们之间的一切

> 原文：<https://dev.to/msfjarvis/teachingkotlin-part-1-classes-and-objects-and-everything-in-between-5bn0>

Kotlin 中的类在实现上很好地模仿了它们的 Java 对应物，有一些重要的变化，我将在这里列出来。

让我们用 Kotlin 和 Java 声明两个相同的类作为起点。我们将对它们进行修改，以展示不同的模式在两种语言中是如何实现的。

Java:

```
class Person {
  private final String name;

  public Person(String name) {
    this.name = name;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

锅碗瓢盆

```
class Person(val name: String) 
```

Enter fullscreen mode Exit fullscreen mode

使用 Kotlin 的好处立即开始显现！但是让我们以系统的方式回顾一下，并从各个方面分析一下是什么让科特林如此伟大。

## 构造函数和参数

Kotlin 使用非常紧凑的语法来描述主构造函数。通过一些关于默认值的巧妙技巧，我们可以从一个构造函数中创建许多构造函数！

注意参数名中的`val`。这是一种简洁的语法，用于声明变量并从构造函数本身初始化它们。像任何其他属性一样，它们可以是可变的(`var`)或不可变的(`val`)。如果您删除了我们的`Person`构造函数中的`val`，您将没有一个`name`变量可用于它的实例，也就是说，`Person("Person 1").name`将不会被解析。

主构造函数不能有任何代码，所以 Kotlin 提供了一种叫做“初始化程序块”的东西，允许你从构造函数中运行初始化代码。试着在[科特林游乐场](https://play.kotlinlang.org/)
运行下面的代码

```
class Person(val name: String) {
  init {
    println("Invoking constructor!")
  }
}

val _ = Person("Matt") 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们为我们的类添加一个可选的年龄参数，默认值为 18。为了方便查看不同的构造函数如何影响值，我们还包含了一个用于一些分类打印调试的`toString`方法的实现。

Java:

```
class Person {

  private final String name;
  private int age = 18;

  public Person(String name) {
    this.name = name;
  }

  public Person(String name, int age) {
    this(name);
    this.age = age;
  }

  @Override
  public String toString() {
    return "Name=" + name + ",age=" + Integer.toString(age);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

锅碗瓢盆

```
class Person(val name: String, val age: Int = 18) {
  override fun toString() : String {
    // I'll go over string templates in a future post, hold me to it :)
    return "Name=$name,age=$age"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多新东西！让我们把它们分解开来。

Kotlin 有一个名为“默认参数”的特性，它允许你为参数指定默认值，从而在创建类的实例时使它们成为可选的。

让我们带着这些在 [repl.it](https://repl.it) 上转一圈吧！

[https://repl.it/@msfjarvis/ButteryYellowgreenTraining?lite=true](https://repl.it/@msfjarvis/ButteryYellowgreenTraining?lite=true)[https://repl.it/@msfjarvis/DarkcyanDisfiguredDatalogs?lite=true](https://repl.it/@msfjarvis/DarkcyanDisfiguredDatalogs?lite=true)

两者都工作得非常好，但是你知道你更喜欢写哪一个；)

这里需要注意的一点是，如果您正在编写一个库或任何需要与 Java 互操作的代码，带有默认值的构造函数不能直接与 Java 一起工作。使用 Kotlin `@JvmOverloads`注释来处理这个问题。

```
class Person @JvmOverloads constructor(val name: String, val age: Int = 18) {
  override fun toString() : String {
    return "Name=$name,age=$age"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做将生成类似于我们之前用 Java 编写的构造函数，允许 Kotlin 和 Java 调用者工作。

## 类的终局性

在 Kotlin 中，默认情况下所有的类都是 final，不能被继承，而 Java 默认为可扩展类。`open`关键字将 Kotlin 类标记为可扩展，而`final`关键字在 Java 上做的正好相反。

Java:

```
public class Man extends Person { /* Class body */ } // Valid in Java 
```

Enter fullscreen mode Exit fullscreen mode

锅碗瓢盆

```
class Man(val firstName: String) : Person(firstName) // Errors! 
```

Enter fullscreen mode Exit fullscreen mode

在科特林 REPL 试用

```
>>> class Person @JvmOverloads constructor(val name: String, val age: Int = 18) {
...   override fun toString() : String {
...     return "Name=$name,age=$age"
...   }
... }
>>> class Man(val firstName: String) : Person(firstName)
error: this type is final, so it cannot be inherited from
class Man(val firstName: String) : Person(firstName)
                                   ^ 
```

Enter fullscreen mode Exit fullscreen mode

有道理，因为这是科特林的默认值。让我们将关键字`open`添加到我们对`Person`的定义中，然后再试一次。

```
>>> open class Person @JvmOverloads constructor(val name: String, val age: Int = 18) {
...   override fun toString() : String {
...     return "Name=$name,age=$age"
...   }
... }
>>> class Man(val firstName: String) : Person(firstName)
>>> println(Man("Henry"))
Name=Henry,age=18 
```

Enter fullscreen mode Exit fullscreen mode

一切都如我们所愿。这是一个令人困惑的行为改变，很多人都不希望这样，所以 Kotlin 提供了一个编译器插件，将所有类默认标记为`open`。查看 [`kotlin-allopen`](https://kotlinlang.org/docs/reference/compiler-plugins.html#all-open-compiler-plugin) 页面，了解更多关于如何根据需要配置插件的信息。

## 静态实用程序类

每个人都知道，直到你有了一个`StringUtils`类，你才有一个真正的项目。通常它是一个有一堆静态方法的`public static final`类。虽然 Kotlin 有一个更好的选项[扩展函数和属性](https://kotlinlang.org/docs/tutorials/kotlin-for-py/extension-functionsproperties.html)，但出于比较的目的，我们将坚持使用旧的 Java 方式。

这里有一个小函数，我用来将 Android 的 URI 路径转换成人类可读的版本。

Java:

```
public static final class StringUtils {
  public static String normalizePath(final String str) {
    return str.replace("/document/primary:", "/sdcard/");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

锅碗瓢盆

```
object StringUtils {
  // I'll cover this declaration style too. It's just the first post!
  fun normalizePath(str: String): String = str.replace("/document/primary:", "/sdcard/")
} 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 的一个循环模式是简洁的代码，正如您在本例中看到的。

这一个就这么多了！请在评论中告诉我你希望下周的帖子是关于什么的，或者如果你觉得我错过了什么，我一定会努力让它发生:)