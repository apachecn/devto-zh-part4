# 用 JUnit 进行单元测试

> 原文：<https://dev.to/sierisimo/unit-testing-with-junit-3mcd>

我已经解释过[编写测试并不困难](https://dev.to/sierisimo/testing-is-not-that-hard-54e7)
但是我得到了一些关于它的评论…我可以用一句话来总结这些评论:

> 代码示例在哪里？

所以这一次我将用一些简单的例子更多地关注代码部分。

注意:在这篇文章中，我不会遵循 TDD、BDD 或任何特定的方法论。我想解释如何思考测试，以及如何作为第一种方法来编写它们。同样值得一提的是:我将为 JVM 提供大部分 Kotlin 代码，这些概念仍然适用于其他技术。

## 单元测试

当你写代码时，你希望看到它工作，你希望看到在你的头脑中形成的魔法在计算机中工作，你希望证实一切都准备好了，可以进行你的 NITRO-TURBO-SUPER-MEGA-AWESOME-PROJECT 的下一步。但是，每次都运行整个程序，对整个相关代码进行编译，通过前面的屏幕/步骤到达您想要测试的部分，或者简单地添加一千个*日志*作为检查所有工作的过程的一部分，这些都很烦人，而且会花费很多时间。此外，您需要对每个大的变更(或者更小的变更)都这样做，以验证这个新东西不会破坏其他东西。

编写单元测试可以将你从这种情况中解救出来。单元测试是验证系统最小部分的单一测试。它通常不需要太多的设置，并且易于阅读。执行单元测试包括检查一个单一的功能在好的和坏的情况下都能正常工作。

让我们从一个简单(也是经典)的例子开始: **FizzBuzz** 。

FizzBuzz 的规则很简单:

> 一群孩子在数数字，他们轮流根据他们得到的数字说些什么:
> 
> 1.  当数字是 3 的倍数时，他们应该说“嘶嘶”而不是数字
>     
>     
> 2.  当数字是 5 的倍数时，他们应该说“Buzz”而不是数字
>     
>     
> 3.  当数字都是 3 和 5 的倍数时，他们应该说“FizzBuzz”而不是数字
>     
>     
> 4.  否则他们只会说数字

这些规则看起来简单又有趣，那么我们的代码又如何呢？嗯，我们决定使用一个简单的函数:

```
fun fizzBuzz(countUntil: Int): List<String> 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将为 FizzBuzz 游戏的每个元素正确工作，并将结果返回给我们。为了保持测试的焦点，我删除了这个函数的主体。

### 我们的设置

假设前面的函数位于文件系统中:`project/src/main/kotlin/net/sierisimo/games/FizzBuzz.kt`

那么我们的测试将在:`project/src/test/kotlin/net/sierisio/games/FizzBuzzTest.kt`

这遵循 Java 的标准，也是 Kotlin 在官方文档中建议的。

我们将使用 JUnit 5 进行测试，所以如果这篇文章遗漏了什么，您应该查看一下官方文档。到目前为止，您唯一需要知道的是 JUnit 是一个在 JVM 上运行测试框架的工具。这将有助于我们运行测试和做一些检查。

让我们编写测试用例吧！

## 有效案例

首先，我们需要关注有效案例，以检查函数是否按预期工作(添加引号以表示它们是字符串):

1.  当我们经过数字 1 时，我们应该返回:`["1"]`
2.  当我们经过数字 3 时，我们应该返回:`["1","2","Fizz"]`
3.  当我们经过数字 5 时，我们应该返回:`["1","2","Fizz","4","Buzz"]`
4.  当我们经过数字 60 时，我们应该返回:`["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz", "16", "17", "Fizz", "19", "Buzz", "Fizz", "22", "23", "Fizz", "Buzz", "26", "Fizz", "28", "29", "FizzBuzz", "31", "32", "Fizz", "34", "Buzz", "Fizz", "37", "38", "Fizz", "Buzz", "41", "Fizz", "43", "44", "FizzBuzz", "46", "47", "Fizz", "49", "Buzz", "Fizz", "52", "53", "Fizz", "Buzz", "56", "Fizz", "58", "59", "FizzBuzz"]`

这个清单可以一直列下去。目前，我们将继续关注这四个案例。

第一件事是创建一个类来保存我们的测试，并在其上设置一些方法来表示我们的有效案例:

```
import org.junit.jupiter.api.Test

class FizzBuzzTest {
    @Test
    fun whenWePass1WeGotASingleItemList() {
        //…
    }

    @Test
    fun whenWePass3TheLastItemIsFizz(){
        //…
    }

    @Test
    fun whenWePass5TheLastItemIsBuzz(){
        //…
    }

    @Test
    fun whenWePass60TheLastElementIsFizzBuzz(){
        //…
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这堂课上，你会注意到:

*   每个方法前的注释`@Test`。这告诉 JUnit 哪些方法是测试用例，应该单独运行。
*   名称更长，更具描述性

在 Kotlin 和 JUnit 5 中也有机会对方法名使用**反勾**符号:

```
internal class FizzBuzzTest {
    @Test
    fun `when 1 is passed a single item list is returned`() {
        //…
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您不喜欢这个符号中的名称，您也可以使用注释在测试报告中添加一个有趣的名称:

```
@DisplayName("when 3 is passed the last item is 'Fizz'")
@Test
fun whenWePass3TheLastItemIsFizz(){
    //…
} 
```

Enter fullscreen mode Exit fullscreen mode

风格取决于你，甚至有办法让展示更加花哨或复杂。

一旦我们有了测试，让我们说一下每个测试都涉及到哪些部分。

我通常认为一场考试有三个部分:

```
@Test
fun myTestCase(){
  // Setup of data
  // Call to my SUT (System Under Test)
  // Assertions/Verifications
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的第一个测试是:

```
@Test
fun when1IsPassedASingleItemListIsReturned() {
    //Setup of data:
    val limit = 1
    //Call
    val actual = fizzBuzz(limit)
    //Assertions/Verification
    assertTrue(actual.isNotEmpty())

    assertEquals(1, actual.size)
    assertEquals("1", actual.first())
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你在 IntelliJ/Android Studio 或 Gradle/Maven 上运行这个，你会注意到你的功能是否正常。

像`assertEquals`和`assertTrue`这样的断言包含在 JUnit 5 中，但是你可以在互联网上搜索其他的断言库，它们包含了一组更加通用和多样化的断言(比如 [AssertJ](http://joel-costigliola.github.io/assertj/)

这里有一个简单的例子来说明如何以测试结束课程:

```
package net.sierisimo.games

import org.junit.jupiter.api.Assertions.assertEquals
import org.junit.jupiter.api.Assertions.assertTrue
import org.junit.jupiter.api.DisplayName
import org.junit.jupiter.api.Test

internal class FizzBuzzTest {
    @DisplayName("when 1 is passed a single item list is returned")
    @Test
    fun when1IsPassedASingleItemListIsReturned() {
        //Setup of data:
        val limit = 1
        //Call
        val actual = fizzBuzz(limit)
        //Assertions/Verification
        assertTrue(actual.isNotEmpty())

        assertEquals(1, actual.size)
        assertEquals("1", actual.first())
    }

    @DisplayName("when 3 is passed the last item is 'Fizz'")
    @Test
    fun whenWePass3TheLastItemIsFizz() {
        val limit = 3

        val expected = "Fizz"
        val actual = fizzBuzz(limit)

        assertTrue(actual.isNotEmpty())
        assertEquals(expected, actual.last())
    }

    @DisplayName("when 5 is passed the last item is 'Buzz'")
    @Test
    fun whenWePass5TheLastItemIsBuzz() {
        val limit = 5

        val expected = "Buzz"
        val actual = fizzBuzz(limit)

        assertTrue(actual.isNotEmpty())
        assertEquals(expected, actual.last())
    }

    @Test
    fun whenWePass60TheLastElementIsFizzBuzz() {
        val limit = 60

        val expected = "FizzBuzz"
        val actual = fizzBuzz(limit)

        assertTrue(actual.isNotEmpty())
        assertEquals(expected, actual.last())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 无效病例

快乐之路的测试很棒，显示了该功能工作正常，但我们知道用户不会那样工作。他们不遵循快乐的道路，他们总是试图破坏我们的东西，他们给我们的代码发送奇怪的东西。

我们能做的最好的事情就是为无效的案例编写测试。首先想到的是:

1.  零不是有效的参数，函数应该返回一个空列表:`[]`
2.  负数不是有效参数，函数应该返回空列表:`[]`

> 对于其他语言是可选的:检查数据类型是否正确也值得测试。对于 Kotlin 来说没有必要，因为这是在编译时检查的。

这两种情况非常简单，我们甚至可以将它们放在一个测试中(并引入新的注释):

```
@ParameterizedTest
@ValueSource(ints = [0, -1, -50, -1000, -123459, Int.MIN_VALUE])
fun whenLimitIsLessOrEqualThanZeroReturnsEmptyList(limit: Int) {
    val actual = fizzBuzz(limit)

    assertTrue(actual.isEmpty())
} 
```

Enter fullscreen mode Exit fullscreen mode

`@ParameterizedTest`允许多次执行测试，将`@ValueSource`中找到的值分别传递给测试方法。测试方法需要一个参数。

这种测试的一个很大的优点是，我们现在能够用一个方法测试所有我们想要的无效测试用例，如果需要一个行为相同的新用例，我们可以把它添加到`@ValueSource`列表中。

* * *

## 结论

这只是一个关于如何为一个单一函数编写单元测试的简单介绍，当我们需要传递更复杂的数据或者我们需要测试其他类型的元素时，事情可能会变得更复杂，但是我们仍然可以看到*测试并没有那么难*，我们可以很容易地验证我们使用的代码。如果你对最终的测试类感兴趣，可以在这里找到[，还有这篇文章的降价信息](https://github.com/sierisimo/publications/tree/master/UnitTestingWithJUnit)。

再次感谢您的阅读！

(还要特别感谢约翰·萨拉维亚和 T2·亚历杭德罗·特列斯帮助我学习语法和拼写)