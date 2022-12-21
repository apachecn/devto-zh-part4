# GraphQL 和 Urql 示例

> 原文：<https://dev.to/open-graphql/graphql-and-urql-by-example-1ldc>

根据[文件](https://github.com/FormidableLabs/urql)，

> urql 是一个 GraphQL 客户端，它公开了一组 React 组件和钩子。它具有高度的可定制性和通用性，因此您可以从开始使用第一个 GraphQL 项目一直到构建复杂的应用程序和试用 GraphQL 客户端。

[Urql](https://formidable.com/open-source/urql/) ，发音为`Urkel`，最近几个月前达到了 [v1.0](https://formidable.com/blog/2019/urql-2019/) 。不像 [Apollo](https://www.apollographql.com/docs/react/) ，它是一个极简主义的 GraphQL 客户端。Urql 是为了应对 Apollo 日益复杂的设置而引入的。

## 阿波罗与 Urql 的比较

Apollo 和 Urql 之间的详细比较可以在下面找到(感谢[这一原始要点](https://gist.github.com/oddlyfunctional/74dc7e0d6df4e2e7c70fc6c4b5ed212e)，为最近的更正进行了编辑):

| 特征 | 阿波罗客户端 | Urql |
| --- | --- | --- |
| 隐藏物 | 规范化对象 | 哈希查询+变量。规格化缓存为 [WIP](https://github.com/FormidableLabs/urql-exchange-graphcache) |
| 定量 | 与`apollo-link-batch-http` ( [虽然它建议尽可能推迟配料](https://blog.apollographql.com/batching-client-graphql-queries-a685f5bcd41b) | 没有第一方解决方案，但允许使用 Apollo 的链接扩展 |
| 德都平 | 带`apollo-link-dedup`(默认启用) | 用`dedupExchange` |
| 证明 | 支持向`fetch`客户端添加选项或完全改变网络层 | 支持向`fetch`客户端添加选项或完全改变网络层 |
| 页码 | 第一方支持与 [fetchMore](https://www.apollographql.com/docs/react/advanced/caching#fetchMore) ，还提供[几个食谱](https://www.apollographql.com/docs/react/features/pagination) | 没有第三方支持，需要实施定制解决方案 |
| 反应钩 | 从 [v3.0](https://blog.apollographql.com/apollo-client-now-with-react-hooks-676d116eeae2) 开始正式支持 | 第一方支持 |
| 乐观更新 | `mutate({ optimisticResponse })` ( [如果插入新数据需要操作缓存](https://www.apollographql.com/docs/react/features/optimistic-ui) | 不支持，因为[基于文档的缓存](https://github.com/FormidableLabs/urql/issues/212#issuecomment-476232503) |
| 地方州 | [支持`@client`指令](https://www.apollographql.com/docs/react/essentials/local-state) | [没有官方支持](https://github.com/FormidableLabs/urql/issues/323) |
| 突变后重新提取 | [T2`mutate({ refetchQueries })`](https://www.apollographql.com/docs/react/essentials/mutations) | 需要手动调用执行查询时获得的函数 |
| 捐款 | 支持 | 支持 |
| 社区 | 充满活力，易于在线找到答案，[官方聊天](https://spectrum.chat/apollo)，大量问题和公关 | 几乎不存在 |
| 证明文件 | 非常彻底，有几个教程和食谱 | 全面的 |

## 设置 GraphQL 服务器

Prisma 2 专门为本教程制作了一个 [GraphQL 服务器](https://github.com/deadcoder0904/graphql-and-urql-by-example/tree/master/server),所以一定要克隆它。

克隆之后，使用`yarn`安装依赖项。这也将运行`postinstall`钩子，该钩子运行`prisma2 generate`在`node_modules/@generated`文件夹中生成[光子](https://photonjs.prisma.io/)和 [nexus-prisma 类型](https://github.com/prisma/nexus-prisma/)。

继续使用
启动服务器

```
$ yarn start 
```

打开 [http://localhost:4000](http://localhost:4000) 来玩 GraphQL API。

## Urql 入门

安装`urql`和你选择的软件包管理器:

```
$ yarn add urql
# or
$ npm install urql 
```

`urql`有一个类似于其他库的`Provider`组件，比如管理状态和数据的`react-redux`。您需要用`Provider`组件包装您的应用程序。这个`<Provider>`组件持有用于管理数据、请求、缓存和其他东西的`client`，这样它下面的每个组件都可以访问客户端，并且可以查询或修改数据。

```
import React from 'react';
import { Provider, createClient } from 'urql';

const client = createClient({
  url: "http://localhost:4000"
});

const App = () => (
    <Provider value={client}>
    {/* ... */}
    </Provider> );

export default App; 
```

## 使用 Render Props 或 React Hooks 在 Urql 中查询数据

让我们使用`urql`的`Query`组件查询一些 GraphQL 数据。

```
import React from 'react';
import { useQuery } from "urql";

const getPokemonData = `
    query GetPokemonData($name: String!) {
        pokemon(name: $name) {
            id
            number
            name
            attacks {
                special {
                    id
                    name
                    damage
                }
            }
        }
    }
`;

export const ListPokemonDataQuery = ({ name = "Pikachu" }) => {
    const [{ fetching, data, error }] = useQuery({
        query: getPokemonData,
        variables: { name }
    });

    if (fetching) {
        return `Loading ${name}...`;
    } else if (error) {
        return `Oh no! Error: ${error}`;
    }

    const pokemon = data.pokemon[0];
    return (
        <>
            <h1>
                #{pokemon.number} {pokemon.name}
            </h1>
            <ul>
                {pokemon.attacks.special.map(({ name, id, damage }) => (
                    <li key={name}>
                        #{id} {name} - {damage}
                    </li>
                ))}
            </ul>
        </>
    );
}; 
```

上面的`Query`组件将`name`作为变量的`getPokemonData`查询发送给`createClient`的`url`属性中提到的 GraphQL API。

是一个渲染道具，它只不过是一个反应组件，其值是一个函数。这个渲染道具给了我们`fetching`、`data`和`error`。`fetching`返回一个布尔值，表明请求是否仍在发送，是否仍在加载。`data`给出 GraphQL API 返回的数据，而`error`给出 GraphQL API 是否有任何错误。

`urql`也有一流的钩子支持，所以我们也可以使用`useQuery`功能。

如果我们重写上面的例子，它将看起来像:

```
import React from "react";
import { useQuery } from "urql";

const getPokemonData = `
    query GetPokemonData($name: String!) {
        pokemon(name: $name) {
            id
            number
            name
            attacks {
                special {
                    id
                    name
                    damage
                }
            }
        }
    }
`;

export const ListPokemonDataHook = ({ name = "Pikachu" }) => {
    const [{ fetching, data, error }] = useQuery({
        query: getPokemonData,
        variables: { name },
    })

    if (fetching) {
        return `Loading ${name}...`;
    } else if (error) {
        return `Oh no! Error: ${error}`;
    }

    const pokemon = data.pokemon[0];
    return (
        <>
            <h1>
                #{pokemon.number} {pokemon.name}
            </h1>
            <ul>
                {pokemon.attacks.special.map(({ name, id, damage }) => (
                    <li key={name}>
                        #{id} {name} - {damage}
                    </li>
                ))}
            </ul>
        </>
    );
} 
```

注意，`useQuery`钩子是如何简化组件结构的。`useQuery`像任何其他 [React Hook](https://reactjs.org/docs/hooks-intro.html) 一样工作，因为它接受一个值并返回一个元组。它接受的值是一个查询和一个变量名，它返回一个包含`fetching`、`data`和`error`的元组。其他都一样。

## 使用渲染道具或 React 钩子使 Urql 中的数据变异

让我们使用`urql`的`Mutation`组件改变一些 GraphQL 数据。

```
import React, { useState } from 'react';
import { Mutation } from 'urql';

const addPokemon = `
  mutation AddPokemon($number: Int!, $name: String!) {
    addPokemon(data: {
      number: $number,
      name: $name
    }) {
      id
      number
      name
    }
  }
`

export const InsertPokemonMutation = () => {
  const [name, setName] = useState('')
  return (
    <Mutation query={addPokemon}>
      {({ fetching, data, error, executeMutation }) => {
        return (
          <>
            {error && <div>Error: {JSON.stringify(error)}</div>}
            <input value={name} onChange={e => setName(e.target.value)} />
            <button onClick={() => {
              if (name.trim() === "") return // return if input is empty
              executeMutation({ name, number: Math.ceil(Math.random() * 1000) })
              setName("") // clear the input
            }}>
              Add Pokemon
            </button>
            {data && (<div>
              <br/>
              Mutation successful: 
              <pre>{JSON.stringify(data, null, 2)}</pre>
            </div>)}
          </>
        )
      }}
    </Mutation>
  )
} 
```

`Mutation`组件接受一个查询并返回`executeMutation`。`executeMutation`是一个函数，它接受一个变量`name`和一个随机的`number`，如我们在上面的`addPokemon`查询中所述，并调用`Mutation`。如果突变不成功，则显示`error`。如果你想做任何事情，渲染道具还会给你`fetching`和`data`。

如果我们使用`useMutation`钩子重写上面的例子，那么它看起来像:

```
import React, { useState } from 'react';
import { useMutation } from 'urql';

const addPokemon = `
  mutation AddPokemon($number: Int!, $name: String!) {
    addPokemon(data: {
      number: $number,
      name: $name
    }) {
      id
      number
      name
    }
  }
`

export const InsertPokemonHook = () => {
  const [name, setName] = useState('')
  const [{ fetching, data, error }, executeMutation] = useMutation(addPokemon)
  return (
    <>
      {error && <div>Error: {JSON.stringify(error)}</div>}
      <input value={name} onChange={e => setName(e.target.value)} />
      <button onClick={() => {
        if (name.trim() === "") return
        executeMutation({ name, number: Math.ceil(Math.random() * 1000) })
        setName("")
      }}>
        Add Pokemon
      </button>
      {data && (<div>
        <br/>
        Mutation successful: 
        <pre>{JSON.stringify(data, null, 2)}</pre>
      </div>)}
    </>
  )
} 
```

`useMutation`接受突变`addPokemon`并返回元组中的突变状态(`fetching`、`data`、`error`)和`executeMutation`函数。然后点击按钮调用`executeMutation`。

## Urql 中有哪些交易所

`urql`有一个`exchanges`的概念。当一个`new Client()`被创建时，你传递给它一个`url`和`fetchOptions`。但是你也可以传递一个`exchanges`数组给它。交易所是操作处理者。它接收`client`和`forward`函数作为对象，并返回一个接受操作流并返回操作结果流(即 GraphQL 结果)的函数。

换句话说，交换是满足我们的 GraphQL 请求的处理程序。它们是输入/输出流，输入是操作，输出是结果。

默认情况下，`urql`创建 3 个不同的交易所，即`dedupExchange`、`cacheExchange`和`fetchExchange`。

`dedupExchange`对挂起的操作进行重复数据删除。它消除了等待响应的重复操作，因为同时发送两次相同的操作是没有意义的。

`cacheExchange`根据缓存检查操作。根据`requestPolicy`，缓存的结果可以被解析，来自网络请求的结果被缓存。

`fetchExchange`向 API 发送操作并返回结果。

当一个`new Client()`被创建并且没有交换被传递给它时，一些交换被自动添加，这与使用下面的交换创建一个`new Client()`是一样的:

```
import { Client, dedupExchange, cacheExchange, fetchExchange } from "urql";

const client = new Client({
  url: "http://localhost:4000",
  exchanges: [dedupExchange, cacheExchange, fetchExchange]
}); 
```

这也可以写成:

```
import { Client, defaultExchanges } from "urql";

const client = new Client({
  url: "http://localhost:4000",
  exchanges: defaultExchanges
}); 
```

现在我们知道了什么是交换，让我们学习一下订阅。

## 使用 Render Props 或 React 钩子订阅 Urql 中的数据

继续，首先使用`yarn` :
安装`subscriptions-transport-ws`

```
$ yarn add subscriptions-transport-ws 
```

要使用订阅，我们需要首先将`subscriptionExchange`添加到我们的`new Client()`中，并使用 websocket 协议创建一个`new SubscriptionClient()`，如下所示:

```
import { SubscriptionClient } from "subscriptions-transport-ws";
import { Client, defaultExchanges, subscriptionExchange } from "urql";

const subscriptionClient = new SubscriptionClient(
  "ws://localhost:4001/graphql",
  {
    reconnect: true,
    timeout: 20000
  }
);

const client = new Client({
  url: "http://localhost:4000",
  exchanges: [
    ...defaultExchanges,
    subscriptionExchange({
      forwardSubscription: operation => subscriptionClient.request(operation)
    })
  ]
}); 
```

现在我们可以开始在我们的应用程序中使用`Subscription`组件:

```
import React from 'react'
import { Subscription } from 'urql'

const newPokemon = `
  subscription PokemonSub {
    newPokemon {
      id
      number
      name
      attacks {
        special {
          name
          type
          damage
        }
      }
    }
  }
`

const NewPokemon = () => (
  <Subscription query={newPokemon}>
    {({ fetching, data, error }) => {
      if (fetching) {
        return `Loading...`
      } else if (error) {
        return `Oh no! Error: ${error}`
      }

      const { newPokemon } = data
      return (
        <>
          <h1>
            #{newPokemon.number} {newPokemon.name}
          </h1>
          <ul>
            {newPokemon.attacks.special.map(({ name, type, damage }) => (
              <li key={name}>
                {name} ({type}) - {damage}
              </li>
            ))}
          </ul>
        </>
      )
    }}
  </Subscription> ) 
```

`Subscription`组件的工作方式与`Query`组件相似。它可以接受一个`query`和一个`variables`道具。它也有`fetching`、`data`和`error`，就像一个`Query`组件。每次服务器接收到新事件时，渲染道具的`data`和`error`都会改变。

我们也可以使用`useSubscription`钩子如下:

```
import React from 'react';
import { useSubscription } from 'urql';

const newPokemon = `
  subscription PokemonSub {
    newPokemon {
      id
            number
            name
            attacks {
                special {
                    name
                    type
                    damage
                }
            }
    }
  }
`

export const NewPokemonSubscriptionHook = () => {
  const [{ fetching, data, error }] = useSubscription({ query: newPokemon }, (pokemons = [], res) => {
        return [res.newPokemon, ...pokemons] 
    })

  if (fetching) {
    return `Loading...`
  } else if (error) {
    return `Oh no! Error: ${error}`
  }
    return (
        <>
            {data.map(pokemon => {
              const { newPokemon } = pokemon
                return (
                    <div key={newPokemon.number}>
                        <h1>
                            #{newPokemon.number} {newPokemon.name}
                        </h1>
                        <ul>
                            {newPokemon.attacks.special.map(({ name, type, damage }) => (
                                <li key={name}>
                                    {name} ({type}) - {damage}
                                </li>
                            ))}
                        </ul>
                    </div>
                )
            })}
        </>
    )
} 
```

`useSubscription`接收订阅`newPokemon`并返回订阅状态(`fetching`、`data`和`error`)。此外，`useSubscription`的第二个参数可以是一个可选的 reducer 函数，其工作方式类似于 [Array.prototype.reduce](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。它接收该函数返回的前一组数据或`undefined`。作为第二个参数，它接收来自订阅的事件。您可以使用它来累积一段时间内的数据，这对于一个列表非常有用。

# 结论

在本教程中，我们学习了 URQL(Universal React Query Library ),这是一个速度极快的 GraphQL 客户端，作为一组 ReactJS 组件公开。然后我们展示了 Apollo 和 Urql 之间的区别。

我们了解了 Urql 提供的`Query` API、`Mutation` API 和`Subscription` API。我们还使用钩子`useQuery`、`useMutation`和`useSubscription`来减少渲染道具不必要的回调地狱样板。

我们还学习了交换。最后，我们使用 Urql 创建了一个简单的口袋妖怪应用程序。Urql 是一项新技术，但它已经足够成熟，可以用于生产。虽然，由于缺乏缓存规范化，乐观更新等一些东西还没有工作，但它是一个正在进行中的工作，很快就会发布。