# 用 React 和 GraphQL 创建一个无限加载列表

> 原文：<https://dev.to/yvonnickfrin/create-an-infinite-loading-list-with-react-and-graphql-19hh>

*吕山德·袁在 Unsplash 上拍摄的照片*

* * *

本周，我必须实现一个无限加载列表来显示我为当前客户开发的 React 应用程序中的用户。我以前已经做过，但从未使用 GraphQL 作为服务器部分。是时候学习如何实现 GraphQL 规范中描述的[分页机制](https://graphql.org/learn/pagination/)了。

无限加载列表对于将大量数据分解成小块非常有用，当用户向下滚动列表时可以加载这些小块。 [dev.to](https://dev.to/) 的首页就是这种列表的一个很好的例子。它为用户节省了一整页的工作量。它还在移动应用程序中提供了更好的用户体验。

<figure>![Infinite loading list of persons](img/e5b8328928f227745fd17b6d4cd4def5.png)

<figcaption>*We will create a infinite loading list of persons. You can see data loading as the user scrolls down the list.*</figcaption>

</figure>

## 本文涵盖的内容

在本教程中，我们将描述如何使用 [Apollo Server](https://www.apollographql.com/docs/apollo-server/) 在 GraphQL 端实现分页。我们不会讨论服务器的设置，但是你可以在我写的[上一篇文章](https://dev.to/yvonnickfrin/mock-your-graphql-server-realistically-with-faker-js-25oo)中找到如何用 Koa 设置服务器。

接下来，我们将在 React 端使用最近添加到 [Apollo 客户端](https://www.apollographql.com/docs/react/)的[钩子](https://blog.apollographql.com/apollo-client-now-with-react-hooks-676d116eeae2)消费一个分页查询。

最后但同样重要的是，我们将使用 [Brian Vaughn](https://github.com/bvaughn) 的库 [react-window](https://github.com/bvaughn/react-window) 实现无限加载列表。

## 分页

我们想显示一个人员列表。我们将使用一个简单的数据模型，一个人有一个`firstname`和一个`lastname`。这将足以说明我们正在实施什么。

```
type Person {
    id: ID,
    lastname: String,
    firstname: String,
} 
```

Enter fullscreen mode Exit fullscreen mode

简单的部分已经完成了。现在是最难的，分页。有多种分页方式。您可能知道使用页面索引和元素偏移量的基于页面的分页。但是这种分页模型有一些常见场景中会遇到的限制。你可以从[的 Caleb Meredith](https://twitter.com/calebmer) 的这篇[文章](https://blog.apollographql.com/explaining-graphql-connections-c48b7c3d6976)中了解更多。

我们将使用基于光标的分页。

其原理在于，游标是列表中每个元素的唯一标识符。在我们的例子中，它是这个人的 id(但也可以是其他任何东西)。

我相信举例是最好的理解方式。让我们抛出一系列查询来加载前两个人员块。

```
persons(first: 10) 
```

Enter fullscreen mode Exit fullscreen mode

它将返回前十个人。每个人都有一个光标。我们将使用最后提取的人员的光标再次查询 GraphQL 服务器，并获得一个新的人员块。

```
persons(first: 10, cursor: "ZmY3OTI0YWMtYTY0Ny00NTIyLWE2ZjEtNzJmMTNhN2E3NjAx") 
```

Enter fullscreen mode Exit fullscreen mode

它将返回我们获取的最后一个人之后的十个人。

在 GraphQL 中，分页查询返回一个`connection`。

```
type Query {
    persons(first: Int!, cursor: ID): PersonConnection
} 
```

Enter fullscreen mode Exit fullscreen mode

A `connection`让您提供比查询元素更多的信息。比如与当前页面相关的元素或信息的总数。

```
type PersonConnection {
    edges: [PersonEdge]
    pageInfo: PageInfo
}

type PageInfo {
    endCursor: ID!,
    hasNextPage: Boolean!,
} 
```

Enter fullscreen mode Exit fullscreen mode

元素的集合存储在一个`edges`属性中。一个`edge`由我们前面谈到的光标和一个包含与列表元素相关的所有信息的`node`组成。因为光标不直接在`Person`中，所以它让我们可以自由地改变我们的服务器实现，而不会影响数据模型。它还增加了增强 edge 携带的信息的可能性，比如添加一个`relations`属性，列出与这个人有联系的人。

```
type PersonEdge {
    cursor: ID!,
    node: Person!,
} 
```

Enter fullscreen mode Exit fullscreen mode

是时候为我们的`persons`查询实现解析器了。我们使用 faker 来生成随机数据并提供一个种子，这样人们就不会在每次请求之间改变。

```
const range = (size, callback) => {
  return Array.from({length: size}, callback);
};

const resolvers = {
  Query: {
    persons: (query, {cursor, first}) => {
      faker.seed(123);
      const persons = range(200, () => ({
        id: random.uuid(),
        firstname: name.firstName(),
        lastname: name.lastName(),
      }));

      const cursorIndex = !cursor
        ? 0
        : persons.findIndex(person => person.id === cursor) + 1;
      const sliceOfPersons = persons.slice(cursorIndex, cursorIndex + first);

      return {
        edges: sliceOfPersons.map(person => ({
          cursor: person.id,
          node: {...person},
        })),
        pageInfo: {
          endCursor: sliceOfPersons[sliceOfPersons.length - 1].id,
          hasNextPage: cursorIndex + first < persons.length,
        },
      };
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果没有提供 cursor 参数，我们将在数组的开头创建一个由`first`参数决定的一定数量的元素的切片。如果提供了`cursor`参数，我们将在数组中找到 person 的索引，并在该索引处创建一个切片。

我们不会忘记通过用切片中最后一个人的`index`设置`endCursor`属性来提供与当前页面相关的信息。我们还添加了一个`hastNextPage`属性，通知用户是否可以查询更多的人。

我们现在已经完成了服务器部分。您可以使用 GraphQL playground 测试您的查询。在 Apollo 服务器实现中，默认情况下它在 GraphQL 端点上可用。在您的浏览器中打开以下 url `http://localhost:4000/graphl`并键入此查询(该 url 取决于您在服务器中配置的端点):

```
{
  persons(first: 10) {
    edges {
      node {
        lastname
        firstname
      }
    }
    pageInfo {
      endCursor
      hasNextPage
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该会在右侧面板上看到一个人员列表。

## 从 Apollo 客户端使用钩子消费查询

我在 React 应用程序中使用了`create-react-app`。我将使用下面的文件夹结构:

```
.
├── package.json
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── InfiniteList.css
    ├── InfiniteList.hooks.js
    ├── InfiniteList.js
    ├── index.css
    └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要安装 Apollo 客户端依赖项。

```
yarn add apollo-boost @apollo/react-hooks graphql 
```

Enter fullscreen mode Exit fullscreen mode

在文件`App.js`中，我们使用`apollo-boost`实例化了一个 Apollo 客户端，并将其从`@apollo/react-hooks`传递给一个`Provider`。

```
import React from 'react';
import ApolloClient from 'apollo-boost';
import {ApolloProvider} from '@apollo/react-hooks';

import InfiniteList from './InfiniteList';

import './App.css';

const client = new ApolloClient({
  uri: 'http://localhost:4000/graphql',
});

function App() {
  return (
    <ApolloProvider client={client}>
      <div className="App">
        <InfiniteList />
      </div>
    </ApolloProvider>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢将数据提取逻辑与呈现逻辑分开。我们将在文件`InfiniteList.hooks.js`中创建一个【自定义钩子】。

我们用 GraphQL 查询创建一个常量。

```
import {gql} from 'apollo-boost';

const GET_PERSONS = gql`
  query getPersons($cursor: ID) {
    persons(first: 20, cursor: $cursor) {
      edges {
        node {
          lastname
          firstname
        }
      }
      pageInfo {
        endCursor
        hasNextPage
      }
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了一个名为`usePersons`的定制钩子，它将返回实现无限加载列表所需的所有变量。为了调用我们的查询，我们使用来自`@apollo/react-hooks`的`useQuery`钩子。它将查询和选项作为参数。我们将`notifyOnNetworkStatusChange`选项指定为 true，这样每次调用查询时`loading`变量都会更新。

```
import {useQuery} from '@apollo/react-hooks';

// ...

function usePersons() {
  const {data, loading, fetchMore} = useQuery(GET_PERSONS, {
    notifyOnNetworkStatusChange: true,
  });

  if (loading && !data.persons) return {loading, persons: []};

  const loadMore = () => {
    return fetchMore({
      query: GET_PERSONS,
      notifyOnNetworkStatusChange: true,
      variables: {
        cursor: data.persons.pageInfo.endCursor,
      },
      updateQuery: (previousResult, {fetchMoreResult}) => {
        const newEdges = fetchMoreResult.persons.edges;
        const pageInfo = fetchMoreResult.persons.pageInfo;

        return newEdges.length
          ? {
              persons: {
                __typename: previousResult.persons.__typename,
                edges: [...previousResult.persons.edges, ...newEdges],
                pageInfo,
              },
            }
          : previousResult;
      },
    });
  };

  return {
    persons: data.persons.edges.map(({node}) => node),
    hasNextPage: data.persons.pageInfo.hasNextPage,
    loading,
    loadMore,
  };
}

export default usePersons; 
```

Enter fullscreen mode Exit fullscreen mode

`useQuery`返回一个`fetchMore`函数，我们可以在用户每次向下滚动列表时使用。我们准备了一个`loadMore`函数，用我们前面解释过的最后一个人的光标来调用`fetchMore`。`updateQuery`选项让我们描述如何处理新获取的大量人员。我们把新的边和以前的边合并起来。

## 实现无限加载列表

我们将使用`react-window`来实现显示无限加载列表的组件。我们在做之前安装依赖项。

```
yarn add react-window react-window-infinite-loader react-virtualized-auto-sizer 
```

Enter fullscreen mode Exit fullscreen mode

关于这些依赖性的一些解释是必要的。用于高效显示大型列表。它只为可见元素和重用节点创建组件。

`react-window-infinite-loader`是一个在用户向下滚动列表时及时加载元素的特设组件，`react-virtualized-auto-sizer`是一个小的附加组件，可以帮助你显示列表，使其适合父容器中的可用空间。

所有这些工具都是由布莱恩·沃恩制造的。他们完美地配合在一起。

首先，我们调用自定义钩子来获取第一批人。

```
import React from 'react';

import usePersons from './InfiniteList.hooks';

import './InfiniteList.css';

function InfiniteList() {
  const {persons, loading, loadMore, hasNextPage} = usePersons();
}

export default InfiniteList; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在添加`AutoSizer`组件来获得一个`width`和一个`height`属性，表示组件容器中的可用空间。

```
import AutoSizer from 'react-virtualized-auto-sizer';

// ...

return (
  <div className="InfiniteList-list">
    <AutoSizer>{({height, width}) => <div />}</AutoSizer>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了需要三个属性的`InfiniteLoader`组件:

*   `isItemLoaded`确定是一行已经被加载
*   `itemCount`是将在列表中显示的元素总数。在我们的例子中，我们将使用一个小技巧，因为我们可能不知道这个数字(想想 twitter 提要)。如果还有下一页要加载，我们将总计数加 1。
*   是一个获取新人群的函数

它还取一个函数作为`children`。在作为参数传递的对象中有两个变量，让`InfiniteLoader`控制我们将在下一步添加的`List`组件。

```
const personsCount = hasNextPage ? persons.length + 1 : persons.length;
const loadMorePersons = loading ? () => {} : loadMore;
const isPersonLoaded = index => !hasNextPage || index < persons.length;

// ...

return (
  <div className="InfiniteList-list">
    <AutoSizer>
      {({height, width}) => (
        <InfiniteLoader
          isItemLoaded={isPersonLoaded}
          itemCount={personsCount}
          loadMoreItems={loadMorePersons}>
          {({onItemsRendered, ref}) => <div />}
        </InfiniteLoader>
      )}
    </AutoSizer>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们添加组件`List`来显示实际的人员列表。

为了工作，它需要一个`height`和`width`属性。我们应该传递由`AutoSizer`组件提供的值。在`itemSize`属性中也需要一个行高。我们还将来自`InfiniteLoader`的变量作为道具传递。

组件`List`也将一个函数作为`children`。它给你当前人的`index`和一个`style`属性。您必须将`style`属性传递给行的父元素，这样列表才能很好地显示。

如果此人尚未加载，我们会显示一个占位符“正在加载...”。

```
// ...

return (
  <div className="InfiniteList-list">
    <AutoSizer>
      {({height, width}) => (
        <InfiniteLoader
          isItemLoaded={isPersonLoaded}
          itemCount={personsCount}
          loadMoreItems={loadMorePersons}>
          {({onItemsRendered, ref}) => (
            <List
              height={height}
              itemCount={personsCount}
              itemSize={40}
              onItemsRendered={onItemsRendered}
              ref={ref}
              width={width}>
              {({index, style}) => {
                let content;
                if (!isPersonLoaded(index)) {
                  content = 'Loading...';
                } else {
                  const {firstname, lastname} = persons[index];
                  content = `${firstname}  ${lastname}`;
                }

                return (
                  <div className="InfiniteList-item" style={style}>
                    {content}
                  </div>
                );
              }}
            </List>
          )}
        </InfiniteLoader>
      )}
    </AutoSizer>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

你都准备好了🙌！

我用本文中介绍的所有源代码创建了一个[存储库](https://github.com/frinyvonnick/create-infinite-loading-list-react-graphql)。

您可以通过在存储库的顶层执行以下命令来运行它:

```
yarn && yarn start 
```

Enter fullscreen mode Exit fullscreen mode

感谢反馈🙏有任何问题请发微博给我[@ yvonickfrin](https://twitter.com/YvonnickFrin)！

**编辑:**我为 React 实现部分做了一个抽象，叫做`react-simple-infinite-loading`。你可以在这篇[文章](https://dev.to/yvonnickfrin/infinite-loading-list-component-in-react-431o)中找到更多关于这个项目及其运作方式的信息。