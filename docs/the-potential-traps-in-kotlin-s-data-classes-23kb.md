# 科特林数据类中的潜在陷阱

> 原文：<https://dev.to/lankydandev/the-potential-traps-in-kotlin-s-data-classes-23kb>

这篇文章的目的不是指出 Kotlin 的数据类设计中的一些重大缺陷，并告诉你如何通过它们。其实恰恰相反。这个帖子的内容在[科特林文档](https://kotlinlang.org/docs/reference/data-classes.html#properties-declared-in-the-class-body)中有清晰的记载。我在这里只是向那些没有注意到他们的数据类是如何工作的人强调这些信息。

数据类对我们开发人员来说很方便，尤其是我们这些从 Java 过来的人。它们提供了几个生成的函数，允许您用很少的代码编写一个全功能的类。数据类提供以下生成的函数:

*   `equals`
*   `hashCode`
*   `toString`
*   `copy`
*   [componentN()函数](https://kotlinlang.org/docs/reference/multi-declarations.html)。

所有这些都是为类的主构造函数中定义的属性生成的。在这个构造函数之外定义的任何东西都会被忽略。这就是潜在的*陷阱*。但是，如果您不知道数据类是如何工作的，这只是一个陷阱。正如我前面提到的，这显然是[记载的](https://kotlinlang.org/docs/reference/data-classes.html#properties-declared-in-the-class-body)，你只需要留心它。当然，你现在也是。

如果在定义数据类时考虑不周，很可能会在应用程序中发现一些错误。`equals`和`hashCode`一般是必不可少的函数。如果它们不能像预期的那样工作，错误肯定会随之而来。

下面是一个例子:

```
data class MyClass(val a: String, val b: Int) {
  // property defined outside of primary constructor
  lateinit var c: String
}

fun main() {
  // create two equal objects
  val myClass = MyClass("abc", 0)
  val myClass2 = myClass.copy()
  // check their hashCodes are the same and that they equal each other
  println("myClass hashCode: ${myClass.hashCode()}")
  println("myClass2 hashCode: ${myClass2.hashCode()}")
  println("myClass == myClass2: ${myClass == myClass2}")
  // set the lateinit variables
  myClass.c = "im a lateinit var"
  myClass2.c = "i have a different value"
  // have their hashCodes changed?
  println("myClass hashCode after setting lateinit var: ${myClass.hashCode()}")
  println("myClass2 hashCode after setting lateinit var: ${myClass2.hashCode()}")
  // are they still equal to each other?
  println("myClass == myClass2 after setting lateinit vars: ${myClass == myClass2}")
  // sanity check to make sure I'm not being stupid
  println("sanity checking myClass.c: ${myClass.c}")
  println("sanity checking myClass2.c: ${myClass2.c}")
} 
```

Enter fullscreen mode Exit fullscreen mode

执行此示例输出:

```
myClass hashCode: 2986974
myClass2 hashCode: 2986974
myClass == myClass2: true
myClass hashCode after setting lateinit var: 2986974
myClass2 hashCode after setting lateinit var: 2986974
myClass == myClass2 after setting lateinit vars: true
sanity checking myClass.c: im a lateinit var
sanity checking myClass2.c: i have a different value 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，每个对象的`hashCode`是相同的，它们彼此相等，尽管它们的`c`属性不同。如果你试图在`Map`或`Set`中使用`MyClass`，条目相互冲突的几率会增加。也就是说，这确实取决于你想要达到的目标。也许这正是你想要发生的。在这种情况下，你更有力量。

将`c`放入`MyClass`构造函数会影响`hashCode`和`equals`的实现。然后，`c`将被包含在对`hashCode`、`equals`和其余生成函数的任何调用中。

如果你还没有这样做，我建议你快速浏览一下关于这个主题的文档。强调这些信息是这篇文章的目标。它不是一些神奇的代码。相反，对于科特林的工作方式来说，这是更基本、更根本的东西。了解数据类在这方面的工作方式对于减少应用程序中的错误数量至关重要。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！