# 第一天- JSX

> 原文：<https://dev.to/miralkumbhani/day-one-jsx-4n78>

这是我在这里的第一篇博文，作为一个开发者和学习者，我想保持它的简单。这将是我从头到尾学到的一系列 React 概念。对于专业人士来说，这可能是一些基本的东西，但我会确保新手发现它是有帮助的。这只是一种记录我的学习和分享，并在这方面做得更好的方式。

## JSX

JavaScript XML，简而言之，JSX 用于创建 React 元素。JSX 主要用于简化编码，因为它将 UI 逻辑和功能逻辑结合在一起。JSX 是 React.createElement 的替代者。它允许我们使用类似于 HTML 语法的语法来定义 React 元素。

例如，

```
const simpleText = "Hello World!";
const element = <h1>{ simpleText }</h1> 
```

第一行是简单的 JavaScript，将变量 simpleText 定义为字符串。第二行是 JSX，它正在创建一个元素，我们可以直接渲染变量`element`，如下所示。

```
ReactDOM.render(
    element,
    document.getElementById('root')
); 
```

编译后，JSX 表达式也会变成 JavaScript 函数调用，并对 JavaScript 对象求值。巴别尔把 JSX 的电话号码减少到了 100 个。因此，我们可以很容易地在`if...else`、`for`循环中使用 JSX，将它赋给变量，接受它作为参数，并将其作为函数返回。

我们可以使用花括号将 JavaScript 表达式嵌入到属性中。使用 JSX 的另一个好处是，它可以防止注入攻击。

JSX 可以比作下面的语法:

`React.createElement(component, props, ...children)`

第一部分(组件)确定 React 元素的类型。
一些要点是:

1.  大写类型表示 JSX 指的是组件。`React.createElement(<Foo />, document.getElementById('root');`
2.  如果单个模块包含几个 react 组件，则可以使用点符号来指代 React 组件。`<MyComponents.DatePicker />`
3.  React 库必须总是可访问的，并且在 JSX 代码的范围内。`import React from 'react;`
4.  用户定义的组件必须大写。`<Hello toWhat="World" />`
5.  当您想要基于属性渲染不同的组件，或者可能基于特定值或条件动态渲染组件时，JSX 类型可以定义如下:

```
function Story(props) {
    const SpecificStory = component[props.storyType];
    return <SpecificStory story={props.story} />; } 
```

这些是关于 JSX 的一些基本情况。对于我的下一个主题，我将涵盖一些先进的 JSX 概念和渲染元素。