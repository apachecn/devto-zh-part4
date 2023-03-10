# 为什么我们编写了另一个状态管理工具

> 原文：<https://dev.to/adamklein/why-we-wrote-yet-another-state-management-tool-el3>

## redux vs mobx？

大多数当前的状态管理解决方案不允许您使用钩子来管理状态，这导致您以不同的方式管理本地和全局状态，并且在两者之间有一个昂贵的转换。

这促使我们寻找使用 React 钩子的解决方案。问题是钩子只在 React 组件内部运行。

## 语境呢？！

使用简单的 React 上下文并不是状态管理的最佳解决方案:

*   在大型应用程序中使用上下文管理全局状态时，您可能会有许多小型的、单一用途的提供者。很快你就会发现供应商包装地狱。
*   当您对提供者进行垂直排序时，您不能在不改变顺序的情况下动态地选择相互依赖，这可能会破坏东西。
*   上下文不支持选择器、渲染紧急援助或去抖动

## 我们的方针

为了进行全局状态管理，我们需要一个顶级的提供者。我们希望开发者使用钩子来管理不可变的状态。出于性能原因，我们还想考虑选择器和渲染紧急援助。最后，我们希望确保没有初始化代码，以便使用状态管理的包可以轻松地插入到您的应用程序中。

一遍又一遍地迭代 API 让我们找到了一个我们认为简单而强大的解决方案。我们称这个库为可重用库。下面是 API:

### 提供者:

```
const App = () => (
  <ReusableProvider>
    ...
  </ReusableProvider>
) 
```

### 定义一个门店:

```
const useCounter = createStore(() => {
  const [counter, setCounter] = useState(0);

  return {
    counter,
    increment: () => setCounter(prev => prev + 1)
  }
}); 
```

### 订阅商店:

```
const Comp1 = () => {
  const something = useCounter();
}

const Comp2 = () => {
  const something = useCounter(); // same something
} 
```

### 使用选择器:

```
const Comp1 = () => {
  const isPositive = useCounter(state => state.counter > 0);
   // Will only re-render if switching between positive and negative
} 
```

## 了解更多

要了解更多信息并开始尝试可重复使用，请访问回购:

【https://github.com/reusablejs/reusable】T2

你也可以看看 ReactNext 的视频，我和 Maayan Glikser 展示了这个库:
[https://www.youtube.com/embed/oy-6urveWzo](https://www.youtube.com/embed/oy-6urveWzo)