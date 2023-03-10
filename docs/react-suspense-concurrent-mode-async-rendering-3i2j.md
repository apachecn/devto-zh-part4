# 反应暂停和并发模式:异步渲染

> 原文：<https://dev.to/apium_hub/react-suspense-concurrent-mode-async-rendering-3i2j>

去年 6 月 12 日，一些 Apiumhub 的前端开发者有机会参加在以色列特拉维夫举行的 **React Next 2019** 。这是一次不可思议的经历，许多有趣的谈话鼓励我们继续研究不同的概念。有一个演讲特别引起了我的注意: [Liad Yosef](https://twitter.com/liadyosef) 的“**现代反应——要点**”。Liad 经历了所有大的 React 创新，比如钩子、门户和异步渲染。我们已经在[丹·阿布拉莫夫](https://twitter.com/dan_abramov)在 [JSConf 冰岛 2018](https://www.youtube.com/watch?v=nLF0n9SACd4) 期间做的演示中第一次看到了这最后一个概念，他使用了两个**悬念** & **并发模式**的实际例子(在视频中，时间切片)。

这两个功能的目标是为使用 React 构建的应用程序的任何人提供更好的用户体验。重要的是要明白，最终用户遇到的大多数问题可以分为两类:**连接速度**(或服务器响应)和**计算能力**。

### 反应出悬念

(从 React 16.6.0 开始提供)

面向解决与第一组相关的问题，悬念允许我们停止一个组件的渲染，并在其位置显示另一个组件作为*后备*，就像加载组件、占位符或我们可能需要的任何其他组件。

这意味着在异步加载数据或依赖项时停止呈现。你可能会用 Redux 保存一些*加载*标志。悬念会让你换掉它。

要使用这个功能，我们需要两件东西: **React.lazy()** y **。**

 **React.lazy 允许动态导入作为普通组件呈现。它接收一个动态调用导入的函数。这将返回一个承诺，一旦得到解决，它将成为 React 组件。

```
 const LazyComponent = React.lazy(() => import("./some-component")); 
```

在我们的示例中，为了能够在 LazyComponent 加载时显示某些内容，我们必须将它包含在悬念中，就像这样:

```
 const LazyComponent = React.lazy(() => import("./some-component"));

 function ParentComponent() {

    return (

        <React.Suspense fallback={<div>Loading...</div>}>

            <div>

                <LazyComponent />

            </div>

        </React.Suspense>

    );

} 
```

这让我们有了退路。fallback 属性接收任何 React 组件。

通过**悬念**，React 可以在任何时候暂停状态更新，直到满足子组件中所有必要的数据请求。在等待期间，React 可以管理其他优先级更高的更新。这有助于改善用户体验，因为用户界面不会“冻结”。

### 反应并发模式

(尚未推出，但根据路线图，将于 2019 年推出)

设计用于在渲染过于强烈并对用户设备造成损害的情况下改善用户体验，*并发模式*允许 React 在组件(或组件树)渲染太慢或太重时停止渲染，处理其他更相关的事件，然后返回来渲染有问题的部分。换句话说，它允许在不阻塞主线程的情况下呈现组件树。

尽管这是我们想要的，但是我们必须等到我们可以在项目中使用它的时候。事实上，它反映在 API，因为今天这是*不稳定*很能说明问题:

```
 <React.unstable_ConcurrentMode>

    <SomeComponent />

</React.unstable_ConcurrentMode> 
```

当我们等到最终探索这个功能时，不要忘记最近推出的另一个大的新奇事物，钩子，我们已经在最近的文章中谈到过[。](https://apiumhub.com/tech-blog-barcelona/react-next-react-hooks/)

帖子 [React 悬疑&并发模式:异步渲染](https://apiumhub.com/tech-blog-barcelona/react-suspense-concurrent-mode-async-rendering/)最早出现在 [Apiumhub](https://apiumhub.com) 上。**