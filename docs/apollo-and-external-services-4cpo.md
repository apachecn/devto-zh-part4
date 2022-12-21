# 阿波罗和外部服务

> 原文：<https://dev.to/iamphill/apollo-and-external-services-4cpo>

您是否曾经想使用 GraphQL 和 Apollo，但仍然想访问一些外部或较旧的 API？

嗯你**可以**！而且做起来超级简单。

首先让我们设置我们的阿波罗客户端

```
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';

const client = new ApolloClient({
  url: URL_TO_GRAPHQL,
  cache: new InMemoryCache(),
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是事情变得有趣的地方。在旧版本的 Apollo 中，您将使用 [apollo-link-state](https://www.apollographql.com/docs/link/links/state/) ，这将允许您通过 GraphQL 查询访问客户端数据。不过，这已经被合并到阿波罗客户端([https://www . apollographql . com/docs/react/essentials/local-state/](https://www.apollographql.com/docs/react/essentials/local-state/))🎉

这允许您在查询中使用`@client`指令来访问本地缓存的数据。

```
query  {  someLocalData  @client  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道我们通过 GraphQL 查询来查询本地数据，我们可以使用 Apollo 客户机代码并添加一个额外的属性来创建一些本地查询。

```
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';

const client = new ApolloClient({
  url: URL_TO_GRAPHQL,
  cache: new InMemoryCache(),
  resolvers: {
    Query: {
      oldApi() {
        return fetch('https://old.api.com/get/old/data')
          .then(r => r.json());
      },
    },
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个部分是最重要的部分。这告诉 Apollo，每当它在查询中看到一个`@client`指令时，就使用我们的本地解析器，而不是将它们发送到 API 端点。

使用解析器设置，我们现在可以创建一个类似于:
的查询

```
query  {  oldApi  @client  {  id  text  }  } 
```

Enter fullscreen mode Exit fullscreen mode

但是这实际上有什么用呢？一些例子:

*   对较新的数据使用新的 GraphQL API，但仍然访问旧的 API
*   创建将数据保存到旧的内部 API 的变异。