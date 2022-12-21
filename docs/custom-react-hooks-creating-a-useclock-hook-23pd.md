# 自定义 React 挂钩:创建一个 useClock 挂钩

> 原文：<https://dev.to/chriswcs/custom-react-hooks-creating-a-useclock-hook-23pd>

自定义钩子是 React 的新钩子 api 的一个强大部分。在这篇文章中，我将给出一个例子来展示如何构建一个定制的钩子，以及什么时候你可能想要这样做。

## 自定义钩子的规则

首先，我想列举一些钩子的规则。通常定制钩子的名字以 use 开头，比如 useState、useReducer 等等。我认为最好遵循这个模式，尽管我不确定它必须从使用开始。其他规则如下:

*   钩子可以从 React 函数组件内部调用
*   钩子可以从自定义钩子中调用
*   钩子不能在条件逻辑、循环等内部调用。每次都必须以相同的顺序调用它们。

## 使用定制挂钩的原因

随着钩子的发布，React 功能组件现在可以具有状态和生命周期效果。这允许我们共享代码和创建新的抽象。您可能出于不同的原因想要创建自定义挂钩。

当组件之间存在重复时，是使用定制钩子的好时机。当您注意到它时，您可以提取出一个自定义挂钩，并在两个地方使用它。也许使用定制钩子的另一个好时机是当一个组件有复杂的逻辑时，将它提取到一个有好名字的钩子中会有助于使代码更干净和清晰。

## 时钟组件

在下面的例子中，我有一个显示当前时间的组件的基本例子。我们将提取状态和效果，这样我们可以在很多地方显示当前时间。在钩子出现之前，你可能不得不使用更高阶的类或者渲染道具模式。Hooks 允许我们在没有大量嵌套 div 和组件的情况下共享这些代码。

在我们把我们需要的东西提取到我们的定制钩子之前，我想回顾一些事情。当我们调用 useState 时，它接受初始状态并返回一个由两个东西组成的数组，我们可以随意命名。第一个是对状态的引用，第二个是将更新状态的函数。useEffect 挂钩将在组件挂载时运行，因为我们提供了空数组。当它卸载时，它将运行我从函数 useEffect 返回的函数，这将清除间隔。

```
const Clock = () => {
  const [clock, setClock] = useState(new Date().toLocaleTimeString());

  useEffect(() => {
    let id = setInterval(() => {
      setClock(new Date().toLocaleTimeString());
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <h1>{clock}</h1>; } 
```

下面我摘录了设置状态和效果。然后我返回时钟状态。只要使用了 useClock，它就会运行这些钩子并返回时钟状态。

```
const useClock = () => {
  const [clock, setClock] = useState(new Date().toLocaleTimeString());

  useEffect(() => {
    let id = setInterval(() => {
      setClock(new Date().toLocaleTimeString());
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return clock;
}

const Clock = () => {
  const clock = useClock();
  return <h1>{clock}</h1>; } 
```

## 最后的想法

钩子是清理 React 代码的一个很好的方法。它们是 React api 的一个强大的新部分。当你开始使用钩子时，寻找可以从你的功能组件中提取的定制钩子，从你的代码库中移除重复。