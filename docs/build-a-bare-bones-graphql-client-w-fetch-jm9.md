# 通过 Fetch 构建一个基本的 GraphQL 客户端

> 原文：<https://dev.to/droopytersen/build-a-bare-bones-graphql-client-w-fetch-jm9>

已经有很多基于浏览器的 GraphQL 客户端。查看,[探索 GraphQL 客户端](https://medium.com/open-graphql/exploring-different-graphql-clients-d1bc69de305f),了解现有选项的概要。

那么，为什么要编写自己的 GraphQL 客户端呢？

*   也许是为了一件小事，你不想处理拉进 NPM 包和捆绑代码。
*   或者你正在写一个 [Chrome 片段](https://developers.google.com/web/tools/chrome-devtools/javascript/snippets)
*   也许，像我一样，你只是想**走走过场，了解更多关于 GraphQL** 的知识。

> 本文的目的不是要创建一个与之竞争的/更好的 GraphQL 客户端。我们自己花了 10 分钟来打造，这绝对无法与现有库的特性和稳健性相提并论。

## 基础知识

对 GraphQL 端点的客户端请求至少需要是一个`POST`，在请求体中有一个`query`属性。GraphQL 服务器将以`{ data }`或`{ errors }`的形式用 JSON 响应。

### 图形 SQL 请求

*   HTTP `POST`
*   帖子正文包含一个`query`属性
*   `query`是有效的 GraphQL 字符串

### 图形 SQL 响应

*   JSON 形状的`{ data, errors }`
*   如果成功的话，`data`将是一个对象，它包含您在 GraphQL 查询中所请求的每一个事物的属性。
*   `errors`，如果有，将是一个对象数组。每个错误对象至少应该有一个`message`属性。

## 请求举例

在这个例子中，我们连接到一个公开可用的 [Pokemon GraphQL API](https://graphql-pokemon.now.sh) 和`console.log`查询结果。

```
// Public Pokement API
const ENDPOINT = "https://graphql-pokemon.now.sh";
// Use a tool like GraphiQL to help you craft your query
const POKEMON_QUERY = `
  query TopFivePokemon {
    pokemons(first:5) {
      name
      image
    }
  }
`;

// Call fetch, first passing the GraphQL endpoint, then the Request Options
fetch(ENDPOINT, {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  // The request body should be a JSON string
  body: JSON.stringify({ query: POKEMON_QUERY })
})
  // Handle a JSON response
  .then(res => res.json())
  .then(result => console.log(result)); 
```

在上面的代码片段中，我们:

*   使用 JavaScript 字符串模板使 GraphQL 查询可读
*   告诉服务器我们正在发送带有`Content-Type`头的 JSON
*   将我们的 POST `body`设置为我们的`{ query }`对象的 JSON 字符串。
*   期望服务器返回 JSON，并使用`res.json()`相应地处理响应

*控制台输出*

```
{
  "data": {
    "pokemons": [
      {
        "name": "Bulbasaur",
        "image": "https://img.pokemondb.net/artwork/bulbasaur.jpg"
      },
      {
        "name": "Ivysaur",
        "image": "https://img.pokemondb.net/artwork/ivysaur.jpg"
      },
      {
        "name": "Venusaur",
        "image": "https://img.pokemondb.net/artwork/venusaur.jpg"
      },
      {
        "name": "Charmander",
        "image": "https://img.pokemondb.net/artwork/charmander.jpg"
      },
      {
        "name": "Charmeleon",
        "image": "https://img.pokemondb.net/artwork/charmeleon.jpg"
      }
    ]
  }
} 
```

### 基本客户端工厂

上面的代码片段工作得很好，但是让我们稍微增强一下，以便我们的 GraphQL 端点是可配置的，并且我们不依赖于一些现有的随机`ENDPOINT`变量。

我们希望**创建一个 GraphQL 客户端的实例，它被绑定到一个特定的端点**。

> [工厂模式](https://medium.com/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)只是一种奇特的说法，*“创建一个创建事物实例的函数”*。这是创建一个类的替代方法，`createClient(endpoint)`而不是`new Client(endpoint)`

让我们来拍摄一个类似这样的 API:

```
// Create an instance of a GraphQL client by passing in your endpoint
let client = createClient("https://graphql-pokemon.now.sh");

// Make an actual GraphQL request by passing a GraphQL query 
// to the request method on the client
client.request(POKEMON_QUERY)
  .then(result => console.log(result)); 
```

实施:

1.  将第一个例子中的`fetch`代码转换成一个名为`request`的函数。
2.  用另一个函数`createClient`包装`request`，在我们的`endpoint`参数周围创建一个闭包。

```
function createClient(endpoint) {

  let request = function(query) {
    return fetch(endpoint, {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      // The request body should be a JSON string
      body: JSON.stringify({ query })
    }).then(res => res.json());
  };

  return { request };
}; 
```

### 带有自定义头文件的客户端工厂

既然我们的端点是可配置的，让我们通过允许开发人员传入 HTTP `headers`来增加一点灵活性，HTTP`headers`应该包含在所有请求中(对于一个`Authorization`头是常见的)。

*   我们让我们的`createClient`函数为`headers`接受第二个参数，并将其默认为一个空对象。
*   我们将传入的`headers`扩展到 Fetch 选项的 headers 属性上。

```
// Allow an optional second param for request headers
function createClient(endpoint, headers = {}) {

  let request = function(query) {
    return fetch(endpoint, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        // spread the passed in headers
        ...headers
      },
      body: JSON.stringify({ query })
    }).then(res => res.json());
  };

  return { request };
} 
```

### GraphQL 变量

我们最不想支持的是标记 GraphQL 查询的变量(和变异)。在下面的截图中，你可以看到`name`正在通过一个变量进行设置。

[![GraphQL Variable](img/e21982552125d0e923f7f8f56ffd7645.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AV4tXc2b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/why1zn46l55qq7dnjfla.PNG)

这实际上很容易支持。我们需要做的就是向我们的 POST 主体添加一个名为`variables`的附加属性。

然而，不仅仅是增加对`variables`的支持，让`request`接受一个可选的第二个参数，一般命名为`queryOptions`。然后，我们将把任何给定的`queryOptions`(在我们的例子中是`variables`)传播到 POST 主体上。

```
function createClient(endpoint, headers = {}) {

  // Take in an optional queryOptions object
  let request = function(query, queryOptions = {}) {
    return fetch(endpoint, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        ...headers
      },
      // spread the passed in queryOptions onto the POST body
      body: JSON.stringify({ query, ...queryOptions })
    }).then(res => res.json());
  };

  return { request };
} 
```

仅此而已。我们最终的客户端工厂可以支持配置定制头以及 GraphQL 变量。

最终用法示例:

```
const POKEMON_BY_NAME_QUERY = `
query GetByName($name:String!) {
  pokemon(name:$name) {
    types 
    name
    image
    height {
      minimum
      maximum
    }
  }
}`;

let client = createClient("https://graphql-pokemon.now.sh", {
  Authorization: "Bearer ABC123"
});

async function getPokemonByName(name) {
  let {data,errors} = await client.request(POKEMON_BY_NAME_QUERY, { variables: { name } })
  if (errors) {
    console.log("Uh Oh!", errors.map(e => e.message));
  }
  return data.pokemon
}

getPokemonByName("Pikachu"); 
```