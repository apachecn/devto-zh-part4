# 在 Dojo 中使用 GraphQL

> 原文：<https://dev.to/odoenet/using-graphql-with-dojo-3i90>

GraphQL 在过去几年里越来越受欢迎。GraphQL 的亮点在于其查询数据的描述性。

如果你想为[星球大战 API](https://swapi.co/) 编写一个查询来获取所有的电影名称，它可能看起来像这样。

```
{
  allFilms{
    edges{
      node{
        title
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该查询类似于*JSON*，但不是 JSON。你可以在[教程页面](https://www.graphql.com/tutorials/)上了解更多关于 GraphQL 的知识。

[Apollo](https://github.com/apollographql/apollo-client) 提供了一个客户端 API，您可以使用它来处理 GraphQL。它为你节省了一些自己写帖子请求的工作，所以我强烈建议你学习它。Apollo 提供了与 Angular 和 React 集成的库，但到目前为止还没有 Dojo 的库。但这没关系，因为您可以使用 Apollo 客户端构建自己的 GraphQL 集成。

看看 [react-apollo](https://github.com/apollographql/react-apollo) ，他们有一个`ApolloProvider`，你可以用它来包装你的应用程序的组件。这为组件提供了 Apollo 客户端。这些组件然后可以使用一个`Query`更高阶的组件来传递查询和客户端，从而显示结果。

这能有多难？

## 信任流程

当使用 Dojo 时，您使用外部 API 的大部分工作可能会在一个[流程](https://github.com/dojo/framework/tree/master/src/stores#processes)中完成。我们在[本帖](https://learn-dojo.com/maintain-state-with-dojo-stores)中详细报道了这个话题。

下面是使用 GraphQL 的一般过程。

```
// src/processes/apolloProcess.ts
import {
  createProcess,
  createCommandFactory
} from "@dojo/framework/stores/process";
import { add } from "@dojo/framework/stores/state/operations";

const commandFactory = createCommandFactory<{
  client: any; // this will be the apollo client
  data: any; // this will be graphql result
  loaded: boolean; // keep track if the data has been loaded yet
}>();

const fetchData = commandFactory(async ({ path, payload }) => {
  const { client, query } = payload;
  const { data } = await client.query({ query });
  return [add(path("data"), data), add(path("loaded"), true)];
});

export const fetchDataProcess = createProcess("fetch-data", [fetchData]); 
```

Enter fullscreen mode Exit fullscreen mode

这个过程需要一个给定的 apollo 客户机实例和一个 GraphQl 查询来获取一些结果。这工作得很好，因为它不依赖于任何特定的端点或数据结构，即使它当前被类型化为客户端和数据的`any`。我可以尝试用一些泛型来解决这个问题，但是我想让这个例子相当简单。

## 把它放在盒子里

我们可以用一个小部件和 [Dojo 容器](https://dojo.io/tutorials/1010_containers_and_injecting_state/)将它们结合在一起。

```
// src/containers/QueryContainer.tsx
import { tsx } from "@dojo/framework/widget-core/tsx";
import { WidgetBase } from "@dojo/framework/widget-core/WidgetBase";

import { Store } from "@dojo/framework/stores/Store";
import { StoreContainer } from "@dojo/framework/stores/StoreInjector";

import { fetchDataProcess } from "../processes/apolloProcess";

// Use the ApolloClient for typing
import ApolloClient from "apollo-boost";

interface QueryProps {
  client?: ApolloClient<any>;
  query: string;
  data?: any;
  loaded?: boolean;
  fetchData?: (args: any) => void;
}

// base widget that handles displaying children that use the Query
export class BaseQuery extends WidgetBase<QueryProps, any> {
  onAttach() {
    const { client, query, loaded, fetchData } = this.properties;
    // if the data has not been loaded yet
    // and we have a query, lets get some data
    if (!loaded && query) {
      fetchData({ client, query });
    }
  }
  protected render() {
    const { loaded, data } = this.properties;
    return this.children.map(child => {
      // if the child nodes are a function,
      // call the function with data from the
      // GraphQL process
      if (typeof child === "function") {
        return child({ loading: !loaded, data });
      }
      // or just return a regular node
      return child;
    });
  }
}

function getProperties(store: Store<{ data: any; loaded: boolean }>): any {
  const { get, path } = store;

  // pass the Dojo store properties and methods to the widget
  return {
    data: get(path("data")),
    loaded: get(path("loaded")),
    fetchData: fetchDataProcess(store)
  };
}
// Use a StoreContainer
export const Query = StoreContainer(BaseQuery, "state", {
  getProperties
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，我们提供了一个`BaseQuery`，它将处理子部件可能提供的任何查询，并使用这些查询来获取一些数据。这个小部件使用一个`StoreContainer`将使用我们的流程更新的商店传递给`BaseQuery`。为了简单起见，我们可以称这个容器为`Query`。这将允许我们编写一些代码，如下所示。

```
export class MyWidget extends WidgetBase<{ client: any }> {
  protected render() {
    const { client } = this.properties;
    return (
      // use our Query Widget with the client it's given and
      // a query we have written
      <Query query={query} client={client}>
        {({ loading, data }) => {
          if (loading) {
            return <span>Loading...</span>;
          } else {
            return <div classes={[css.root]}>{parseMyData(data)}</div>;
          }
        }}
      </Query>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 做一个好的供给者

此时，您可能会问自己，*我如何将客户端传递给使用它的小部件？*

好问题。从技术上讲，您可以在小部件模块中创建客户机，并将其提供给`<Query>`。但是将后端问题绑定到我的 UI 代码中似乎有点麻烦。`react-apollo`的方式是提供一个`<ApolloProvider>`，你可以给一个`client`，然后用它包装你的应用组件。这些组件将有权访问阿波罗`client`给予`Query`高阶组件。

基本上看起来像是`<ApolloProvider>` *向子部件提供了*的`client`属性。*我可以做到。* 

```
// src/providers/ApolloProvider.tsx
import { tsx } from "@dojo/framework/widget-core/tsx";
import { WidgetBase } from "@dojo/framework/widget-core/WidgetBase";

export class ApolloProvider extends WidgetBase<{ client: any }> {
  protected render() {
    const { client } = this.properties;
    for (let child of this.children) {
      if ((child as any).properties) {
        // inject the client of the provider into each child
        // widget
        (child as any).properties.client = client;
      }
    }
    return this.children;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个*天真的* `ApolloProvider`所做的是迭代小部件的`children`并将`client`属性注入每个小部件。我确信`react-apollo`实现做得更多，但是我不打算争论什么是有效的。

现在我有了我的供应商，我可以开始把它们联系起来。

## 伟大的供应者

在我初始化我的 Dojo 应用程序的`main.tsx`中，我可以创建我的`ApolloClient`并传递给它我的`ApolloProvider`，它将包装我的其他部件以便我可以使用它。

```
// src/main.tsx
...
import { Store } from "@dojo/framework/stores/Store";
import { registerStoreInjector } from "@dojo/framework/stores/StoreInjector";

import ApolloClient from "apollo-boost";

import { ApolloProvider } from "./providers/ApolloProvider";
import { Countries } from "./widgets/Countries";

// initialize a GraphQL client
const client = new ApolloClient({
  uri: "https://countries.trevorblades.com"
});

const store = new Store();
const registry = registerStoreInjector(store);

class App extends WidgetBase {
  protected render() {
    // pass the client to the ApolloProvider
    // The <Countries /> widget will use it
    return (
      <div>
        <ApolloProvider client={client}>
          <h2>{"\u2728 dojo-apollo \u2728"}</h2>
          <Countries />
        </ApolloProvider>
      </div>
    );
  }
}
... 
```

Enter fullscreen mode Exit fullscreen mode

我将使用的示例 GraphQL API 提供了一个国家列表。所以我将编写一个小部件来显示这些结果。

## GraphQL 结果

这是我们看到自己劳动成果的地方！我们可以编写一个小部件来显示来自 GraphQL API 的一组特定数据。因此小部件可以提供自己的 GraphQL 查询。当您认为这个小部件是拥有这个查询的时候，这是有意义的。

```
import { tsx } from "@dojo/framework/widget-core/tsx";
import { WidgetBase } from "@dojo/framework/widget-core/WidgetBase";
import gql from "graphql-tag";

import * as css from "./styles/Countries.m.css";

import { Query } from "../containers/QueryContainer";

interface Country {
  name: string;
  code: number;
}
// initialize a GraphQL query
export const query = gql`
  {
    countries {
      name
      code
    }
  }
`;

// helper method to display each country as a list item
// each country will link to a wikipedia page
const countryItems = (countries: Country[] = []) =>
  countries.map(({ name, code }) => (
    <li classes={[css.item]} key={code}>
      <a
        classes={[css.link]}
        key={code}
        href={`https://en.wikipedia.org/wiki/${name}`}
        target="_blank"
      >
        {name}
      </a>
    </li>
  ));

export class Countries extends WidgetBase<{ client?: any }> {
  protected render() {
    // this is the `client` that was injected by the `<ApolloProvider>`
    const { client } = this.properties;
    return (
      <Query query={query} client={client}>
        {({ loading, data }) => {
          // if data is still loading, show a message
          if (loading) {
            return <span>Loading...</span>;
          } else {
            // when data is done loading, display the list
            return <ul classes={[css.root]}>{countryItems(data.countries)}</ul>;
          }
        }}
      </Query>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小部件使用我们的`Query`容器来包装依赖于 GraphQL 结果的小部件部分。这看起来非常像`react-apollo`。

你可以在下面看到这个完整的例子。

[https://codesandbox.io/embed/dojo-graphql-zgmi6](https://codesandbox.io/embed/dojo-graphql-zgmi6)

## 总结

这是 GraphQL `<Query>`和`<ApolloProvider>`的一个相当简单的实现，但是它在这种情况下工作得相当好。如果您想在一个应用程序中运行多个不同的查询，我认为您需要为您的容器创建一个工厂方法来定义包含不同结果的多个状态。

这绝对是我想在未来继续努力的事情，我认为在未来 Dojo 的一些特性中可能会有更多的 Dojo 方式来处理这个问题。

一如既往，享受它的乐趣，继续黑客！