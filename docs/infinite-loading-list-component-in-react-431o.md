# React 中的无限加载列表组件

> 原文：<https://dev.to/yvonnickfrin/infinite-loading-list-component-in-react-431o>

*Jael valley on un lash*拍摄的照片

你好👋,

上周我写了一篇关于用 React 和 GraphQL 创建无限加载列表的文章。我发现为这种特殊情况编写一个抽象是可能的。

我给你介绍[反应——简单——无限加载](https://github.com/frinyvonnick/react-simple-infinite-loading)。它显示当用户向下滚动列表时加载的列表元素。

[![An infinite loading list of persons](img/e5b8328928f227745fd17b6d4cd4def5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KnQEy-NM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/frinyvonnick/create-infinite-loading-list-react-graphql/blob/master/assets/infinite_loading.gif%3Fraw%3Dtrue)

下面是一个代码示例。您可以在我的[上一篇文章](https://dev.to/yvonnickfrin/create-an-infinite-loading-list-with-react-and-graphql-19hh)的[资源库](https://github.com/frinyvonnick/create-infinite-loading-list-react-graphql/blob/master/src/InfiniteList.js)中找到一个使用 GraphQL 服务器的更具体的例子。

```
import React from 'react'

import InfiniteLoadingList from 'react-simple-infinite-loading'

function Example ({ items, fetchMore, hasMore }) {
  return (
    <div style={{ width: 300, height: 300 }}>
      <InfiniteLoading
        items={items}
        itemHeight={40}
        hasMoreItems={hasMore}
        loadMoreItems={fetchMore}
      >
        {({ item, style }) => (
          <div style={style}>{item}</div>
        )}
      </InfiniteLoading>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

它使用了布莱恩·沃恩的三个库:

*   react-window 用于高效显示大型列表。它只为可见元素和重用节点创建组件。
*   react-window-infinite-loader 是一个 ad HOC，当用户向下滚动列表时，它会及时加载元素
*   [react-virtualized-auto-sizer](https://github.com/bvaughn/react-virtualized-auto-sizer/)帮助你显示你的列表，使其适合其父容器中的可用空间。

如果你有兴趣的话，可以试一试！

资源库:[https://github . com/frinyvonnick/react-simple-infinite-loading](https://github.com/frinyvonnick/react-simple-infinite-loading)

感谢反馈和贡献🙏有任何问题请发微博给我[@ yvonickfrin](https://twitter.com/YvonnickFrin)！

希望有帮助！