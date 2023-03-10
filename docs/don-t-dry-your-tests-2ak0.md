# 不要干燥你的测试🌵🏜🦂

> 原文：<https://dev.to/avanslaars/don-t-dry-your-tests-2ak0>

对我来说，测试提供了 3 个主要的好处(按这个顺序):

1.  作为代码的第一批消费者(至少在遵循 TDD 的时候)，测试是一种很好的方式来告知关于代码的公共 API 的决策。
2.  测试是你的代码的活文档。与书面文档(也很重要)或代码注释不同，如果测试通过了，就可以保证与您的代码同步。
3.  一套可靠的测试是一个很好的安全网，可以在重构代码和添加特性时捕捉到意想不到的破坏性变化。

## 问题

许多开发人员听说， [DRY(不要重复自己)](https://en.wikipedia.org/wiki/Don't_repeat_yourself)并把这个概念应用到**代码复制**而不是它的预定目标，**知识复制**。这通常会导致过于复杂的代码，旨在删除一些字符，如果你足够仔细地观察，这些字符恰好是相同的形状。

您的每个测试都应该独立进行。您的检测应该是独立的，没有副作用，因此它们不依赖于顺序，并且一个检测不能产生在其他检测中导致假阳性(或假阴性)的副作用。

当人们试图“干燥”他们的测试时，这会导致测试依赖于大量共享的效用函数和存储在周围作用域中的值，目的是减少重复。这导致了大量的间接性，并为难以发现和调试起来令人沮丧的意外副作用打开了大门。

除了潜在的副作用之外，所有的间接性使得从长远来看维护测试代码更加困难，并且损害了单个测试的整体可读性。如果你不能看着一个测试并理解什么是被测试的和被测试代码的预期用途，那么它就没有发挥出作为活文档的潜力。以这种方式编写测试并让它们通知 API 决策也很困难，因为您将代码实现分散在您的测试实用程序中，并且您不像消费者那样在您的测试中直接使用代码。

## 解

不要抹杀你的测试！

相反，你可以考虑倾向于[写两遍(湿的)](https://twitter.com/search?q=write%20everything%20twice&src=typed_query)，或者在你的测试中...你需要多少次都行。

## 我们来看一个例子

首先，设置一个小的上下文，这是对一个 [React](https://reactjs.org/) 组件的测试，项目被配置为使用 [Jest](https://jestjs.io/) 、[测试库](https://testing-library.com/)和 [jest-dom](https://testing-library.com/docs/ecosystem-jest-dom) 来扩展 Jest 的匹配器以包括`toBeInTheDocument`。

```
it('should correctly apply the testId', () => {
  const testId = 'test-id'
  const { getByTestId } = renderBasicButton({ testId })

  expect(getByTestId(testId)).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？让我们从这 3 行测试代码中可以确定的内容开始:

*   这个测试使用一个名为`renderBasicButton`的函数来呈现一个“基本”按钮，这个函数必须在测试的周围范围内的某个地方定义
**   那个`renderBasicButton`函数接受一个可以有`testId`属性的对象*   它使用作为从`renderBasicButton`返回的对象的一部分提供的`getByTestId`函数来检查该元素是否可以在文档中找到。*

 *根据测试描述、这里使用的库的一些知识，以及我们能够从代码本身推测的事情，不难想象这里发生了什么:

*   `renderBasicButton`必须使用`testing-library`的`render`功能，这就是`getByTestId`的由来。这不是一个给定的，但如果你熟悉`testing-library`，这似乎是合乎逻辑的。
*   它必须呈现一个按钮，并将`testId`值应用于结果`button`元素的`data-testid`属性。这也假设了`testing-library`的知识。

现在，在我们查看支持该测试的代码之前，让我们列出我们仍然存在的问题:

*   当按钮被渲染时，还会发生什么*或*？
    *   它有其他道具吗？
    *   它有孩子吗？
    *   这些东西对这个测试有影响吗？
    *   如果有人改变了那个`renderBasicButton`函数内部的东西，这个测试会开始失败吗？
*   我们非常确定它返回的对象包含从`testing-library`的`render`函数返回的函数。
    *   是直接退那个吗？
    *   它会返回其中的一个子集吗？
    *   它是增加返回值并返回一个超集吗？

让我们来看看`renderBasicButton`函数，看看我们是否能回答这些问题

```
const renderBasicButton = (myProps = {}) => {
  const props = {
    ...BASE_BUTTON_PROPS,
    ...myProps,
  }

  return render(<Button {...props}>{TEXT}</Button>) } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有进展了。我们现在可以回答*一些*问题了:

*   这个*实际上使用了`render`并直接返回结果。没有子集或超集。*
*   我们还可以看到，我在 object 参数中传递的任何东西都与`BASE_BUTTON_PROPS`合并，以创建扩展到`Button`组件中的道具。
*   组件`Button`正在以`TEXT`为子组件进行渲染

因此，我们离测试代码只有一步之遥了，画面变得清晰了，但仍然不是 100%清晰。我们有一些来自周围范围的值，所以让我们看看`TEXT`和`BASE_BUTTON_PROPS`，看看测试中实际呈现的是什么。

```
const TEXT = 'Test Text'

const BASE_BUTTON_PROPS = {
  id: 'testId',
  onClick: jest.fn(),
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道`TEXT`只是一个字符串，所以没有什么比你在“基本”按钮中期望的更多。而`BASE_BUTTON_PROPS`提供了一个`id`和一个`onClick`，它们使用了 Jest 中的间谍功能。

现在我有了所有的片段，我可以重新构建在这个测试中呈现的按钮。如果我写出它的 JSX，它会是这样的:

```
<Button onClick={jest.fn()} id="testId" testId="test-id">
  Test Text
</Button> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了最终的 JSX 是什么，我们可以修改测试，直接使用 RTL 的`render`函数:

```
it('should correctly apply the testId', () => {
  const testId = 'test-id'
  const { getByTestId } = render(
    <Button onClick={jest.fn()} id="testId" testId={testId}>
      Test Text
    </Button>
  )

  expect(getByTestId(testId)).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

我保留了`testId`变量，以避免复制字符串值的潜在错误，但是它和其他东西一样在测试中是正确的，所以您不必去代码中的其他地方查看该值。

这更好，但我们仍然可以做得更好。因为我们最终只是验证`testId`道具是否被正确渲染到 DOM 中作为`data-testid`，所以我们并不真正需要这个按钮有一个`id`值或者点击处理程序。让我们移除这个测试不需要的道具:

```
it('should correctly apply the testId', () => {
  const testId = 'test-id'
  const { getByTestId } = render(<Button testId="test-id">Test Text</Button>)

  expect(getByTestId(testId)).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们也不需要文本，但是一个没有内容的按钮并不是一个很好的用法示例，但是我不想让它偏离测试的要点，所以让它变得更微妙一点:

```
it('should correctly apply the testId', () => {
  const testId = 'test-id'
  const { getByTestId } = render(<Button testId="test-id">click</Button>)

  expect(getByTestId(testId)).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

我认为通过在这里使用更短的、全小写的字符串，我们已经最小化了它的视觉外观，使它更容易关注测试代码的重要部分。

你可以很容易地争辩说这不值得，或者文本不是正在测试的内容，所以可以删除。在这件事上，我可以走任何一条路，而且对任何一条路都没有强烈的意见。

## 我们收获了什么？

### 降低认知负荷

通过将测试的所有相关部分放在测试函数中，我们可以更容易地看到正在测试什么，对于这个特定的测试来说什么是重要的，以及这个组件的消费代码看起来像什么，尽管是一个不完整的代码片段。在我们最初的测试中，将这些拼凑在一起是可能的，但是这需要我们跳过代码来收集我们需要的所有信息。

### 明确意图的信号

我们已经删除了一些不相关的代码，并明确表示`id`和`onClick`道具与`testId`无关。如果我们所有的测试只揭示了代码的重要部分，一个按钮上有多个道具的测试将表明所有包含的部分都与该测试相关。在一个更复杂的场景中，你很可能有对测试中的功能很重要的模拟，当你在测试中定义它们的行为时，它使你的代码中副作用的影响变得清晰，而你不必跟踪模拟逻辑。如果这些模仿的行为是在别处定义的，你很容易会错过它们。另一方面，如果您的代码运行需要一个模拟，但它不是预期测试的一部分，那么您可以通过给它一个通用值来表明模拟是否不重要，比如用空数组解析一个模拟服务，string`default`的对象，这取决于该服务应该返回什么

### 简化测试代码维护

如果这里测试的特性在将来发生了变化，我们可以在一个地方进行修改。如果我们将来不再需要这个特性，我们可以删除这个测试，并且确信我们没有留下孤立的实用函数。当然，linter 可以为我们捕捉到这一点，但我认为这是值得一提的，是这种方法的一个很好的额外特性。

* * *

当然，这很可能是非常主观的，但是我在我的经验中发现，当我进入一个新的测试代码库时，测试套件越“干燥”，我就越难解析。与测试独立的情况相比，我花了更多的时间来拼凑东西。

如果面临跨测试复制代码和为该测试代码创建抽象之间的选择，请考虑当您忘记了代码部分中的一些细微差别时，对新团队成员甚至是当前团队成员的影响。如果你有清晰的、容易分析的测试，你会感谢理解代码和测试所需的认知负荷的减少。*