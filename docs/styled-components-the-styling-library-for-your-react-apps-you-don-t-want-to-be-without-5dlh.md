# 样式组件，你不想失去的 React 应用程序的样式库

> 原文：<https://dev.to/itnext/styled-components-the-styling-library-for-your-react-apps-you-don-t-want-to-be-without-5dlh>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 这涵盖了样式组件 3.0。从那以后发生了一些变化。值得一读的是发行说明中的主要变化，[发行说明](https://www.styled-components.com/releases)

注意，我知道降价渲染看起来有点不对劲。如果这让你很困扰，你可以在这里阅读原文[原文](https://dev.to/itnext/styled-components-the-styling-library-for-your-react-apps-you-don-t-want-to-be-without-5dlh)

> 在 React 中，有很多方法可以设计组件的样式。这可能是我最喜欢的方式，它在概念上不同于其他风格。

## 样式化组件方法的案例

当你开始设计你的 React 组件时，你可以选择定义 CSS 类并将它们分配给每个元素，就像这样:

```
<div className="card">
  <div className="card-header">
    <h3>{card.header}</h3>
  </div>
  <div className="content">{card.content}</div>
</div> 
```

上面所说的真的没有错，这是一个可行的方法，尽管你们中的一些人可能认为这是一个重复输入单词`className`的过程。

在这一点上，您可能会认为我可以分别为卡片、卡片标题和卡片内容创建一个组件。是的，我们能做到。那么它可能看起来像这样:

```
<card header={card.header}>
  {card.content}
</card> 
```

现在，这个例子可能有点琐碎，制作这样一个组件可能是小菜一碟。此外，我们都知道在 React 中创建组件是多么容易。

所以你需要问自己的是，当我想做的只是添加一些 CSS 样式并随意命名我的 HTML 元素时，我真的需要一个组件吗？如果这是你想去的地方，那么图书馆也许适合你？

## 安装和设置样式化组件

我们可以简单地通过 NPM 安装样式组件，就像这样:

```
yarn add styled-components
// OR
npm install — save styled-components 
```

在这之后，我们准备在我们的 React 项目中使用它。

## 我们的第一次造型

这个库的主页使用的例子是按钮。你可能最终会在你的应用程序中创建不同的按钮来实现不同的目的。您可能有默认按钮、主按钮、禁用按钮等等。样式组件库使您能够将所有 CSS 保存在一个地方。让我们从导入它开始:

```
import styled from ‘styled-components’; 
```

现在要使用它，我们需要使用双反斜杠`` ` ``，就像这样:

```
const Button = styled.button``; 
```

在这一点上，我们没有任何东西可以显示语法。

正如我们在上面看到的，我们称之为

```
styled.nameOfElement`` 
```

为我们的元素定义一个样式。让我们给它添加一些样式:

```
const Button = styled.button`
  background: black;
  color: white;
  border-radius: 7px;
  padding: 20px;
  margin: 10px;
  font-size: 16px;
  :disabled {
    opacity: 0.4;
  }
  :hover {
    box-shadow: 0 0 10px yellow;
  }
`; 
```

从上面的例子中我们可以看到，我们能够将普通的 CSS 属性与类似于`:disabled`和`:hover`的伪选择器结合使用。

如果我们想将按钮作为 JSX 的一部分，我们可以简单地这样做，就像这样:

```
<div>
  <Button>
    press me
  </Button>
</div> 
```

我们可以将我们的`Button`与我们想要的所有 HTML 或 JSX 混合，我们可以像对待 HTML 元素`button`一样对待它，因为它是一个元素，只是添加了一些 CSS 样式。

### 使用属性

`styled-component`库可以通过在我们的元素上寻找指定属性的出现来有条件地应用样式。我们可以使用现有的属性，也可以使用我们创建的自定义属性。

下面我们有一个定义`primary`按钮的例子。相对于一个`normal`按钮，我们对`primary`意味着什么？在一个应用程序中，我们有各种各样的按钮，通常我们有一个默认按钮，但我们也有一个主按钮，这是页面上最重要的按钮，通常执行像保存表单这样的事情。

用不同的方式设计主按钮是很常见的情况，所以我们看到了这样一个按钮和`normal`按钮之间的区别，这样用户就能理解按下它的重要性。

现在让我们展示如何设计这样一个按钮，并用`styled-components`展示属性的用法。我们有自己的风格按钮，我们可以添加属性`primary`，就像这样:

```
<Button primary >I am a primary button</Button> 
```

我们的下一步是更新我们的`Button`定义，并为这个属性`primary`是否存在写一些条件逻辑。

我们可以用下面的结构来做到这一点:

```
${props => props.primary && css`
`} 
```

我们使用`${}`来表示我们正在运行一些条件逻辑，我们称之为`props`。`props`是一个简单的字典，包含了元素的所有属性。正如你在上面看到的，我们说`props.primary`是*真值*，它是在我们的属性字典中定义的。如果是这样的话，我们将应用 CSS 样式。通过我们对 css 函数的使用，我们可以从上面的代码中分辨出后者。

下面我们使用上面的构造添加一些样式，我们应该只在`primary`属性存在时应用:

```
const Button = styled.button`
  background: black;
  color: white;
  border-radius: 7px;
  padding: 20px;
  margin: 10px;
  font-size: 16px;
  :disabled {
    opacity: 0.4;
  }
  :hover {
    box-shadow: 0 0 10px yellow;
  }
  ${props => props.primary && css`
    background: green;
    color: white;
  `}
`; 
```

现在我们有了一个更完整的例子来说明如何测试特定属性的存在。只有一点需要注意，我们说过我们需要使用`css`函数。这是一个我们在`styled-components`名称空间中找到的函数，因此，我们可以通过更新我们的 import 语句来使用它，如下所示:

```
import styled, { css } from 'styled-components'; 
```

### 适应

我们已经展示了如何查看某些属性是否存在，但是我们也可以根据某个属性是否存在来设置不同的属性值。

让我们看看下面的代码，我们根据是否设置了一个圆形属性来改变`border-radius`:

```
const Button = styled.button`
  background: black;
  color: white;
  border-radius: 7px;
  padding: 20px;
  margin: 10px;
  font-size: 16px;
  :disabled {
    opacity: 0.4;
  }
  :hover {
    box-shadow: 0 0 10px yellow;
  }
  ${props => props.primary && css`
    background: green;
    color: white;
  `}
  border-radius: ${props => (props.round ? ‘50%’ : ‘7px’)}
`; 
```

这段代码有趣的一点是:

```
border-radius: ${props => (props.round ? ‘50%’ : ‘7px’)} 
```

我们可以通过如下方式声明按钮来触发上述代码的呈现:

```
<Button round >Round</Button> 
```

## 对现有组件进行样式化

这是一个伟大的第三方组件或你自己的组件。假设您有以下组件:

```
// Text.js

import React from 'react';
import PropTypes from 'prop-types';

const Text = ({ text }) => (
  <div> Here is text: {text}</div> );
Text.propTypes = {
  text: PropTypes.string,
  className: PropTypes.any,
};
export default Text; 
```

现在，要对这个函数进行样式化，我们需要以稍微不同的方式使用样式化的函数。而不是键入

```
styled`` 
```

我们需要像调用一个函数一样调用它，把组件作为一个参数，像这样:

```
const DecoratedText = styled(Text)`
  // define styles
`;
<DecoratedText text={“I am now decorated”} /> 
```

在组件中，我们需要将`className`作为`props`中的一个参数，并将其分配给我们的顶级 div，就像这样:

```
// Text.js
import React from ‘react’;
import PropTypes from ‘prop-types’;

const Text = ({ text, className }) => (
  <div className={className}> Here is text: {text}</div> );
Text.propTypes = {
  text: PropTypes.string,
  className: PropTypes.any,
};
export default Text; 
```

正如你在上面看到的，调用`styled()`函数意味着它在幕后产生一个`className`,它注入到我们需要设置为顶级元素的组件中，以使它生效。

### 继承

我们可以通过使用方法`extend`很容易地获取一个现有的样式并添加到它，就像这样:

```
const GiantButton = Button.extend`
  font-size: 48px;
`; 
```

## 改变样式组件

在某些情况下，您可能希望将样式应用于特定类型的元素，并将该样式应用于另一种类型的元素。一个常见的例子是按钮。您可能喜欢特定按钮附带的所有样式，但是您可能希望将其应用于锚元素。使用`withComponent()`方法就可以做到这一点:

```
const LinkButton = Button.withComponent('a'); 
```

以上操作的最终结果是一个锚，`a`标签带有一个按钮的所有样式。

请注意，如果您使用的是 3.0 或更低版本，`withComponent()`仍然是实现这一点的方法。从 4.0 开始，建议您使用`as`属性，比如:

```
const Component = styled.div`
  color: red;
`

// Examples
<Component>Hello world!</Component>
<Component as="span">Hello world!</Component>
<Component as={Link} to="home">Hello world!</Component> 
```

## 使用属性功能

有时候你所需要的只是改变组件样式中的一个小东西。为此，`attrs()`函数允许您添加带有值的属性。让我们来说明如何添加这个:

```
const FramedText = styled(Text).attrs({ title: ‘framed’ })`
  border: solid 2px black;
  color: blue;
  font-size: 16px;
  padding: 30px;
`; 
```

上面我们链接了`styled()`和`attrs()`，并以一个双`` ` ``记号结束。再比如:

```
const Button = styled.button.attrs({ title: 'titled' })`
  background: black;
  color: white;
  border-radius: 7px;
  padding: 20px;
  margin: 10px;
  font-size: 16px;
  :disabled {
    opacity: 0.4;
  }
  :hover {
    box-shadow: 0 0 10px yellow;
  } ${props => props.primary && css`
    background: green;
    color: white;
  `} border-radius: ${props => (props.round ? ‘50%’ : ‘7px’)} `; 
```

## 主题化

样式化组件导出一个`ThemeProvider`,它允许我们轻松地对样式化组件进行主题化。为了让它工作，我们需要做以下工作:

*   **导入**主题提供者
*   **设置**为 App 的根元素
*   **定义**一个主题
*   **引用主题中的属性**,并将其设置为所需的 CSS 属性

### 设置

在我们打算使用`Theme`的组件中，我们需要导入并声明一个`ThemeProvider`。现在，这可以是整个应用程序的根元素，也可以是您所在的组件。`ThemeProvider`将在所有组件内部或者从您添加的组件及其所有子组件中注入一个`theme`属性。我们来看看全球怎么加:

```
ReactDOM.render(
  <ThemeProvider theme={{ color: 'white', bgcolor: 'red' }}>
    <App />
  </ThemeProvider>,
  document.getElementById('root'),
); 
```

现在，我们准备相应地更改我们的组件，开始使用我们设定的主题。

让我们使用我们定义的按钮组件，并让它使用我们的主题，就像这样:

```
const Button = styled.button.attrs({ title: 'titled' })`
  background: ${props => props.theme.bgcolor};
  color: ${props => props.theme.color};
  border-radius: 7px;
  padding: 20px;
  margin: 10px;
  font-size: 16px;
  :disabled {
    opacity: 0.4;
  }
  :hover {
   box-shadow: 0 0 10px yellow;
  } ${props => props.primary && css`
    background: green;
    color: white;
  `} border-radius: ${props => (props.round ? '50%' : '7px')} `; 
```

让我们放大我们所做的:

```
background: ${props => props.theme.bgcolor};
color: ${props => props.theme.color}; 
```

如你所见，我们可以通过写`props.theme.[nameOfThemeProperty]`来访问主题属性。

### 主题为高阶组件工厂

如果我们想在组件内部使用主题，我们可以这样做，但是我们需要使用一个名为`withTheme()`的助手。它接受一个组件和主题属性，就像这样:

```
import { withTheme } from 'styled-components';

class TextComponent extends React.Component {
  render() {
    console.log('theme ', this.props.theme);
  }
}
export default withTheme(TextComponent); 
```

## 总结

我们通过使用`styled-components`库引入了一种样式化组件的新方法。

我们还了解到，当我们将组件的 DOM 声明与使用`className`并分配所述属性 CSS 类的经典方式进行比较时，我们得到了更具语义的 DOM 声明。

### 进一步阅读

官方文档提供了一些很好的例子来说明如何进一步积累你的知识[风格组件官方文档](https://www.styled-components.com/)

希望这已经使您相信这是一种样式化 React 组件的好方法。自从我发现了这个库，这就是我一直使用的，但那是我，你做你:)