# URM 和科特林的 TDD

> 原文：<https://dev.to/sierisimo/tdd-with-urm-and-kotlin-1dj7>

在我之前的一篇文章中，我写了关于 URM 和它是如何运作的。在另一篇[中，我提到了测试用例](https://dev.to/sierisimo/unit-testing-with-junit-3mcd)以及如何为一个简单的功能保留想法。现在是总结 TDD 的时候了。

首先…

## TDD 是什么？

**TDD** 是*测试驱动开发*的缩写。它是一种基于首先为给定软件编写测试来构建软件的技术。

值得一提的是，这种技术并不能解决所有的问题，但是可以让你在问题变得太混乱之前发现问题。此外，这是一种不同的编写代码的方式，这意味着乍一看它可能不会完全有意义，或者不会让你太着迷。我认为一旦你习惯了这个过程，就很容易去适应它。

一些朋友抱怨交付时间和 TDD 花费的时间比抛出大量代码更长的问题，关于这一点最大的争论是，如果将来有什么东西坏了，而你没有对它进行测试，你会花同样的时间来修复它，就好像你从一开始就花时间去做 TDD 一样。我仍然认为值得努力去了解为什么它如此受欢迎，并且至少尝试一次——这里的一次是指一段时间大于一天。

TDD 只有三条规则可以遵循:

1.  除非是为了通过失败的单元测试，否则不允许编写任何产品代码
2.  不允许编写任何超过足以导致失败的单元测试；编译失败仍然是失败
3.  除了足以通过一个失败的单元测试之外，您不允许编写更多的产品代码

有了这三条规则，你可以很容易地遵循 TDD。整个过程不仅仅是遵循这些规则，还包括改变心态。为了让规则发挥作用，你需要考虑三种不同的角色:

1.  外部开发者
2.  QA/测试人员/对软件设计有部分了解的人
3.  实际的开发者

这意味着你将在一个循环中遵循规则，同时改变当前的心态。你会习惯的整个过程如下:

1.  我想如何在代码之外使用这些代码？我希望有一种方法来传递回调，还是从调用中获得一个值？在实际调用代码之前需要哪些依赖关系？
2.  让我们编写代码来验证响应是否有效，以及值是否按预期返回/发送
3.  让我们来编写实际的代码
4.  重构一些名称
5.  如果有什么需要改变，从第 1 步开始重复

进入 TDD 的整个过程对每个人来说都是不同的，每天当你面临紧张的截止日期时，也很难适应。尽管如此，我坚持认为:这值得一试。

## 项目和设置

附带的项目是一个工作 URM 的简单实现。目标是编写一个能够执行 URM 符号的项目:

```
X: []

1: Z(2)
2: Z(5)
3: I(2)
4: J(2,5,7) 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我将在 JVM 中使用 ***、科特林*** 以及 ***、JUnit 5*** 。我也将使用 ***Gradle*** 作为任务运行器和构建工具。最终项目可以在[这个 Github 项目中找到](https://github.com/sierisimo/publications/tree/master/TDDWithURMAndKotlin/kurm)

## 零点功能

我们要构建的第一件事是让 URM 机器工作的操作，这是最简单的部分，因为我们不必担心*寄存器*或解析过程的内部。

`Z(x)`函数将是我们要写的第一个特性。该函数将接受一个表示寄存器位置数字，并在其上放置一个零。我们将构建这个函数，以便在我们的代码中调用。应该很容易吧？嗯……我有一些问题:

*   函数应该是每个寄存器的一部分吗，比如:`register.zero()`？
    *   我不喜欢这个想法，寄存器应该只知道如何存储一个值，其他什么都不知道
    *   这将涉及创建一个对象来表示每个寄存器，我们不需要这种复杂性
*   函数应该是更大的对象的一部分吗，比如:`URMinstance.zero()`？
    *   这是对函数所在位置的一点改进
    *   这种方法有一个问题，它使函数只能与本地寄存器组(也称为注册表)一起工作，并且对实现有一点了解
    *   我们依赖于`URMinstance`的存在来测试函数(没有我想的那么糟糕)
*   它实际上应该采用哪些参数？
    *   该函数应该获取寄存器的位置，并且应该知道寄存器组，以便对其进行修改
    *   该函数应该不知道注册表的实现方式，但仍然知道如何在其上设置一个值

有了这些问题和答案，我们决定用一种简单的方式调用我们的函数:

```
zero(registry, position) 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，函数不依赖于某个注册表，不依赖于 URM 的内部实现，我们可以在不涉及太多事情的情况下测试它。

### 我们的初始测试

我们的第一个测试(在伪代码中)将接近于:

```
position = 5

zero(registry, position)

checkEquals(0, registry.getPositionValue(position)) 
```

Enter fullscreen mode Exit fullscreen mode

这非常接近我们的第一个 kotlin 测试:

```
import org.junit.jupiter.api.Test

internal class OperationsTest {
    @DisplayName("Zero Function sets to 0 register at position")
    @Test
    fun zeroFunctionSetsTo0TheRegisterAtPosition() {
        val position = 5
        //val registry: ?

        zero(registry, position)

        val expected = 0
        val actual = registry[position]

        assertEquals(expected, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 gradle，我们应该能够运行测试:

```
sierisimo@computer:$ gradle check
…
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (14, 9): Unresolved reference: zero
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (14, 14): Unresolved reference: registry
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (17, 22): Unresolved reference: registry
FAILURE: Build failed with an exception.
* What went wrong:
Execution failed for task ':compileTestKotlin'.
> Compilation error. See log for more details
* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.
* Get more help at https://help.gradle.org
BUILD FAILED in 9s
2 actionable tasks: 1 executed, 1 up-to-date
Compilation error. See log for more details 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，我们有一个编译错误…

> 咄！这是显而易见的，因为我们没有创建那个函数，它是一个非常简单的函数…

嘿！等一下…在说这些之前，你应该记住 TDD 的规则:

1.  除非是为了通过失败的单元测试，否则不允许编写任何产品代码
2.  不允许编写任何超过足以导致失败的单元测试；并且**编译失败是失败**
3.  除了足以通过一个失败的单元测试之外，您不允许编写更多的产品代码

这意味着我不能在编写测试之前编写`zero`函数。但现在我们有一个失败的单元测试，这意味着我们被允许编写生产代码！

注意:*生产代码*可以是一个术语，人们理解为将被立即放在服务器/应用上以响应用户交互的代码。这不是 100%正确的，产品代码基本上是最终项目中的代码，满足项目的部分功能。

### 编写代码并修复

为了写代码，让我们听听错误是怎么说的:

```
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (14, 9): Unresolved reference: zero
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (14, 14): Unresolved reference: registry
e: /Dev/sierisimo/TDD_URM_Kotlin/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (17, 22): Unresolved reference: registry 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们没有`zero`功能！让我们在包中创建一个:`net.sierisimo.kurm.operations.Operations.kt`

```
fun zero(){

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们再次调用`gradle check`，我们得到:

```
e: /Users/sierisimo/Documents/Dev/kurm/src/test/kotlin/net/sierisimo/kurm/operations/OperationsTest.kt: (15, 14): Too many arguments for public fun zero(): Unit defined in net.sierisimo.kurm.operations 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们忘记了添加函数的参数。所以我们的函数将会像这样结束:

```
fun zero(registry: ?, position: Int){

} 
```

Enter fullscreen mode Exit fullscreen mode

我在函数中遗漏了一些东西…什么是`registry`的类型？是数组吗？我不这么认为，我们的程序是“ ***无限制**注册机*，而且数组有大小限制。是列表吗？有可能，只要列表被初始化或者有办法增长到一定的大小来容纳空槽。真正的问题是:注册中心有什么功能？它应该能够检索给定位置的寄存器的值，并设置给定位置的寄存器的值。那么列表可以工作，但是仍然有空插槽的问题。

我们决定，在这个精确的时刻，我们不关心注册中心如何工作，因为我们不关心它如何工作——只要它工作——我们可以为此创建一个定义:

```
interface Registry {
    fun getValueAtPosition(position: Int): Int?

    fun setValueAtPosition(position: Int, value: Int)
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以在以后委派实现的责任。现在我们的第一个零点函数是这样的:

```
fun zero(registry: Registry, position: Int) {

} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们不关心注册表本身的细节，我们可以在测试中创建一个实现:

```
internal class OperationsTest {
    val registry = object : Registry {
        val positionValueMap = mutableMapOf<Int, Int>()

        override fun getValueAtPosition(position: Int): Int? = positionValueMap[position]

        override fun setValueAtPosition(position: Int, value: Int) {
            positionValueMap[position] = value
        }
    }

    @DisplayName("Zero Function sets to 0 register at position")
    @Test
    fun zeroFunctionSetsTo0TheRegisterAtPosition() {
        val position = 5

        zero(registry, position)

        val expected = 0
        val actual = registry.getValueAtPosition(position)

        assertEquals(expected, actual)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以是最终的实现，但是编写这个最终的实现会违反 TDD 的规则。现在，我们继续使用这个本地实现进行测试。

如果我们现在运行`gradle check`，它会显示:

```
> Task :test

net.sierisimo.kurm.operations.OperationsTest > zero function should set a 0 in position X() FAILED
    org.opentest4j.AssertionFailedError at OperationsTest.kt:27

1 test completed, 1 failed

> Task :test FAILED

FAILURE: Build failed with an exception. 
```

Enter fullscreen mode Exit fullscreen mode

这意味着项目成功编译，但是我们的测试没有通过。我们知道目前我们的函数中没有主体，让我们来解决这个问题:

```
fun zero(registry: Registry, position: Int) {
    registry.setValueAtPosition(position, 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后`gradle check`会显示:

```
> Task :test

net.sierisimo.kurm.operations.OperationsTest > zeroFunctionSetsTo0TheRegisterAtPosition() PASSED

BUILD SUCCESSFUL in 1s 
```

Enter fullscreen mode Exit fullscreen mode

### 糟糕的情况

我们的测试通过了。现在，我们应该编写另一个测试来验证一切都按预期工作。例如，我们知道 URM 处理寄存器中的位置，但是负位置会发生什么呢？它们不是有效的位置…让我们为它写一个测试:

```
@Test
@DisplayName("Zero Function throws exception with negative position")
fun zeroFunctionThrowsExceptionWithNegativePosition() {
    val position = -1

    assertThrows<IllegalArgumentException> { zero(registry, position) }

    assertNull(registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，我们检查我们的函数对负位置不起作用。但是我们也在测试中声明，我们期望函数*抛出*一个`IllegalArgumentException`。如果我们运行`gradle check`，我们将看到这个测试没有通过，但是前一个通过了。

我们修复了添加 Kotlin 合同的`zero`函数:

```
fun zero(registry: Registry, position: Int) {
    require(position > 0) { "Position must be positive number" }

    registry.setValueAtPosition(position, 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行检查，我们的两个测试都将通过，并且我们已经确保，即使在我们修改了函数内部的工作方式之后，它也能在不同的场景中正常工作。

在进入下一个函数之前，我们应该检查更多的情况，而不是每个情况都有一个方法来参数化测试:

```
@ParameterizedTest
@ValueSource(ints = [1, 5, 10, 1000, Int.MAX_VALUE])
fun `zero function sets value to 0 at register in position`(position: Int) {
    zero(registry, position)

    val expected = 0
    val actual = registry.getValueAtPosition(position)

    assertEquals(expected, actual)
}

@ParameterizedTest
@ValueSource(ints = [-1, -5, -10, -1000, Int.MIN_VALUE])
fun `zero function throws exception with negative position`(position: Int) {
    assertThrows<IllegalArgumentException> { zero(registry, position) }

    assertNull(registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还利用了 ***Kotlin*** 对带空格的函数名的支持，使用反斜杠删除`@DisplayName`并将名字直接放入函数中。我们可以测试我们的函数，只需将我们想要的值添加到`@ValueSource(ints = [])`中，而不必编写更多的测试用例。

* * *

## 结论

目前，我们已经理解/学习了遵循 TDD 的*设计-测试-检查-修复-重复*循环过程的基本知识。所有这些都有一个简单的函数。

还有很多事情要做，比如为其他函数(`increment`，`jump`)创建测试，但是我们将在下一篇文章中讨论这些。

感谢阅读！