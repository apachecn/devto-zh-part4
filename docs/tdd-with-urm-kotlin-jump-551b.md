# TDD 与 URM +科特林:跳

> 原文：<https://dev.to/sierisimo/tdd-with-urm-kotlin-jump-551b>

在这里，我们再次试图建立一个 URM 解释器。

这一次我们将进行最难的部分，即`jump`函数。应该很容易吧？让我们记住跳转函数的作用:

> 它采用两个位置作为参数和一个指令号。如果位置寄存器的值相等，那么它将执行移到指令号”

#### 什么？！？

放松，这比你想象的要容易。让我们看一个例子:

你有注册表:

```
X: [,,4] 
```

Enter fullscreen mode Exit fullscreen mode

还有那套指令:

```
1: Z(2)
2: J(2, 3, 10)
3: I(2)
4: J(2, 2, 2) 
```

Enter fullscreen mode Exit fullscreen mode

这组指令只会将一个注册表中的号码克隆到另一个注册表中。在这种情况下，位置 2 处的注册表将最终具有与注册表 3 相同的值。不相信我？没问题！让我们试一试，看看内存中会发生什么:

```
1: Z(2)        -> X: [,0,4]
2: J(2, 3, 10) -> X: [,0,4] // 0 != 4 so we continue execution at line 3
3: I(2)        -> X: [,1,4]
4: J(2, 2, 2)  -> X: [,1,4] // 1 == 1 comparing to the same position is always true, so we move back to line 2

2: J(2, 3, 10) -> X: [,1,4] // 1 != 4 so we continue execution at line 3
3: I(2)        -> X: [,2,4]
4: J(2, 2, 2)  -> X: [,2,4] // 2 == 2 comparing to the same position is always true, so we move back to line 2

2: J(2, 3, 10) -> X: [,2,4] // 2 != 4 so we continue execution at line 3
3: I(2)        -> X: [,3,4]
4: J(2, 2, 2)  -> X: [,3,4] // 3 == 3 comparing to the same position is always true, so we move back to line 2

2: J(2, 3, 10) -> X: [,3,4] // 3 != 4 so we continue execution at line 3
3: I(2)        -> X: [,4,4]
4: J(2, 2, 2)  -> X: [,4,4] // 4 == 4 comparing to the same position is always true, so we move back to line 2

2: J(2, 3, 10) -> X: [,3,4] // 4 == 4 so let's move to instruction 10… wait… there's no instruction 10, so we finish the execution 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`J`又名`jump`函数是我们机器中的一个简单的`go-to`。它将执行从一点移动到另一点。我们需要创建一种方法在我们的解释器上表现这一点。

## 但首先……测试

从我们的`jump`函数开始，我们需要对它进行测试。我们知道该函数将使用一个注册表来获取要比较的值，并使用一个整数来将执行移动到该行。现在，我们将把这组指令传递给函数，以允许`jump`函数移动*当前的*索引指令。
我们可以利用已经创建的其他函数来设置一些寄存器，然后检查函数是否按预期工作:

```
@Test
fun `jump function should move to instruction X when registries are equal`() {
    zero(registry, 5)
    zero(registry, 10)

    jump(registry, 5, 10, instructionSet, 1)

    assertEquals(1, instructionSet.current)
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，那实际上是一个丑陋的函数。它需要 5 个参数，大概是 Bob 大叔在看我们，感觉很失望……好吧，我们以后再重构。现在让我们看看它是否能工作……嗯，实际上我们知道它不能工作，正如我们之前看到的中的[，如果我们运行`gradle check`来运行测试，它将生成一个*编译时错误*，因为我们还没有创建`jump`函数。](https://dev.to/sierisimo/tdd-with-urm-and-kotlin-1dj7)

让我们来解决这个问题:

```
fun jump(registry: Registry, positionX: Int, positionY: Int, instructionSet: ?, instruction: Int) {

} 
```

Enter fullscreen mode Exit fullscreen mode

在继续之前，我们需要定义我们的`instructionSet`将如何表现或者它将是什么类型的数据。要记住的事情:

*   它应该根据需要保存尽可能多的指令
*   需要知道哪个是当前指令，哪个是基于索引的下一个指令
*   需要轻松地将执行指针更改为任何其他指令

我们可能需要更多来自`instructionSet`的功能，但是这三个需求告诉我们我们需要一个类，这将让我们至少编写完成失败测试所需的最小量。首先我们需要改变测试，为`instructionSet` :
添加一个类型

```
internal class OperationsTest {
    //…

    val instructionSet = InstructionSet()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在测试中使用它，但是等等，测试要求`instructionSet`有一个`current`属性。让我们创建类并添加属性来移除*编译时错误* :

```
class InstructionSet {
    var current: Int = 0
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`var`,因为我们将在指令集的每次执行中将该值设置为不同的数字。这个类只保存数据，实际上不需要为我们当前的目的做任何其他事情。我们的测试应该通过了，对吗？没有编译时错误，而且非常简单。

不完全是。如果我们运行经典:`gradle check`我们将得到:

```
expected: <1> but was: <0>
org.opentest4j.AssertionFailedError: expected: <1> but was: <0>
... 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我们忘记给函数添加一个实现了！咄！嗯…是时候写实际的代码了:

```
fun jump(registry: Registry, positionX: Int, positionY: Int, instructionSet: InstructionSet, instruction: Int) {
    instructionSet.current = instruction
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，现在测试通过了！你可以自己去查。请[转到这个提交](https://github.com/sierisimo/publications/commit/929e3bb3dfff455fed5dccb51a2f716e5e43ebeb)，它包含这个部分的所有代码并检查它。这是值得的。

在这一点上，你可能认为我疯了，但我没有。这是发生在 TDD 上的一件公平的事情:您创建了一个测试，然后您创建了通过测试的代码，让我们继续。没有人说实现是正确的，但是测试通过了。我们需要更深入的测试:

```
@Test
fun `jump function won't move to instruction X when registries are different`() {
    //Setup
    instructionSet.current = 3

    zero(registry, 5)
    zero(registry, 10)
    increment(registry, 10)

    jump(registry, 5, 10, instructionSet, 10)

    //Assertions
    assertNotEquals(10, instructionSet.current)
    //If the jump function did not matched it should go in the next instruction
    assertEquals(4, instructionSet.current)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我们进行这个项目，我们将会失败！这意味着我们的另一个测试按照我们编写的方式是正确的，但是函数的实现实际上是错误的。让我们解决这个问题。
我们需要验证寄存器中的值是否相等，只有这样我们才会更新`instructionSet`上的位置。否则我们应该继续执行下一条指令。添加通过测试的最小值我们的测试将再次通过:

```
fun jump(registry: Registry, positionX: Int, positionY: Int, instructionSet: InstructionSet, instruction: Int) {
    val xValue = registry.getValueAtPosition(positionX)
    val yValue = registry.getValueAtPosition(positionY)

    if (xValue == yValue) {
        instructionSet.current = instruction
    } else {
        instructionSet.current++
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们终于想出了一个做`jump`功能的方法！我们添加了最终的测试和修复，以匹配我们的 URM 的其他操作符(这里没有包括，所以你可以把它作为家庭作业来做，或者看一看最终的代码来作弊):

*   `jump`功能不能在负位置上运行(应抛出`IllegalArgumentException`)
*   `jump`函数不能处理空寄存器(应抛出一个`IllegalStateException`)

这非常容易，现在我们可以进入 URM 实施的下一部分了。

我们有一些悬而未决的事情，我们将稍后实现:

*   `jump`函数有 5 个参数。那是丑陋的
*   所有函数都将一个`Registry`作为第一个参数。我们可以解决这个问题
*   这不是最终的实现，我们会做很多重构，因为这是用 TDD 构建周期的一部分
*   没有操作，如果我们添加了测试，我们应该添加它们
*   我们仍然使用存根`Registry`。我们需要稍后解决这个问题，以使测试更加可信
*   测试是活的代码，也是我们的项目，对修复和重构我们的测试保持开放
*   有些测试会因为数据处于错误状态而不时失败，我们的`Registry`需要在每次测试时重置…