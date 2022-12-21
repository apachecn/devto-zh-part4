# 构建可重用的测试查询

> 原文：<https://dev.to/jonmajorc/build-reusable-testing-queries-27m1>

这篇文章的灵感来自于两周前我遇到的一个问题；我编写了与 React Material UI 中的`Select`组件交互的脆性测试。那天花了很多时间尝试了很多解决方案后，我找到了一个令我满意的方案...这就是我今天要分享的解决方案！

**TLDR；通过共享可重用的 DOM 查询，保持测试库的可维护性和不易碎性。访问第三方组件的“抽象细节”的模式可能会随着时间的推移而改变，但是更新可以在一个地方进行。**

## 问题

我想编写可维护的测试，并且类似于我的软件被使用的方式。这意味着我需要在组件中模拟用户交互，包括任何第三方组件。然而...

1.  数据属性不能出现在第三方组件中。
2.  数据属性不能出现在第三方组件内的预期元素上。

我非常喜欢数据测试，但是在使用第三方组件时，我不能总是依赖它们。

**快速靠边:材料`Select`组件使用`react-select`。这篇文章只会在一个虚构的例子中使用`react-select`...**

经过一些调试，我在`react-select`内部的`input`标签上发现了一个`id`。

```
<input
  aria-autocomplete="list"
  autocapitalize="none"
  autocomplete="off"
  autocorrect="off"
  id="react-select-2-input" {/* That's helpful! */}
  spellcheck="false"
  style="box-sizing: content-box; width: 2px; border: 0px; font-size: inherit; opacity: 1; outline: 0; padding: 0px;"
  tabindex="0"
  type="text"
  value=""
/> 
```

Enter fullscreen mode Exit fullscreen mode

通过查询`id`进行测试后，我发现它会根据页面上呈现的 Select 组件的数量而增加。我不会相信这是一个测试 id！这可能随时改变，导致级联测试失败。一个好的经验法则是为测试保留一个 id。然而，我们不能在输入中使用数据属性或这个`id`...我宁愿在组件的根标签上有一个`id`;然后我可以查询组件内部的任何内容...原来，我可以做到这一点！

*“这里是一个热点”*，如果一个组件包不允许数据属性，请阅读文档并了解什么可以作为替代品传递。可能会有一个`id`之类的，可以更名为测试 id。就我而言，我可以做到这一点。在我设计的例子中，我可以创建我自己的内部`Select`组件，用必需的`dataTestId`属性重新引入`react-select`。现在我可以使用我的内部组件，它有一个**可信的**测试 id。

```
// Select.js
import ReactSelect from 'react-select'
import React from 'react'
import PropTypes from 'prop-types'

function Select({ dataTestId, ...props }) {
  return <ReactSelect {...props} id={dataTestId} />
}

Select.propTypes = {
  dataTestId: PropTypes.string.isRequired,
}

export default Select 
```

Enter fullscreen mode Exit fullscreen mode

## 解

让我们继续一些好的旧时尚“接受标准。”

*   我在`Select`组件的输入字段中看到我选择的值
*   我在`Select`组件的正下方的`span` **中看到我选择的值**

这是一个符合验收标准的工作示例，但是我们需要测试来确保我们在生产中避免退化！

```
import React from 'react'
import Select from './Select'

const options = [
  { value: 'chocolate', label: 'Chocolate' },
  { value: 'strawberry', label: 'Strawberry' },
  { value: 'vanilla', label: 'Vanilla' },
]

function App() {
  const [selectedOption, setSelectedOption] = React.useState({})

  return (
    <div>
      <Select
        dataTestId="select-ice-cream"
        value={selectedOption}
        onChange={valSelected => setSelectedOption(valSelected)}
        options={options}
      />
      <span data-testid="select-ice-cream-selected">You selected {selectedOption.value}</span>
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要检查第三方组件，它里面有很多`div`之类的东西。很多我们不关心的“抽象细节”。测试一个未封装的第三方组件可能相当困难，但是这样做让我对应用程序正常工作有了更大的信心。好吧，既然我们没有使用`data-testid`，我们就不能使用 React 测试库中的`queryByTestId`选择器。我将使用 DOM `querySelector`来代替...

```
it('renders without crashing', () => {
  const { container, debug } = render(<App />)
  const inputEl = container.querySelector('[id="select-ice-cream"] input')
  debug(inputEl)
}) 
```

Enter fullscreen mode Exit fullscreen mode

*我不知道 React 测试库查询是否可以查询属性。这就是我们使用 DOM `querySelector`的原因。*我们可以做得更好，我们可以把上面的变成一个自定义查询！更好的是，我将返回一个包含满足接受标准所需元素的对象！

```
it('shows selected value in input field and right below select', () => {
  const { querySelectComponent } = render(<App />, {
    queries: {
      ...queries,
      querySelectComponent: (root, id) => {
        return {
          rootEl: root.querySelector(`[id=${id}]`),
          inputEl: root.querySelector(`[id=${id}] input`),
          spanEl: document.querySelector(
            `div[id=${id}] + span[data-testid='${id}-selected']`
          ),
        }
      },
    },
  })

  const { rootEl, inputEl, spanEl } = querySelectComponent('select-ice-cream')

  fireEvent.change(inputEl, { target: { value: 'strawberry' } }) // change input value to strawberry
  fireEvent.keyDown(inputEl, { key: 'Tab', code: 9 }) // select what the input value has as the selected value

  //Assertions!
  expect(spanEl).toHaveTextContent(/strawberry/)
  expect(getByText(rootEl, 'Strawberry')).toHaveTextContent('Strawberry')   
}) 
```

Enter fullscreen mode Exit fullscreen mode

测试块现在涵盖了验收标准！是的，我们有一个包含抽象细节的非常具体的选择器。`div[id=${id}] + span[data-testid='${id}-selected']`。该选择器确保跨度出现在验收标准描述的`Select`正下方。*用户应选择一个值，并在`Select`的输入栏和 `Select`正下方的`span` **内看到选择的值。***

当前的测试块具有抽象组件选择器细节的查询。让查询在任何测试块中可重用是理想的。任何需要与`Select`组件交互的人都可以在他们的测试中使用相同的选择器模式。每个测试都可以重用相同的模式来访问第三方组件，或者可能是内部组件的抽象细节。但是当`react-select`更新时，我可以从一个地方更新我的查询！

```
//testUtils.js
export const selectComponentQueries = (root, id) => {
  return {
    rootEl: root.querySelector(`[id=${id}]`),
    inputEl: root.querySelector(`[id=${id}] input`),
    spanEl: document.querySelector(
      `div[id=${id}] + span[data-testid='${id}-selected']`
    ),
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//App.test.js
it('shows selected value in input field and right below select', () => {
  const { container } = render(<App />)

  const { rootEl, inputEl, spanEl } = selectComponentQueries(
    container,
    'select-ice-cream'
  )

  fireEvent.change(inputEl, { target: { value: 'strawberry' } })
  fireEvent.keyDown(inputEl, { key: 'Tab', code: 9 })

  expect(spanEl).toHaveTextContent(/strawberry/)
  expect(getByText(rootEl, 'Strawberry')).toHaveTextContent('Strawberry')
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

组件的抽象细节可以改变。保持一个测试库的可维护性，对于类似查询这样的事情使用可共享的测试工具。这样，所有的测试都使用相同的可重用代码。将查询放在一个单一的源中会使改变变得容易得多。

* * *

你好！我是乔恩·康登少校。我是一名资深软件农民，在 [Bendyworks](https://bendyworks.com/) 负责客户代码库。作为一名软件农民，我专注于网络上的任何东西，但我的好奇心通常会让我掉进兔子洞...*“乔恩·梅杰刚刚掉进了另一个兔子洞……敬请期待下一篇博文！👋”*