# 让我们也使用 Kotlin 范围函数来运行

> 原文：<https://dev.to/frosnerd/let-s-also-apply-run-with-kotlin-scope-functions-1ci4>

# 作用域函数

在 Kotlin 中，作用域函数允许在对象的上下文中执行函数，即代码块。然后可以在该临时范围内访问该对象，而无需使用名称。尽管无论您使用范围函数做什么，都可以不使用它们，但是它们使您能够以不同的方式构建代码。使用它们可以增加可读性，使代码更加简洁。

Kotlin 标准库提供了四种不同类型的作用域函数，可以根据它们引用上下文对象的方式和它们返回的值进行分类。范围函数或者将上下文对象作为*函数参数*或者作为*函数接收器*来引用。范围函数的返回值要么是*函数结果*，要么是*上下文对象*。

可用的功能有`let`、`also`、`apply`、`run`和`with`。下表根据访问上下文对象的方式和上述返回类型总结了每个函数的特征:

|  | 作为函数参数的上下文对象 | 作为函数接收器的上下文对象 |
| --- | --- | --- |
| 返回:函数结果 | `let` | `run`，`with` |
| 返回:上下文对象 | `also` | `apply` |

`run`和`with`的区别只在于称呼的方式。当所有其他范围函数都作为扩展函数实现时，`with`是一个常规函数。

既然我已经提到了像函数接收器和扩展函数这样的概念，在我们进入作用域函数的详细描述之前，简单解释一下它们是有意义的。如果你已经熟悉 Kotlin 中的函数接收器和扩展函数，你可以跳过下一节。

# 函数自变量，扩展函数，接收者

Kotlin 允许将函数视为值。这意味着您可以将*函数作为参数*传递给其他函数。使用`::`操作符，你可以将一个方法转换成一个函数值。为了增加可读性，最后一个函数参数可以放在参数列表之外。

下面的例子通过定义一个高阶函数`combine`来说明如何做到这一点，该函数带有一个函数参数`f`。我们用来自`Int`类的`plus`方法调用它，并在参数列表内外使用匿名函数文字:

```
// Apply function argument f to integers a and b
fun combine(a: Int, b: Int, f: (Int, Int) -> Int): Int = f(a, b)

// Using the plus method as a function value
combine(1, 2, Int::plus)

// Passing a function literal
combine(1, 2, { a, b ->
    val x = a + b
    x + 100
})

// Passing it outside of the argument list
combine(1, 2) { a, b ->
    val x = a + b
    x + 100
} 
```

Enter fullscreen mode Exit fullscreen mode

*扩展函数*是一种扩展现有类或接口的方法，你不需要控制这些类或接口。在一个类上定义一个扩展函数可以让你在这个类的实例上调用这个方法，就像它是原始类定义的一部分一样。

以下示例在`Int`上定义了一个扩展函数来返回绝对值:

```
fun Int.abs() = if (this < 0) -this else this

(-5).abs() // 5 
```

Enter fullscreen mode Exit fullscreen mode

带有*接收者*的函数文字类似于扩展函数，因为接收者对象可以通过`this`在函数中访问。下面的代码片段定义了前面的扩展函数，但是这次是一个带有 receiver 的函数文本:

```
val abs: Int.() -> Int = { if (this < 0) -this else this }

(-5).abs() // 5 
```

Enter fullscreen mode Exit fullscreen mode

带有接收器的函数文字的一个常见用例是[类型安全构造器](https://kotlinlang.org/docs/reference/type-safe-builders.html)。现在我们已经介绍了基础知识，让我们分别来看看五个范围函数。

# 让、还、应用、运行、用

## 让

`let`范围函数使上下文对象作为函数参数可用，并返回函数结果。一个典型的用例是对值应用空安全转换。

```
val x: Int? = null

// null-safe transformation without let
val y1 = if (x != null) x + 1 else null
val y2 = if (y1 != null) y1 / 2 else null

// null-safe transformation with let
val z1 = x?.let { it + 1 }
val z2 = z1?.let { it / 2 } 
```

Enter fullscreen mode Exit fullscreen mode

## 也

`apply`范围函数使上下文对象作为函数参数可用，并返回上下文对象。当你在一个函数中计算一个返回值，然后想在返回之前对它应用一些副作用时，可以使用这个函数。

```
// assign, print, return
fun computeNormal(): String {
    val result = "result"
    println(result)
    return result
}

// return and also print
fun computeAlso(): String =
    "result".also(::println) 
```

Enter fullscreen mode Exit fullscreen mode

## 适用

`apply` scope 函数使上下文对象作为接收者可用，并返回上下文对象。这使得它对于可变对象(如 Java Beans)的“特别构建者”非常有用。

```
// Java Bean representing a person
public class PersonBean {
    private String firstName;
    private String lastName;
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    public String getFirstName() {
        return firstName;
    }
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    public String getLastName() {
        return lastName;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Initialization the traditional way
val p1 = PersonBean()
p1.firstName = "Frank"
p1.lastName = "Rosner"

// Initialization using apply
val p2 = PersonBean().apply {
    firstName = "Frank"
    lastName = "Rosner"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 跑与同

`run` scope 函数使上下文对象作为接收者可用，并返回函数结果。它可以在有或没有接收器的情况下使用。当在没有接收方的情况下使用它时，可以使用局部作用域的变量来计算表达式。通过使用接收器，可以在任何对象上调用`run`，例如连接对象。

```
// compute result as block result
val result = run {
    val x = 5
    val y = x + 3
    y - 4
}

// compute result with receiver
val result2 = "text".run {
    val tail = substring(1)
    tail.toUpperCase()
} 
```

Enter fullscreen mode Exit fullscreen mode

`with`函数的工作方式与`run`完全相同，但它是作为常规函数而不是扩展函数来实现的。

```
val result3 = with("text") {
    val tail = substring(1)
    tail.toUpperCase()
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

在这篇文章中，我们学习了范围函数`let`、`also`、`apply`、`run`和`with`。它们引用上下文对象和返回值的方式不同。结合函数参数、扩展函数和接收器的概念，作用域函数是生成可读性更强的代码的有用工具。

你对范围函数有什么看法？你曾经在你的项目中使用过它们吗？你还记得什么时候用哪个吗？请在评论中告诉我你的想法！

# 参考文献

*   [范围功能文件](https://kotlinlang.org/docs/reference/scope-functions.html)
*   [接收器文件的功能](https://kotlinlang.org/docs/reference/lambdas.html?_ga=2.65237611.45512856.1561714615-1060180565.1544467616#higher-order-functions-and-lambdas)
*   [扩展功能文档](https://kotlinlang.org/docs/reference/extensions.html)