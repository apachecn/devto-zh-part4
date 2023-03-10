# React 道具到底是什么？

> 原文：<https://dev.to/jasterix/what-exactly-are-react-props-f91>

在深入研究 React 之前，我强迫自己花时间去理解最基本的 React 概念。这篇简短的博客文章将试图以一种编程初学者能够理解的方式清晰地总结出 **props** 的概念。

那么什么是道具呢？

#### 简单地说:

props(properties 的缩写)是传递给 React 组件的参数，该组件随后呈现 React 元素。

道具可以是字符串、对象、数组或函数。道具被传入 React 组件

```
 class Hello extends Component {
  render() {
    return <h1>Hi, {this.props.name}</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，对吧？

默认情况下，“props”可以通过组件的关键字`this`获得。它们还可以在组件之间传递，通常是从父组件传递到子组件。

查看这篇博文，了解一些不同的道具示例。