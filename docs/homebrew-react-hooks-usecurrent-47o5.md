# 自制反应钩:使用当前

> 原文：<https://dev.to/n1ru4l/homebrew-react-hooks-usecurrent-47o5>

你可能听说过 React 悬念。

2018 年，丹·阿布拉莫夫展示了这项尚未发布的新功能。作为演讲的一部分，他展示了悬念如何让数据获取变得更容易。

我真的被他关于加载指示器以及移除指示器如何带来更好的用户体验的话迷住了。

你们中的一些人可能会想:**为什么我要向我的用户显示一个空白页面？**

嗯，他实际上的意思是不完全删除它们，而是只在必要的时候显示它们。

假设您有一个只需要 100 毫秒的请求。您将在很短的时间内显示一个加载微调器。用户可能甚至没有足够的时间来意识到他刚刚进入加载状态。他可能只是注意到页面在闪烁。

另一方面，如果您根本没有加载指示器，并且请求花费了几秒钟(例如，由于糟糕的互联网连接)，而用户看到的只是一个空白页面，情况会变得更糟。

这种“恶作剧”行为会让用户感到困惑，让他认为页面有问题或坏了，在最坏的情况下，他甚至会生气(我们都知道，只有快乐的用户才是忠诚的客户)。

为了提供最佳的用户体验，我们需要同时解决这两个问题！

所以有两个大问题需要回答，第一个是:

## 当对新内容的请求仍未决时，我们呈现什么？

正确！我们渲染**旧内容** ™️

当然，如果我们没有任何旧的内容，我们仍然需要显示一个加载微调。

下面是第二个问题:

## 当对新内容的请求花费很长时间时，我们呈现什么？

是啊！我们展示了一个装载旋转器。

虽然悬念可能是未来解决这个问题的银弹，但我问自己:**我们今天能用钩子实现同样的用户体验吗？**

T5】

### 让我们看看现有的数据提取库

在下面的例子中，我将使用一个钩子来模拟数据获取部分的网络请求。它在内部使用`setTimeout`。

```
const {data, loading} = useFakeFetch("/endpoint") 
```

Enter fullscreen mode Exit fullscreen mode

以前使用过`react-apollo-hooks`或`react-fetch-hook`的人应该对代码很熟悉。

几乎所有这些数据获取钩子的工作方式都是一样的，一旦钩子参数发生变化，就会用新的参数重新获取数据。

尝试在以下示例中点击`change endpoint`按钮。

[https://codesandbox.io/embed/use-current-example-1-tqupx](https://codesandbox.io/embed/use-current-example-1-tqupx)

**您是否注意到点击按钮后数据立即变为空？**

这正是我们想要改变的行为！

那么我们从哪里开始呢？

当然，我们可以改变现有的数据获取挂钩。

然而，我关于钩子的第一条规则是**钩子的力量在于多个钩子的可组合性**。

因此，我们现在将一步一步地构建一个新的钩子，与每个返回`data`和`loading`值的数据获取钩子兼容，而不是改变现有的钩子。

所以让我们从存储旧的`data`开始，直到新的`data`可用。

**的完美用例为`useState`结合`useEffect`**

```
const {data, loading} = useFakeFetch("/endpoint")
const [currentData, setCurrentData] = React.useState(data);

React.useEffect(() => {
 // condition for updating the stored data
 if (loading === false && data !== currentData) {
   setCurrentData(data)
 }
}, [loading, data, setCurrentData, currentData]);

// use currentData instead of data 
```

Enter fullscreen mode Exit fullscreen mode

看看下面的例子:

[https://codesandbox.io/embed/use-current-example-2-v8nnt](https://codesandbox.io/embed/use-current-example-2-v8nnt)

您注意到新值`currentData`了吗？它现在保存旧值，直到获取新数据为止。

问题已经解决了一半！

在下一步中，我们将确保只有在超过某个阈值(也就是请求花费的时间比预期的长)后才显示负载指示器。

**重新介绍我们的老朋友`setTimeout``clearTimeout`**

```
const {data, loading} = useFakeFetch(endpoint);
const [currentData, setCurrentData] = React.useState(data);

React.useEffect(() => {
  if (loading === false && data !== currentData) {
    setCurrentData(data);
  }
}, [loading, data, setCurrentData, currentData]);

// NEW STUFF STARTS HERE
const previousLoadingRef = React.useRef(loading);
const [
  shouldShowLoadingIndicator,
  setShouldShowLoadingIndicator
] = React.useState(loading);

React.useEffect(() => {
  let timeout = undefined;
  // only update in case loading has changed between renders
  if (previousLoadingRef.current !== loading) {
    if (loading) {
      // should show loading indicator if request time
      // exceeds one second
      timeout = setTimeout(() => {
        setShouldShowLoadingIndicator(true);
      }, 1000);
    } else {
      setShouldShowLoadingIndicator(false);
    }
  }
  previousLoadingRef.current = loading;

  // cancel the timeout in case the data is available 
  // before one second has passed
  return () => timeout && clearTimeout(timeout);
}, [loading, setShouldShowLoadingIndicator]);

// use currentData instead of data
// use shouldShowLoadingIndicator instead of loading 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们现在有一个很长的请求，一秒钟后我们想显示一个加载指示器！

[https://codesandbox.io/embed/use-current-example-3-iv6yu](https://codesandbox.io/embed/use-current-example-3-iv6yu)

快好了！现在我们有了一个概念实现的工作证明。让我们将代码转换成一个可重用的钩子:

```
const useCurrent = (data, loading, showLoadingIndicatorThereshold = 300) => {
  const [currentData, setCurrentData] = React.useState(data);
  const previousLoadingRef = React.useRef(loading);
  const [
    shouldShowLoadingIndicator,
    setShouldShowLoadingIndicator
  ] = React.useState(loading);

  React.useEffect(() => {
    if (loading === false && data !== currentData) {
      setCurrentData(data);
    }
  }, [loading, data, setCurrentData, currentData]);

  React.useEffect(() => {
    let timeout = undefined;
    if (previousLoadingRef.current !== loading) {
      if (loading) {
        timeout = setTimeout(() => {
          setShouldShowLoadingIndicator(true);
        }, showLoadingIndicatorThereshold);
      } else {
        setShouldShowLoadingIndicator(false);
      }
    }
    previousLoadingRef.current = loading;
    return () => timeout && clearTimeout(timeout);
  }, [loading, setShouldShowLoadingIndicator, showLoadingIndicatorThereshold]);

  return [shouldShowLoadingIndicator, currentData];
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个用法示例

```
const { data, loading } = useFakeFetch(endpoint);
const [shouldShowLoadingIndicator, currentData] = useCurrent(
  data,
  loading,
  300
); 
```

Enter fullscreen mode Exit fullscreen mode

当然，还有一个活生生的例子

[https://codesandbox.io/embed/use-current-example-4-uq6t5](https://codesandbox.io/embed/use-current-example-4-uq6t5)

我们完成了！这个挂钩现在正式与以下库兼容:

*   `react-apollo`(使用专用 API)
*   `react-fetch-hook`
*   `react-apollo-hooks`
*   *T2`insert every hook data fetching library here`*

让我们用这个钩子让网络成为一个更好的地方，直到 React 悬念最终登陆！

## 奖励:减少重新提交者的数量(可能的性能优化)

甚至还可能减少再呈现器的数量。

打开以下示例的控制台，直到加载初始数据，然后单击按钮`Change endpoint`一次。

[https://codesandbox.io/embed/use-current-example-4-uq6t5](https://codesandbox.io/embed/use-current-example-4-uq6t5)

最后输出的应该是`render 8`。这意味着在整个过程中出现了 8 次渲染。我们可以将该过程所需的渲染总数减少到 6 个，减少 2 个重新渲染程序。

在我们优化这个例子之前，我想提一下，我们可能不需要做这样的优化。然而，当我们有一个大的组件树并且在它的顶部使用这个钩子时，你可能会感觉到两个额外的重新呈现器的影响！

先说`currentData`状态。我们不需要一个真正的`useState`挂钩来存放它。这是因为每次`data`或`shouldShowLoadingIndicator`改变时，一个重新渲染已经被触发。

因此，我们可以使用`useRef`来存储`currentData`。因此，我们也可以腾出`useEffect`来更新`currentData`。

此外，`shouldShowLoadingIndicator`仅在两种情况下改变:

*   未取消超时(超过了请求持续时间)
*   在`useEffect`期间，当`loading`已经改变并且现在为假时

后者实际上是不必要的，我们也可以将`shouldShowLoadingIndicatorR`重构为`useRef`。那是因为当`loading`为假时`shouldShowLoadingIndicator`自动为假，这意味着我们可以在更新`currentData`的同时更新`shouldShowLoadingIndicator`。

但是我们如何将`shouldShowLoadingIndicator`更新为`true`？变异一个引用不会触发任何重新渲染。解决方案是引入一个新的状态，其唯一目的是触发一个重新呈现器。

我们来看看优化后的版本:

```
const useCurrent = (data, loading, showLoadingIndicatorThereshold = 300) => {
  const currentDataRef = React.useRef(data);
  const previousLoadingRef = React.useRef(loading);
  const shouldShowLoadingIndicatorRef = React.useRef(loading);
  const [, triggerStateUpdate] = React.useState(0);

  // those values should always reflect the input when loading is false 
  if (!loading) {
    currentDataRef.current = data;
    shouldShowLoadingIndicatorRef.current = false;
  }

  React.useEffect(() => {
    let timeout = undefined;
    // only when loading has changed from false to true
    if (previousLoadingRef.current !== loading && loading) {
      timeout = setTimeout(() => {
        // mutate ref
        shouldShowLoadingIndicatorRef.current = true;
        // trigger rerender
        triggerStateUpdate(i => i + 1);
      }, showLoadingIndicatorThereshold);
    }
    previousLoadingRef.current = loading;
    return () => timeout && clearTimeout(timeout);
  }, [loading, triggerStateUpdate, showLoadingIndicatorThereshold]);

  return [shouldShowLoadingIndicatorRef.current, currentDataRef.current];
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样，在下面的例子中打开控制台！等到初始加载完成后，单击按钮并观察控制台日志。

[https://codesandbox.io/embed/use-current-example-5-41sbh](https://codesandbox.io/embed/use-current-example-5-41sbh)

我们已经成功删除了两个不必要的重新渲染程序！

我希望你喜欢阅读这篇博文！

我要感谢@mfpiccolo 和@sseraphini 帮助我校对了这篇文章！

这只是许多关于实用程序挂钩和其他主题(包括 react 和 GraphQL)的博客文章中的第一篇。

请在 DEV 或 [Twitter](http://twitter.com/n1rual) 和 [Github](http://github.com/n1ru4l) 上关注我。