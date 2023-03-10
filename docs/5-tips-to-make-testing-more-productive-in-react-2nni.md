# 在 React 中提高测试效率的 5 个技巧

> 原文：<https://dev.to/akirautio/5-tips-to-make-testing-more-productive-in-react-2nni>

当谈论生产性软件开发时，没有“没有测试”这回事。客户会一直对代码进行测试。你可以依赖他们，也可以自己测试。

测试 React 项目的最快方法是使用自动化测试。以下是关于如何提高工作效率的五点建议:

### 1。在不同的测试类型之间找到平衡

React 应用程序可以通过许多不同的方式进行测试。有单元测试、功能测试、集成测试和端到端测试。每种类型的测试都有它的用例，所以一起使用它们会产生最好的结果。

另一方面，所有这些测试类型都相互重叠。较大的端到端测试很多时候检查与集成和单元测试相同的东西。 ***因为较低层次的测试，比如单元测试，做起来和运行起来都很快，所以建议尽可能地使用它们*** ，并且将更复杂的测试方法保留给单元测试无法完成的用例。

```
// Example tests for the Preview component can be found from Tip 2
const Preview = ({ text }) => text.lenght > 0 && (
 <div data-testid='preview'>{text}</div> )

// Example tests for the Data component can be found from Tip 3
const Data = () => {
  const [text, setText] = React.useState()
  const setPreviewText = (value) => setText(`[${value}]`)
  return {
    <div>
      <Preview text={text} />
      <input 
        data-testid='input-text'
        name='input-text'
        value={text} 
        onChange={({ target }) => setPreviewText(target.value)}
      />
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有两个组件需要测试。预览组件是无状态的，所以我们只需要对相关的输入和输出进行单元测试。

数据组件测试有点复杂，因为逻辑在组件内部。因此，我们需要功能测试来检查逻辑。我们还应该进行集成测试，以查看预览在数据组件中是否正常工作。尽管集成测试只需要测试来自数据组件的相关交互，因为我们已经对预览组件输入进行了单元测试。

### 2。使应用程序结构可测试

结构良好的代码使测试变得更加容易，从而使测试过程更快、更有趣。在 React 中，无状态功能组件最容易测试，因为它们每个输入设置只有一个输出。如果可能的话，这会促使组件保持无状态。

```
const Preview = ({ text }) => text.lenght > 0 && (
 <div data-testid='preview'>{text}</div> )

describe('Preview', () => {
  it('should handle filled text', () => {
    expect(Preview({ text: 'test' }).to.be.equal(<div>test</div>)
  })

  it('should handle empty text', () => {
    expect(Preview({ text: '' }).to.be.equal(undefined)
  })

  it('should handle without text', () => {
    expect(Preview().to.be.equal(undefined) // Note: this will fail :)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

有状态组件通常需要工具通过输入或其他增加复杂性的方式来改变内部状态。好的工具通常在这方面有很大帮助，但是需要更长的时间来找出需要的测试。如果一个组件有一些不依赖于状态的内部函数，一个好的做法是分别测试它们。 这减少了有状态组件中需要处理的测试数量。

```
// Example tests for Data component can be found from the Tip 3
const setPreviewText = (fn, value) => fn(`[${value}]`)
const Data = () => {
  const [text, setText] = React.useState()
  return {
    <div>
      <Preview text={text} />
      <input 
        data-testid='input-text'
        name='input-text'
        value={text} 
        onChange={({ target }) => setPreviewText(setText, target.value)}
      />
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3。使用正确的工具

React 有很多工具来简化测试。现在最常用的可能是:

*   Jest -测试框架提供工具来准备测试
*   [反应测试库](https://github.com/testing-library/react-testing-library) / [酶](https://airbnb.io/enzyme/) -帮助测试组件的附加工具
*   [赛普拉斯](https://www.cypress.io/) / [木偶师](https://github.com/GoogleChrome/puppeteer) -在浏览器上进行端到端测试的解决方案

使用简化复杂任务(尤其是有状态组件)的工具，可以大大加快测试速度，所以花些时间来理解这些工具之间的区别是有好处的。

例如，使用 react 测试库，可以通过以下方式测试数据组件:

```
import { render } from '@testing-library/react'
const setPreviewText = (fn, value) => fn(`[${value}]`)
const Data = () => {
  const [text, setText] = React.useState()
  return {
    <div>
      <Preview text={text} />
      <input 
        data-testid='input-text'
        name='input-text'
        value={text} 
        onChange={({ target }) => setPreviewText(setText, target.value)}
      />
    </div>
  }
}

describe('Data', () => {
  it('should change preview text when input is changing', () => {
    const dataComponent = render(<Data />)
    const input = dataComponent.getByTestId('input-text')
    fireEvent.change(input, { target: { value: 'test' } })
    expect(dataComponent.getByTestId('preview')).toBeEqual('[test]')
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 4。将可视化与代码相关的测试结合起来

视觉和快照测试是确保组件在任何情况下都保持其设计外观的极好工具。这些工具的想法很简单。首先，测试将从代码或者通过创建图像来创建快照。当再次测试相同的组件时，测试工具会将当前情况与快照版本进行比较，并通知更改。如果更改是有意进行的，则快照会更新，否则需要检查代码。

可视化测试在 React 中工作得特别好，因为大多数组件产生了一小部分界面。它们还极大地加快了测试速度，因为它们消除了编写检查 DOM 或样式是否看起来像它们应该的样子的需要。

在单元测试层面， [Jest](https://jestjs.io/docs/en/snapshot-testing) 通过 react-test-renderer 支持开箱即用的快照。

```
import renderer from 'react-test-renderer';

const Preview = ({ text }) => text.lenght > 0 && (
 <div id='preview'>{text}</div> )

it('renders correctly', () => {
  expect(
    renderer
    .create(<Preview text='test'/>)
    .toJSON()
  ).toMatchSnapshot()
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用 Storybook 来开发组件，我会强烈推荐[story shots](https://storybook.js.org/docs/testing/structural-testing/)。这是一种奇妙的方法，可以在不需要的更改进入代码库之前就捕捉到它们。

### 5。在提交或请求时自动运行测试。

保持高代码质量的最简单的方法之一是在每次提交(或拉请求)时运行测试。 ***当只做了相当少量的更改时，发现突破性测试背后的问题会更快*** 并且 bug 存活的时间最少。

如果项目将 GIT 作为版本控制运行，那么在运行提交或推送时，有可能包含附加命令(钩子)。Husky 使这个过程变得更加容易，所以我强烈推荐这个工具。

由于端到端测试几乎永远持续下去，因此循环运行这些测试是有意义的(比如一小时一次或一天一次，这取决于代码库变化的频率)。

* * *

你如何使你的测试有效？请在评论中告诉我:)