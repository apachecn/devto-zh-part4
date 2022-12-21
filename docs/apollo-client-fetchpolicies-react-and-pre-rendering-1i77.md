# Apollo 客户端提取策略、反应和预渲染

> 原文：<https://dev.to/hotgazpacho/apollo-client-fetchpolicies-react-and-pre-rendering-1i77>

## 背景

我在 [FireEye](https://www.fireeye.com/) 的团队用 graph QL*(Apollo Server Lambda)*后端构建了 [FireEye Market](https://fireeye.market/) 作为 React 应用。这是一个市场，*“发现与您的 FireEye 体验集成并扩展它的应用、扩展和附加组件。”*我们早期发现的一件事是，我们需要努力改进第一次绘制的[时间](https://developers.google.com/web/tools/lighthouse/audits/first-meaningful-paint) *(TTFMP)* 。我们真的不能进一步减少我们的包大小，而且我们已经做了代码分割。因此，我们转而生成静态 HTML，将 Apollo 客户端缓存数据序列化到标记中。这允许客户端快速下载完整呈现的 HTML 页面，立即开始与交互，同时 React 应用程序脚本由浏览器下载和评估。当 React 应用程序[合并](https://reactjs.org/docs/react-dom.html#hydrate)时，它已经被配置为将序列化数据读入 Apollo 客户端缓存，这样 React 应用程序就可以立即使用这些数据来更新组件树。然而，有一个问题...

## 输入`fetchPolicy`

阿波罗客户端，以及消费客户端的对应 React 组件 *( `Query`、`Mutation`、`Subscription`和`graphql`HOC)*，有一个选项叫做`fetchPolicy`。这样做的目的是控制组件如何与 Apollo 客户机缓存交互。这非常强大，但是它的文档分散在 Apollo 文档的几个地方。我在这里的目的是巩固这一信息，并希望澄清一点。

`cachePolicy`的有效值为:

### `cache-first`

如果没有明确指定选项，这是默认设置。这意味着客户端将查看其缓存，如果它找到了完成查询所需的所有数据，它将使用这些数据，并且 T2 不会对数据发出网络请求。您所做的每个查询以及参数都存储在缓存中。如果查询被缓存，那么它将使用来自该查询的数据。*我相信*也考虑了查询的选择集，所以如果不同，将会发出一个网络请求*。*

 *无可否认，我对最后一点没有把握。FireEye Market 应用程序有一组已知的客户端执行的查询，不同之处仅在于运行时传递的参数。

### `cache-and-network`

该策略将首先在缓存中查找，并使用可用的数据。它将**总是发出网络请求**，更新缓存并在可用时返回新数据。当新数据进来时，这可能会导致组件的额外更新。该策略优化了客户端快速获取缓存数据的能力，同时也确保了总是获取新数据。

在处理预渲染时，这是我们发现在大多数情况下最有效的策略。

### `network-only`

该策略完全跳过从缓存中读取数据，直接从网络中获取数据。使用该选项的查询将**永远不会从缓存**中读取。但是，它会将结果写入缓存。这适用于您总是想去后端获取数据，并且愿意在响应时间上为此付费的情况。

### `cache-only`

该策略*独占*从缓存中读取，并且**永远不会访问网络**。如果数据不存在于缓存中，那么就会抛出一个错误。这对于希望客户端仅在脱机模式下运行的情况非常有用，在这种情况下，所有数据都存在于客户端上。

我自己从未使用过这种策略，所以对这种说法要有所保留。

### `no-cache`

该策略永远不会从缓存中读取数据，也不会向缓存中写入数据。

## 配置

有了这些关于`fetchPolicy`的知识，你如何配置它呢？有两个地方:客户端配置和请求配置。

### 客户端配置

当您[配置 Apollo 客户机实例](https://www.apollographql.com/docs/react/api/apollo-client/)时，您可以为它提供一个`defaultOptions`键，它指定每种类型的查询应该使用的策略，除非请求特别提供。

```
const defaultOptions = {
  watchQuery: {
    fetchPolicy: 'cache-and-network',
    errorPolicy: 'ignore',
  },
  query: {
    fetchPolicy: 'network-only',
    errorPolicy: 'all',
  },
  mutate: {
    errorPolicy: 'all'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*从文档:*

> **注意:**React Apollo`<Query />`组件使用了 Apollo 客户端的`watchQuery`功能，所以如果你想在使用`<Query />`时设置`defaultOptions`，一定要在 defaultOptions.watchQuery 属性下设置。

还要注意，`graphql` HOC 给出了一个文档为`query`，最终包装了一个`<Query />`组件的实例。

### 请求配置

您还可以为每个请求指定`fetchPolicy`。您可以向`<Query />`组件提供的道具之一是`fetchPolicy`。这将覆盖客户机中仅为该查询配置的任何内容。

```
<Query query={QUERY_DOCUMENT} fetchPolicy="network-only">
  {(data) => { /* render prop! */ }}
</Query> 
```

Enter fullscreen mode Exit fullscreen mode

同样，对于`graphql` HOC，您可以在配置对象中指定一个`fetchPolicy`:

```
const listAppsForNotificatonSettings = graphql(APPS_FOR_NOTIFICATION_SETTINGS_QUERY, {
  options: {
    fetchPolicy: 'cache-first' 
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

正如我提到的，我们发现这个`cache-and-network`策略在为应用程序的各种入口点提供预渲染页面时，最终成为为我们的客户提供最佳体验的最佳选择。在少数情况下，我们发现使用`cache-first`是一个更好的选择，但是这种情况很少。一如既往，这是对我的团队有效的方法。您的里程可能会有所不同。*