# fullstacking:中继+图形 QL

> 原文：<https://dev.to/heymarkkop/fullstacking-relay-graphql-175j>

现在我们已经将一切安排妥当，我们可以(重新)开始实现 **GraphQL** 和 **Relay** 。

我强烈建议你看一下《如何绘制 QL 基础图》的前 4 章，然后阅读一下《T2》的中继文档，以理解 Graph QL 和中继的一些概念。

## 设置图表 QL

首先我们将定义我们的`schema.graphql`。这个文件是用[模式定义语言(SDL)](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) 编写的，包含了 GraphQL 将要寻找的内容。
通常会有三种根类型:**查询**、**突变**和**订阅**。如果我们设置一个 **CRUD** ( **C** reate， **R** ead， **U** pdate， **D** elete)风格的 API，我们将拥有

*   **查询:**读取
*   **突变:**创建、更新、删除
*   **订阅:**订阅这些 CRUD 事件

除了根类型之外，它还会有一些**“对象”**类型来定义数据库中的对象。
在下面的例子中，我们将`schema.graphql`和**产品**的类型设置为必需的(！) **id** 和一个**头衔**。
我们还设置了一个名为 **"product"** 的查询，它需要一个 id 并返回一个**产品**类型。
我们还可以设置一个**“产品”**查询，返回一个**产品列表**

```
// packages/server/data/schema.graphql
// and a copy in packages/app/data/schema.graphql
type Product {
  id: ID!
  title: String
}

type Query {
  product(id: ID!): Product
  products: [Product]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须把这个**模式**写成 javascript，这样 **Koa** (通过`koa-graphql`)就可以用它作为**指令**(契约)在我们的数据库中查找数据。

你会注意到一些代码是如何转换的:
**！**as**GraphQLNonNull**
**ID**as**GraphQLID**
**String**as**GraphQLString**
an 以此类推

```
// packages/server/graphql/productType.js
const graphql = require('graphql');
const globalIdField = require('graphql-relay').globalIdField;

const {GraphQLObjectType, GraphQLString} = graphql;

const ProductType = new GraphQLObjectType({
  name: 'Product',
  fields: () => ({
    id: globalIdField('products'),
    title: {type: GraphQLString},
  }),
});

module.exports = ProductType; 
```

Enter fullscreen mode Exit fullscreen mode

```
// packages/server/graphql/schema.js
const { 
      GraphQLSchema, 
      GraphQLObjectType, 
      GraphQLID, 
      GraphQLList, 
      GraphQLNonNull,
      } = require('graphql');
const fromGlobalId = require('graphql-relay').fromGlobalId;
const productGraphQLType = require('./productType');
const Product = require('../models/Product');

const Query = new GraphQLObjectType({
  name: 'Query',
  fields: {
    product: {
      type: productGraphQLType,
      args: {id: {type: GraphQLNonNull(GraphQLID)}},
      resolve(parent, args) {
        return Product.findById(fromGlobalId(args.id).id);
      },
    },
    products: {
      type: GraphQLList(productGraphQLType),
      resolve() {
        return Product.find().lean();
      },
    },
  },
});

module.exports = new GraphQLSchema({
  query: Query,
}); 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们的**解析功能**。它们是将**模式**连接到**数据库**的函数。记住从`'../models/Product`导入的`Product`类是用**mongose**创建的，这就是它访问我们的 MongoDB 实例的方式。

## 反应原生

为了从 React 获得数据，我们将使用`babel-plugin-relay/macro`将`graphql`翻译成我们的请求。
我们还将使用一个名为`<QueryRenderer>`的**高阶组件**来渲染来自中继的数据。
一个**查询呈现器**组件将使用以下道具:

*   一个配置文件`Environment`
*   该查询
*   查询中使用的变量
*   一个渲染函数，返回 3 种情况:错误、成功和加载

```
// packages/app/src/App.js
import React, {Fragment} from 'react';
import {Text} from 'react-native';
import graphql from 'babel-plugin-relay/macro';
import {QueryRenderer} from 'react-relay';

import Environment from './relay/Environment';

const App = ({query}) => {
  const {products} = query;

  return (
    <Fragment>
      <Text>Hello World! Product: {products[0].title}</Text>
    </Fragment>
  );
};

const AppQR = () => {
  return (
    <QueryRenderer
      environment={Environment}
      query={graphql`
        query AppQuery {
          products {
            id
            title
          }
        }
      `}
      variables={{}}
      render={({error, props}) => {
        console.log('qr: ', error, props);
        if (error) {
          return <Text>{error.toString()}</Text>;
        }

        if (props) {
          return <App query={props} />;
        }

        return <Text>loading</Text>;
      }}
    />
  );
};

export default AppQR; 
```

Enter fullscreen mode Exit fullscreen mode

然而，要让`babel-plugin-relay`工作，你需要创建这个**脚本**来生成一个`schema.json`文件，这个文件将被一个**中继编译器**
读取

```
// packages/server/scripts/updateSchema.js #!/usr/bin/env babel-node --optional es7.asyncFunctions 
const fs = require('fs');
const path = require('path');
const schema = require('../graphql/schema');
const graphql = require('graphql').graphql;
const introspectionQuery = require('graphql/utilities').introspectionQuery;
const printSchema = require('graphql/utilities').printSchema;

// Save JSON of full schema introspection for Babel Relay Plugin to use
(async () => {
  const result = await graphql(schema, introspectionQuery);
  if (result.errors) {
    console.error(
      'ERROR introspecting schema: ',
      JSON.stringify(result.errors, null, 2),
    );
  } else {
    fs.writeFileSync(
      path.join(__dirname, '../data/schema.json'),
      JSON.stringify(result, null, 2),
    );

    process.exit(0);
  }
})();

// Save user readable type system shorthand of schema
fs.writeFileSync(
  path.join(__dirname, '../data/schema.graphql'),
  printSchema(schema),
); 
```

Enter fullscreen mode Exit fullscreen mode

您需要如下更改`babel.config.js`文件

```
// packages/app/babel.config.js
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: ['macros'], // add this
}; 
```

Enter fullscreen mode Exit fullscreen mode

每次使用`yarn update-schema`
改变模式时，你也需要运行这个`updateSchema.js`

```
// packages/server/package.json
...
  "scripts": {
    "start": "nodemon server.js",
    "update-schema": "babel-node --extensions \".es6,.js,.es,.jsx,.mjs,.ts\" ./scripts/updateSchema.js",
    "test": "jest"
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

```
// package.json
...
"scripts: {
   ... "update-schema": "yarn --cwd packages/server update-schema",
   ...
   },
... 
```

Enter fullscreen mode Exit fullscreen mode

## 继电器

环境配置应如下进行:

```
// packages/app/src/relay/Environment.js
import {Environment, Network, RecordSource, Store} from 'relay-runtime';

import fetchQuery from './fetchQuery';

const network = Network.create(fetchQuery);

const source = new RecordSource();
const store = new Store(source);

const env = new Environment({
  network,
  store,
});

export default env; 
```

Enter fullscreen mode Exit fullscreen mode

```
// packages/app/src/relay/fetchQuery.js
import {Variables, UploadableMap} from 'react-relay';
import {RequestNode} from 'relay-runtime';

export const GRAPHQL_URL = 'http://localhost:3000/graphql';

// Define a function that fetches the results of a request (query/mutation/etc)
// and returns its results as a Promise:
const fetchQuery = async (request, variables) => {
  const body = JSON.stringify({
    name: request.name, // used by graphql mock on tests
    query: request.text, // GraphQL text from input
    variables,
  });
  const headers = {
    Accept: 'application/json',
    'Content-type': 'application/json',
  };

  const response = await fetch(GRAPHQL_URL, {
    method: 'POST',
    headers,
    body,
  });

  return await response.json();
};

export default fetchQuery; 
```

Enter fullscreen mode Exit fullscreen mode

您还必须通过添加并运行`yarn relay`
来配置`relay-compiler`

```
"scripts":  {  "relay":  "relay-compiler --src ./src --schema ./schema.graphql"  } 
```

Enter fullscreen mode Exit fullscreen mode

## KoaJS

最后，为了将我们的 **GraphQL 服务器**服务到单个端点中，我们将使用`koa-mount`和`koa-graphql`以及我们的`schema.js`T5】

```
// packages/server/server.js
const Koa = require('koa');
const mount = require('koa-mount');
const graphqlHTTP = require('koa-graphql');
const schema = require('./graphql/schema');

const databaseUrl = "mongodb://127.0.0.1:27017/test";
mongoose.connect(databaseUrl, { useNewUrlParser: true });
mongoose.connection.once("open", () => {
  console.log(`Connected to database: ${databaseUrl}`);
});

const app = new Koa();

app.use(
  mount(
    '/graphql',
    graphqlHTTP({
      schema: schema,
      graphiql: true,
    }),
  ),
);

app.listen(3000, () =>
  console.log("Server is running on http://localhost:3000/")
); 
```

Enter fullscreen mode Exit fullscreen mode

## 跑步

您需要首先安装所有依赖项。

*   `app`包装内:

`yarn add react-relay`
T1】

*   `server`包装内:

`yarn add graphql koa-mount koa-graphql graphql-relay graphql-compiler`
T1】

并运行我们设置的脚本:
`yarn relay`
`yarn update-schema`

然后你可以运行一些在上一篇文章中设置的 yarn 命令。

`yarn start:server`(别忘了`sudo service mongod start` )
`yarn start:app`
`yarn android`

如果您让`Network error`与服务器和 mongodb 正确运行，您将需要**重定向**一些带有`adb reverse tcp:<portnumber> tcp: <portnumber>`
的端口，您可能希望在`packages/app/scripts/redirectPorts.sh`中添加以下脚本，在根`package.json`中添加`"redirect": "sh ./packages/app/scripts/redirectPorts.sh"`以便使用`yarn redirect`
使事情变得更容易

```
adb reverse tcp:8081 tcp:8081
adb reverse tcp:3000 tcp:3000
adb reverse tcp:5002 tcp:5002

adb -d reverse tcp:8081 tcp:8081
adb -d reverse tcp:3000 tcp:3000
adb -d reverse tcp:5002 tcp:5002

adb -e reverse tcp:8081 tcp:8081
adb -e reverse tcp:3000 tcp:3000
adb -e reverse tcp:5002 tcp:5002 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。您应该在视图中看到“Stampler”。

## 参考文献:

*   【JavaScript 的 GraphQL 参考实现
*   [Relay——用于构建数据驱动的 React 应用程序的 JavaScript 框架](https://relay.dev/docs/en/introduction-to-relay)
*   [graph QL 的全栈教程](https://www.howtographql.com/)