# 带 URM +科特林的 TDD:增量

> 原文：<https://dev.to/sierisimo/tdd-with-urm-kotlin-increment-3m7c>

在上一篇文章中，我们提到了一种使用 TDD 开始编写 URM 实现的方法。我们也提到了很多在编写`zero`函数时出现的问题。

现在该写增量函数了。

## 增量功能

我们知道`increment`函数将是`I(x)`，其中`x`代表我们*无限寄存器机器*中的一个寄存器。我们现在只编写用于表示我们功能的内部代码。

遵循我们用于`zero`函数的相同方法，我们将从编写一个测试开始，测试我们期望该函数如何处理负数:

```
@ParameterizedTest
@ValueSource(ints = [-2, -8, -20, -100])
fun `increment function throws exception with negative position`(position: Int) {
    assertThrows<IllegalArgumentException> { increment(registry, position) }

    assertNull(registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

这个初始测试支持防御模型，但是请记住，我们首先需要确保该函数在一些无效的情况下会失败。我们可以从代码的不同部分开始，但是这是最容易写的，因为我们已经在`zero`函数:
中写了

```
fun increment(registry: Registry, position: Int){
    require(position > 0) { "Position must be positive number" }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的第二个测试，我们需要验证一个“严格”的规则:当一个寄存器没有值时，它不能递增。我们的测试应该代表这一点，我们应该为它放置一个简单的异常:

```
@ParameterizedTest
@ValueSource(ints = [2, 4, 20, 40])
fun `increment function cannot work with empty register`(position: Int) {
    assertThrows<IllegalStateException> { increment(registry, position) }

    assertNull(registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在此时运行我们的测试，它将会失败:

```
Expected java.lang.IllegalStateException to be thrown, but nothing was thrown.
org.opentest4j.AssertionFailedError: Expected java.lang.IllegalStateException to be thrown, but nothing was thrown. 
```

Enter fullscreen mode Exit fullscreen mode

这个异常告诉我们我们已经知道的事情:我们的函数没有为空寄存器抛出正确的异常。让我们通过使用在`kotlin stdlib`上可用的契约`checkNotNull`来解决这个问题(我将在另一篇文章中写关于契约的内容):

```
fun increment(registry: Registry, position: Int){
    require(position > 0) { "Position must be positive number" }
    checkNotNull(registry.getValueAtPosition(position)) { "Register must be initialized" }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试又通过了。让我们来写实际的实现。

## 真正的实现

实际实现的测试需要更多的设置。我们知道，如果我们的测试只是:
，当前的方法将抛出一个异常

```
@ParameterizedTest
@ValueSource(ints = [11, 15, 110, 200, Int.MAX_VALUE])
fun `increment function adds 1 to value of position`(position: Int) {
    increment(registry, position)

    assertEquals(1, registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我们编写了一个测试，当注册表不包含值并且有人对其调用`increment`函数时，该测试将抛出一个异常。但幸运的是，我们已经为`zero`函数编写了测试，这意味着我们可以在调用`increment` :
之前初始化我们的注册表

```
@ParameterizedTest
@ValueSource(ints = [11, 15, 110, 200, Int.MAX_VALUE])
fun `increment function adds 1 to value of position`(position: Int) {
    zero(registry, position)
    increment(registry, position)

    assertEquals(1, registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

为了修复中断测试，我们需要修改我们的函数:

```
fun increment(registry: Registry, position: Int) {
    require(position > 0) { "Position must be positive number" }

    val currentValue = registry.getValueAtPosition(position)
    checkNotNull(currentValue) { "Register must be initialized" }

    registry.setValueAtPosition(position, currentValue + 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们存储`registry.getValueAtPosition(position)`的值，然后调用合同`checkNotNull`(我保证很快会写合同的！)

但是用单一的`1`进行测试是很无聊的…让我们写一些在不同值上操作的东西。为了进行动态测试，我们需要检查一个参数，然后调用`increment`并获得一个期望值。我们可以简单地给`assertEquals(parameter + 1, registry.getValueAtPosition(parameter))`打电话，但这多少感觉像是欺骗…

使用 JUnit 5 中的*参数化测试*，我们可以创建一个单一的测试，它将在一个单一的方法中覆盖不同的情况，我们将使用`@CsvSource`来传递不同的参数。`@CsvSource`为每组参数取一组逗号分隔的字符串，我们的方法将要求这些参数属于适当的类型:

```
@ParameterizedTest
@CsvSource("50,1,2", "50,5,6", "50,100,101")
fun `increment function adds 1 to any integer value`(
    position: Int,
    original: Int,
    expected: Int
){
    registry.setValueAtPosition(position, original)

    increment(registry, position)

    assertEquals(expected, registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得测试更加动态，并让我们添加更多具有更大灵活性的案例！

让我们用我们所学的知识编写一个测试:

```
@ParameterizedTest
@CsvSource("10,23,24", "15,99,100", "1000,1000,1001")
fun `check zero and increment can work together`(
    position: Int,
    original: Int,
    expected: Int
) {
    registry.setValueAtPosition(position, original)
    increment(registry, position)
    assertEquals(expected, registry.getValueAtPosition(position))

    zero(registry, position)
    assertEquals(0, registry.getValueAtPosition(position))

    increment(registry, position)
    assertEquals(1, registry.getValueAtPosition(position))
} 
```

Enter fullscreen mode Exit fullscreen mode

哇！那是一个测试。我们从手动设置位置值开始。然后我们递增它，以断言增量按预期工作——我们知道它是这样的，因为我们有一个测试来验证它！-然后我们做一些“新的”:我们重置一个寄存器为 0。这里我们验证了一些我们以前没有检查过的东西:函数`zero`可以重置任何注册表而不管其值是多少。最后，我们`increment`并验证`increment`不管值是多少都有效。

* * *

## 结论

我们添加了一个新函数，并且验证了我们之前的工作不会影响我们刚刚添加的新代码。一切都是通过编写测试和修复代码实现的！

仍然有一些事情让测试感觉像作弊…例如手动调用`registry.setValueAtPosition`和定制实现。我们将在下一篇讨论模拟的文章中解决这个问题！

感谢阅读:)