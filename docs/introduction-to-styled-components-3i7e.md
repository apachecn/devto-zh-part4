# 样式组件介绍

> 原文：<https://dev.to/ogwurujohnson/introduction-to-styled-components-3i7e>

本文将让我们直接介绍在对 React 组件进行样式化时使用样式化组件的概念和方法。

样式化的组件遵循 CSS-in-JS 的范例。要使用样式化组件，首先，我们必须将它安装并导入到 react 项目中，因为它没有预构建的 react。

我们普通的 react 应用程序如下所示:

```
 export default function Component() {
        return (
         <div>
            <p>Hello World </p>
         </div>
        )
     } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用样式化组件来样式化组件，而不是样式化组件，这是通过到处乱放类名来实现的。样式组件确保样式不会从一个组件泄漏到另一个组件。为了开始使用样式化组件，我们必须首先将它导入到我们的项目中

```
 import styled from 'styled-components'; 
```

Enter fullscreen mode Exit fullscreen mode

为了在上面的组件中设置 div 的样式，我们将执行以下操作

```
 const DivWrapper = styled.div`
      width: 50%;
      border: 2px solid black;
    `; 
```

Enter fullscreen mode Exit fullscreen mode

该变量以大写字母开始命名，因为它实际上是一个组件。现在我们必须用刚刚创建的`DivWrapper`替换我们的`div`。下面的代码演示了如何:

```
 export default function Component() {
     return (
      <DivWrapper>
        <p>Hello Styled component</p>
      </DivWrapper>
     )
     } 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，与`DivWrapper`相关联的样式被应用到我们的组件。

> 值得注意的是，样式组件不应该在类或函数组件中声明，而应该在两者的范围之外。

我们可以通过以下方式进一步设计`<p>`标签的样式:

```
 const Paragraph = styled.p`
       font-size: 32px;
     `; 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们可以用段落样式组件替换`<p>`标签，并应用样式。

> 样式化组件支持一些预处理器内置的语法，比如 Less 和 Sass，比如嵌套和 mixins 的创建。

就像任何其他 react 组件一样，styled-component 接收道具，并使开发人员能够就作为道具传递给 styled-component 的内容做出某些样式决定。为了说明这一点，假设我们的`DivWrapper`样式的包装器将被其他组件重用，那么我们就需要指定特定的样式准则，这些准则将根据开发人员的选择而有所不同，其中之一可能是 div 的背景颜色。我们如何用样式化组件做到这一点？

首先，我们需要向样式化组件传递一个颜色属性，如下所示:

```
 <DivWrapper
    color= 'blue'
  >

  </DivWrapper> 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们的样式组件声明中，我们将执行以下操作:

```
 const DivWrapper = styled.div`
    width: 50%;
    border: 2px solid black;
    ${props => (props.color === 'blue') ? `background-color: blue`: null}
    ${props => (props.color === 'red' ? `background-color: red`: null)}
  `; 
```

Enter fullscreen mode Exit fullscreen mode

样式化的组件可以互相继承，我们是怎么说的。一个样式化组件可以重用另一个样式化组件中的样式。举例来说，假设我们有另一个 div，但这一次它的背景颜色是红色，我们可以创建一个继承以前 div 样式组件`DivWrapper`所有属性的组件，然后添加它所依赖的其他样式，而不是创建一个完全不同的样式组件。下面是如何:

```
 const DivWrapper2 = styled(DivWrapper)`
        background-color: 'blue';
    `; 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用样式组件`DivWrapper2`时，它从`DivWrapper`组件继承样式、宽度和边框。

样式化组件非常适合样式化 React 组件。如果你想深入挖掘样式组件，请访问这里的官方网站。