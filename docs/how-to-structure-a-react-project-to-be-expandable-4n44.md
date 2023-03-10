# 如何构建一个可扩展的 React 项目

> 原文：<https://dev.to/akirautio/how-to-structure-a-react-project-to-be-expandable-4n44>

我喜欢 React 的最大原因之一是，它对如何做事情没有什么限制。这也包括项目的结构。这种自由也有它的缺点。一旦项目开始变大，选择一个糟糕的结构可能会带来一些麻烦。最常见的迹象是，一个组件中的变化将破坏多个不相关的特性，并且创建全面的测试开始变得不可能。

在准备这篇文章的时候，我最终搜索了其他人写的东西，哦，天哪，有很多指南。其中最著名的可能是下面的推特帖子:

> ![Dan Abramov profile image](img/17c386e6839df2dd490f297a2c139b1e.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ fbartho](https://twitter.com/fbartho)虽然是一个指引，它的字面意思是“从把所有东西放在一个文件中开始；感觉烦的时候，就开始把他们分了；那有什么烦人的，也许加些文件夹”。2018 年 8 月 08 日下午 17:43[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1027248875072114689)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1027248875072114689)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1027248875072114689)

尽管我仍然相信，从长远来看，某些好的实践将会简化和加速开发。

## 用原子设计限制嵌套

如果你以前没有听说过原子设计，我建议你先看看布拉德·弗罗斯特和 T2·卡蒂亚·惠勒的文章。重点是项目的整个 UI 部分有以下五个层次:

*   原子
*   分子
*   有机体
*   模板
*   页

这种结构有两种优点:

*   它将嵌套限制在只有五层。虽然嵌套本身并不是一个坏习惯，但是拥有大量的层会使组件更难重用和维护。甚至 [React 文档](https://reactjs.org/docs/faq-structure.html)也鼓励避免过多嵌套。

*   它定义了每个组件级别的责任和期望。每个页面都应该有公共部分(模板)和页面特定部分(有机体)。所有的生物都是由原子组成的分子组成的。

我发现这两个特性对我的项目非常有帮助，因为它们给出了内容分解成组件背后的推理。例如，如果一个原子有很多复杂的逻辑，它可能根本就不是原子。

除此之外，Katia 写的东西，我一直在努力保证所有组件只导入它下面的其他组件。换句话说，分子应该只引入原子，而不引入其他分子。如果我需要连接两个分子，那么我会在有机体水平上做。这使得连接更易于管理，因为项目中的依赖项看起来不像蜘蛛网，而更像一棵树。

```
import * as React from 'react'
import { ListContainer, Container, Title, Value } from './atoms'

const List = ({ items = [], component: Component, ...props } ) => (
  <ListContainer>
    {items.map(item =>
      <Component {...item} {...props} />
    )}
  </ListContainer> )

const ListItem = ({ name, value, onChange }) => (
  <Container>
    {name}
    <Value onChange={onChange}>{value}</Value>
  </Container> )

const ListSetup = () => (
  <List 
    component={ListItem} 
    items={[
      { name: 'Name', value: 'value'}
    ]}
    onChange={() => console.log('Do something')}
  /> ) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中重用列表组件非常容易，因为它可以接受任何呈现列表项的组件。新的列表项组件只需要具有相同的属性，并且开箱即用。

## 结构化状态管理

在项目的某个阶段，需要以某种方式处理状态。这可以是简单地给组件添加一个状态处理或者使用一个全局状态。

我发现从长远来看，将状态和表示分离会使开发变得更容易。它将状态集中在几个组件下，并使 rest 成为无状态的。由于缺少转换，无状态组件更容易进行单元测试，而在有状态组件上，我们可以只关注状态变化。例如:

```
import * as React from 'react'

const Input = ({ name, value, onChange } ) => ( 
  <input name={name} value={value} onChange={onChange}/> ) 

const FormInput = ({ name }) => {
  const [value, setInput] = React.useState()
  const onChange = ({ target: { value} }) => setInput(value)
  return(
    <Input name={name} value={value} onChange={onChange} />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 常见组件

除了分成无状态和有状态组件之外，最好将组件分成特定于页面的和通用的组件。公共组件应该呈现项目中常用的部分，比如排版和表单元素。

我已经看到了让每个原子和分子级别的组件通用的很多好处，但这并不适用于所有人。当底层组件变得普遍可用时，它们将足够通用，从而对项目的其他部分也有好处。

## 基于功能的开发

构建项目的另一个常用实践是根据特性对组件进行分组。这使得项目很容易扩展，因为每个新特性都有一个新的结构。

对于 Javascript 和 NPM 包，有两种方法可以实现基于特性的结构化。要么将特性拆分到包内的不同目录，要么将每个特性都作为一个单独的包。

一包多能:

```
├── package.json
└── src
    ├── feature1
    │   └── Feature1.jsx
    └── feature2
        └── Feature2.jsx 
```

Enter fullscreen mode Exit fullscreen mode

多包多功能:

```
├── package.json
└── packages
    ├── Feature1
    │   ├── package.json
    │   └── src
    │       └── index.js
    └── Feature2
        ├── package.json
        └── src
            └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

独立的包通常用于更大的项目和有很多独立元素的包中。独立的包给了项目更多的控制权，因为包的版本是独立的。它还有助于显示应用程序的哪个部分使用了什么包。

不利的一面是，当在特性之间移动组件时，单独的包会产生更多的工作，因为源和目标特性都需要新的版本。我建议，只有当单独的包比单一的包更有优势，并且对如何分割项目有了清晰的认识时，才使用单独的包。

## 将好的实践应用到实际项目中

为了总结良好的实践，让我们创建一个示例结构来展示它们在实际项目中的工作情况:

*   **组件**排版和表单元素等常用组件的目录。这里的元素可以从原子或分子的层次来完成，但不能超出这个层次。

*   **Pages** 目录来处理特定于页面的内容。一个单独的页面应该由有机体组成，有机体应该只使用共同的组件和原子。

*   **Data** 目录为所有与转换或业务逻辑相关的组件分别保存表示和状态。项目的大多数有状态组件应该在数据目录下，这些组件在页面中使用时应该被视为有机体。如果使用像 Redux 这样的全局状态包，数据目录中的组件应该充当全局状态和表示之间的网关。

```
├── package.json
└── src
    ├── components
    │   ├── FormElements
    │   │   ├── Field
    │   │   │   ├── atoms
    │   │   │   │   ├── Error.jsx
    │   │   │   │   ├── index.js
    │   │   │   │   └── Label.jsx
    │   │   │   ├── Field.jsx
    │   │   │   └── index.js
    │   │   ├── Form
    │   │   │   ├── Form.jsx
    │   │   │   └── index.js
    │   │   ├── index.js
    │   │   └── Input
    │   │       ├── atoms
    │   │       │   ├── index.js
    │   │       │   ├── InputContainer.jsx
    │   │       │   └── InputItem.jsx
    │   │       ├── index.js
    │   │       └── Input.jsx
    │   └── Typography
    │       ├── Heading1.jsx
    │       └── index.js
    ├── data
    │   └── Login
    │       ├── index.js
    │       └── Login.jsx
    ├── pages
    │   └── LoginPage
    │       ├── index.js
    │       ├── LoginPage.jsx
    │       └── organisms
    │           ├── LoginForm
    │           └── LoginLoading
    │               ├── index.js
    │               └── LoginLoading.jsx
    └── templates
        └── Public
            ├── index.js
            └── Public.jsx 
```

Enter fullscreen mode Exit fullscreen mode

同样的想法将适用于三个小调整的独立包。

*   组件包将包括所有常见组件
*   登录包将包括登录页面和登录数据。
*   PublicLayout 包将包括公共布局。

通过遵循这些实践，我已经能够在没有重大重组的情况下扩展项目，并且保持了对项目目标的关注。开始时，开发会稍微慢一点，因为创建一个通用组件库需要时间。一旦每种常见情况都有一个组成部分，这个阶段就会加快很多。

我看到的这种结构的另一个很大的优点是测试变得容易多了，因为无状态组件的快照测试很简单。

您是否在使用 React 的同一种结构，或者在为应用程序寻找合适的结构时遇到了困难？请在评论中告诉我！

感谢阅读！