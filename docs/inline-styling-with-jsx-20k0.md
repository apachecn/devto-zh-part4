# 与 JSX 的内联风格

> 原文：<https://dev.to/ceceliacreates/inline-styling-with-jsx-20k0>

如果您是 React 新手，您可能已经遇到过 JSX，它是框架使用的 Javascript 的语法扩展。它非常类似于 HTML，如果您已经在使用 HTML、CSS 和 Javascript 创建 web 应用程序，则可以轻松转换。就像 HTML 一样，JSX 允许内嵌样式。我在这里不是要争论是支持还是反对内联样式还是外部或内部样式表，但是如果您确实需要对 JSX 元素使用内联样式，那么需要记住一些事情，以确保您的组件按照预期呈现。

下面是一个由简单函数组件呈现的 JSX 表达式的例子:

```
function HelloHeader () {
    return <h1>Hello World!</h1> } 
```

Enter fullscreen mode Exit fullscreen mode

JSX 表达式在 return 关键字之后，包括 h1 标记。如果你认为这看起来和 HTML 一样，那你就对了！HTML 和 JSX 之间有一些不同，但在大多数情况下，如果你对 HTML 感到舒适，你会得到很好的照顾。

假设您想要对这个组件进行样式化，由于这里没有解释的原因，您想要使用内联样式化。下面是用 16px 的字体大小和红色来设置 h1 样式的语法。

```
function HelloHeader() {
    return <h1 style={{color: "red", fontSize: "16px"}}>Hello World!</h1> } 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到了一些事情:

*   style 属性的值包含在一组花括号{}中。这就是在 JSX 传递 Javascript 表达式的方式。
*   里面有第二组花括号，表示包含 CSS 属性和值的对象。
*   CSS 属性 font-size 被键入为 fontSize。连字符与 JSX 不兼容，所以任何带有连字符的 CSS 属性都必须转换成 camelCase 才能工作。
*   属性值用引号括起来。虽然在 CSS 样式表中不一定需要这样做，但在大多数情况下，我们确实需要将值作为字符串传递。如果您正在传递一个数值，比如宽度或边距，它将默认为像素，所以如果您不希望这样，您将需要传递一个类似“40%”的字符串。

只要你记住这些事情，在 JSX 设计你的组件就不会有任何问题！

如果您决定使用外部样式表，React 中的 webpack 允许您将样式表直接导入到。定义组件的 js 文件！

像这样:

```
 import "../styles/style.css"; 
```

Enter fullscreen mode Exit fullscreen mode

如果您有任何问题或更正，请告诉我，感谢您的阅读！