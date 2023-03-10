# 使用 Effect()的 4 种方法

> 原文：<https://dev.to/spukas/4-ways-to-useeffect-pf6>

还有一篇文章是关于最常用的 React 钩子之一的。只是我不会详细说明它是如何工作的，而是写一些例子和模式来快速提醒如何以及何时使用它。

## 这是什么？

它是一个钩子，可以让函数组件产生副作用。它将一个函数作为第一个参数，将一个依赖数组作为第二个参数:

```
React.useEffect(fn, [deps])` 
```

它在渲染中被调用，如下所示:

*   用当前状态准备用户界面
*   渲染结果，即`<div>Hello World!</div>`
*   将结果提交到 DOM
*   浏览器绘制屏幕
*   React 调用 useEffect()

在渲染周期的最后一个阶段，`useEffect()`被调用，并带有该调用的状态、处理程序和效果。因此，每个渲染都有其特定的属性，这些属性永远不会改变，但 React 将始终应用最后的渲染结果。

## 何时如何使用

它与类组件生命周期方法略有不同。主要区别在于，lifecycle 方法总是拥有对最新状态的引用，而`useEffect()`将兑现每个渲染的状态、处理程序和效果，并且与下一个不同。
但好的一面是，您可以通过指定依赖列表或无来操纵何时调用`useEffect()`中的函数。

我想到了调用该方法的 4 种可能方式:

*   一次，当组件安装时
*   在每个组件渲染上
*   在每个组件上呈现一个条件
*   当组件卸载时

### 当组件挂载时

通常，您会希望使用它来获取数据或添加事件侦听器。
要运行该函数一次，添加一个空的依赖列表。如果其中没有依赖项，这意味着它将一直保持不变，不会再次调用该函数。

```
function MyComponent() {
    // ...
    React.useEffect(() => {
        callMeOnlyOnce()
    }, [])
    // ...
} 
```

### 对每个组件进行渲染

要在每个组件渲染上调用该函数，请跳过添加依赖关系列表。没有列表，没有可以比较的，这意味着每次都要运行效果。

```
function MyComponent() {
    // ...
    React.useEffect(() => {
        runThisFunctionOnEveryRender();
    })
    // ...
} 
```

### 在每个组件上渲染一个条件

要有条件地调用函数，请指定依赖项列表。
经验法则是总是添加那些你在`useEffect()`中使用的依赖项。

```
function MyComponent() {
    // ...
    React.useEffect(() => {
        runIfOneOfTheDepsWillChange(dep1, dep2);
    }, [dep1, dep2])
    // ...
} 
```

### 组件卸载时

为了在组件卸载后进行清理(删除事件监听器或停止数据获取，但会产生副作用),应该在`useEffect()`钩子内添加一个带有函数的 return 语句。

```
function MyComponent() {
    // ...
    React.useEffect(() => {
        document.addEventListener();

        return () => {
            document.removeEventListener();
        }
    }, [])
    // ...
} 
```

## 带走

从这篇文章中得到的启示是，总是要指定您在效果中使用的依赖项。它将阻止无条件运行效果，这可能会导致无限循环和性能问题。