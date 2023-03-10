# 有界组件的故事

> 原文：<https://dev.to/mfrachet/a-tale-of-bounded-components-4af2>

构建可重用的 UI 组件很难。我总是依赖迭代的方法，在得到一些我觉得有用的、可在我的应用程序中重用的东西之前，我会编写 2-3 次我的组件。寻找“好的”抽象和创建它们的“好的”方法有点像一个游戏。

在[一篇关于用 React](https://acodingdance.io/a-note-on-composing-components-with-react/) 合成组件的笔记中，我以[Google Material Design Cards](https://material.io/design/components/cards.html)为例，简要地谈了一下我对合成的构想，以及我将如何实现这样一个东西。这篇文章是上一篇文章的延伸，所以我建议你看看☺️.

今天，我想与大家分享我在基于设计系统实现 UI 组件库时的经验，以及我和我的团队如何成功构建了一个*稍微复杂一些的组件*。

我们来说一下*共享一些*的组件。

## 单选*按钮*

我举单选按钮的例子有两个原因。

第一个是我正在用 [React Native](https://facebook.github.io/react-native/) 构建 UI 组件库，它不提供内置的单选组件，第二个是因为单选按钮有点像*的特殊*。

根据定义，它是一组可选择的元素，一次只能选择一个元素。[这里有一个到**电台**和**电台组**](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/radio) 的 MDN 定义的快速链接。

在 HTML 中，这个链接由`input` `name`属性表示:

```
// this is from MDN

<input type="radio" name="contact" value="email">

<input type="radio" name="contact" value="phone">

<input type="radio" name="contact" value="mail"> 
```

Enter fullscreen mode Exit fullscreen mode

我认为我们可以称这些**为复合语义元素**

如果我们想要构建与前面的无线电元素定义相匹配的 [React](https://reactjs.org/) 组件，这些组件必须与其他组件共享一些信息。

在 React 世界中，我们可以说这些组件**共享某个状态**。

为了管理这种共享状态，我们可以依靠不同的方法。

### 穿越母状态

在 React 世界中，最简单的事情就是依赖父组件状态。

让我们想象下面的片段:

```
const Parent = () => {
  const [selectedRadio, selectRadio] = useState(0)

  return (
    <>
      <Radio onPress={() => selectRadio(0)} isSelected={selectedRadio === 0} />
      <Radio onPress={() => selectRadio(1)} isSelected={selectedRadio === 1} />

      <Text>The selected value is {selectedRadio}</Text>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一种很好的方法，只要我们接受管理每个父组件中`<Radio />`组件的状态，这种方法就会奏效。

然而，在这段代码中，我们丢失了一些东西:**单选按钮的链接性质**。或者至少是无线电元素的*家族*环节。

当然，由于父状态，所选择的值将被反映。但是单选按钮组依赖于父项，而不仅仅依赖于它自己。例如，在 web 平台上，没有父元素来管理元素之间的链接。

### 使用全局状态管理工具(姑且称之为 Redux)

我们还可以依靠一个全局状态管理工具来存储实际选择的值，并在整个应用程序中提供它:

```
const mapStateToProps = (state, ownProps) => ({
  isSelected: state.selectedRadio === ownProps.name,
})

const mapDispatchToProps = (dispatch, ownProps) => ({
  selectMe = () => dispatch({ type: 'SELECT_RADIO', payload: { newSelectedRadio: ownProps.name } })
})

const RadioEnhanced = connect(mapStateToProps, mapDispatchToProps)(Radio)

const Parent = ({ selectedRadio }) => (
  <>
    <RadioEnhanced />
    <RadioEnhanced />

    <Text>The selected value is {selectedRadio}</Text>
  </>
) 
```

Enter fullscreen mode Exit fullscreen mode

这也是一个很好的方法，它的好处是使用全局状态来保持无线电元素的链接性质。

然而，我们必须在存储中为每种不同的无线电组件定义一个新的 Redux 键。我们还必须为每种单选按钮组等创建一个缩减器。即使你不使用 Redux，而是使用另一个全局状态管理系统，这也是一样的。

### [React 的上下文 API](https://reactjs.org/docs/context.html)

我经常听说使用 React 上下文是一种不好的做法。我不完全同意这种说法。我认为，我们必须理解什么时候不使用它，什么时候少用它。上下文是 React 中内置的一个特性，因此它们可能是它的一些很好的用例。

我建议在编写任何组件实现之前，想象一下使用该组件的理想方式。我经常从编写我希望它拥有的形状(它的 API)开始，比方说:

```
const MyComponent = () => {
  const [selected, setSelected] = useState('first')

  return (
    <RadioGroup selected={selected} onChange={setSelected}>
      <Radio name="first">
        <Text>My first radio</Text>
      </Radio>

      <Radio name="second">
        <Text>My second radio</Text>
      </Radio>
    </RadioGroup>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这种 API，因为它简单易懂。

该代码表示一组一起工作的无线电组件。当`<Radio name="first" />`被选中时，`RadioGroup`子树中的所有其他无线电组件将被取消选中。

`RadioGroup`组件的`selected`道具对应所选电台组件的`name`。如果我想选择`first`无线电，那么代码将类似于`<RadioGroup selected="first">...</RadioGroup>`。

我们可以使用 [React 的上下文 API](https://reactjs.org/docs/context.html) 创建这种行为和链接的感觉，其中`RadioGroup`组件拥有其上下文中实际选择的`name`，并在它的不同`Radio`子组件之间共享它。

**这种*隐藏*组件间状态管理的技术叫做*隐式状态传递*** 。我们以一种开发人员不必关心也不必多次实现的方式来管理状态。

[这里是`Radio`和`RadioGroup`实现](https://codesandbox.io/s/elastic-surf-pxxdt)的运行代码沙箱。

* * *

我们现在对`React context` API 有了很好的理解。让我们探索 React 的另一个高级功能，它也允许通过另一个例子传递一些隐式状态:这个例子是`Tabs`。

## `Tabs`举例

在我的帖子中，我谈论了很多关于[瑞恩弗洛伦斯](https://twitter.com/ryanflorence)在[这个视频](https://www.youtube.com/watch?v=hEGg-3pIHlE)中提供的标签的例子。正是这个视频让我意识到我做错了一些事情，我必须理解“构图”的概念。

是定义共享一个可视链接的(UI)界面的 UI 元素。它们必须彼此靠近才能提供良好的用户体验。在设备的左上方创建一个标签，而在右下方创建另一个标签是没有意义的。

我喜欢把`Tabs`归入布局组件一类:它们不是真正面向业务的，也不是原子 UI 组件。它们代表了一种显示信息以及如何在不同类型的信息之间导航的方式。

我们可以想象这种组件使用多种方法，其中一种是我经常使用的数据驱动方法。

### 数据驱动方法

数据驱动方法是一种构建组件的方法，因此组件需要其道具具有特定的形状才能使用。例如:

```
const items = [
  { title: 'First', Component: () => <div>First</div> },
  { title: 'Second', Component: () => <div>Second</div> },
]

const Tabs = ({ items }) => {
  const [selected, setSelected] = useState()
  let SelectedComponent

  if (selected) {
    SelectedComponent = items[selected].Component
  }

  return (
    <div>
      {items.map((item, index) => (
        <div
          key={item.title}
          onClick={() => setSelected(index)}
          style={{ color: selected === index ? 'green' : 'black' }}
        >
          {item.title}
        </div>
      ))}

      {SelectedComponent && <SelectedComponent />}
    </div>
  )
}

// would be used <Tabs item={items} /> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`Tabs`组件知道它的每个项目的形状，以便能够正确地显示它们。这是对象和组件之间的契约。

虽然使用这种方法没问题，但我认为最好想办法避免这种紧密耦合。构图可以帮助实现这一点。

正如我之前提到的，让我们想象一下我们的完美世界 API。在我看来，类似下面这样的东西很棒:

```
<Tabs>
  <TabsHeader>
    <TabHead>First button</TabHead>
    <TabHead>Second button</TabHead>
  </TabsHeader>

  <TabsBody>
    <Tab>
      <FirstComponent />
    </Tab>
    <Tab>
      <SecondComponent />
    </Tab>
  </TabsBody>
</Tabs> 
```

Enter fullscreen mode Exit fullscreen mode

使用 React，我们如何创建不同的`TabXXX`组件，使其以这种方式工作？

我们可以使用上下文 API 来完成这项工作，但是现在，我想介绍一下`React.cloneElement`函数。

### 反应，克隆

这个函数允许克隆一个带有实际属性的 React 元素，可以覆盖它们或者添加新的属性。

可以如下使用:

```
const element = <div>Hello world</div>
const clone = React.cloneElement(element, {
  style: { backgroundColor: 'red' },
})

const App = () => (
  <>
    {element}
    {clone}
  </>
) 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用这个`React.cloneElement`函数的定义来隐式地为组件的子组件提供一些道具。

例如，我们将为`TabHead`组件添加一个道具，这样它们就可以点击了。
我们还将为`TabsBody`组件添加一个`selectIndex`道具，这样他就知道哪个组件必须显示或者不显示。

#### 实现了`Tabs`组件

组件是所有者，一个知道一切的人。它拥有实际选择的索引，并且知道如何修改该选择的索引:

```
const Tabs = ({ children }) => {
  const [selectedIndex, setSelectedIndex] = useState(0)

  return React.Children.toArray(children).map(child =>
    React.cloneElement(child, { selectedIndex, selectIndex: setSelectedIndex })
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`React.cloneElement`通过添加两个道具来增强子组件:实际选择的索引和修改选择的索引的方法。

事实上，我们将把这两个道具分别传给`TabHead` s 和`Tab` s。

`TabsHeader`组件将接收`selectIndex`函数，并通过一个微妙的变化将其传递给它的`TabHead`子组件:我们将限定`TabHead`组件的实际索引，这样它们就可以调用`selectIndex`函数，而无需显式传递它们的索引:

```
const TabsHeader = ({ selectIndex, children }) =>
  React.Children.toArray(children).map((child, index) =>
    React.cloneElement(child, { selectIndex: () => selectIndex(index) })
  ) 
```

Enter fullscreen mode Exit fullscreen mode

`TabHead`将简单地看起来像这样:

```
const TabHead = ({ selectIndex, ...props }) => (
  <button onClick={selectIndex} {...props} />
) 
```

Enter fullscreen mode Exit fullscreen mode

`TabsBody`的作用是只显示匹配所选索引的元素。这可以通过对孩子使用`Array.prototype.find`:
来实现

```
const TabsBody = ({ selectedIndex, children }) =>
  React.Children.toArray(children).find((_, index) => selectedIndex === index) 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个链接，链接到之前代码片段的 codesandbox。

我建议你花些时间分析并熟悉这类代码。这是我在深入研究之前不习惯的事情。

* * *

这就是我们，这些例子解释了更复杂和相互关联的组件！