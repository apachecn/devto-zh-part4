# Lambda 函数图 SQL 解析器

> 原文：<https://dev.to/dabit3/lambda-function-graphql-resolvers-11cd>

[![](img/4cf0beec4e52ef92adec188d568acc99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hucOdQHh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ll6s30yjcw5n5ufb6h7a.jpg)

Amplify CLI [最近增加了对直接从 Amplify 环境为 AppSync APIs 部署 Lambda GraphQL 解析器的支持](https://aws.amazon.com/blogs/mobile/amplify-framework-adds-support-for-aws-lambda-functions-and-amazon-dynamodb-custom-indexes-in-graphql-schemas/)。Lambda 函数解析器允许您用 JavaScript 编写 AppSync 解析器逻辑。

使用`@function`指令，您可以指定与 GraphQL 模式中的 Lambda 函数交互的操作:

```
type  Mutation  {  addEntry(id:  Int,  email:  String):  String  @function(name:  "addEntry-${env}")  } 
```

Enter fullscreen mode Exit fullscreen mode

在本教程中，我将教你如何创建一个使用两种 Lambda 解析器的应用程序:

1.  Lambda 解析器与另一个 API 对话，并通过查询返回 GraphQL 响应

2.  一个 Lambda 解析器，它发送查询和突变来与真实的 NoSQL 数据库进行交互，以对其执行创建和读取操作。

在本教程结束时，您应该理解如何使用 Amplify 框架部署与 Lambda GraphQL 解析器交互的 AppSync GraphQL API。

> 要查看该项目的最终源代码，请单击此处的。

## 入门

首先，您需要创建一个新的 React 应用程序，并在其中初始化一个新的 Amplify 项目:

```
npx create-react-app gql-lambda

cd gql-lambda

amplify init

# Follow the steps to give the project a name, environment name, and set the default text editor.
# Accept defaults for everything else and choose your AWS Profile. 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您尚未安装和配置 Amplify CLI，请遵循此处的说明[。](https://aws-amplify.github.io/docs/cli/concept)

接下来，安装 AWS Amplify 库:

```
npm install aws-amplify 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 API

我们将创建的第一个 GraphQL API 将从另一个 REST API 查询数据并返回一个 GraphQL 响应。您将与之交互的 API 是 [Coinlore API](https://www.coinlore.com/cryptocurrency-data-api) 。

让我们首先创建函数:

```
amplify add function

? Provide a friendly name for your resource to be used as a label for this category in the project: currencyfunction
? Provide the AWS Lambda function name: currencyfunction
? Choose the function runtime that you want to use: NodeJS
? Choose the function template that you want to use: Hello world
? Do you want to access other resources created in this project from your Lambda function? N
? Do you want to invoke this function on a recurring schedule? N
? Do you want to edit the local lambda function now? Y 
```

Enter fullscreen mode Exit fullscreen mode

用以下代码更新函数:

```
// amplify/backend/function/currencyfunction/src/index.js
const axios = require('axios')

exports.handler = function (event, _, callback) {
  let apiUrl = `https://api.coinlore.com/api/tickers/?start=1&limit=10`

  if (event.arguments) { 
    const { start = 0, limit = 10 } = event.arguments
    apiUrl = `https://api.coinlore.com/api/tickers/?start=${start}&limit=${limit}`
  }

  axios.get(apiUrl)
    .then(response => callback(null, response.data.data))
    .catch(err => callback(err))
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的函数中，我们使用了 [axios](https://github.com/axios/axios) 库来调用另一个 API。为了使用 axios，我们需要将其安装在 function 文件夹中。我们还将安装`uuid`以备后用:

```
cd amplify/backend/function/currencyfunction/src

npm install axios uuid

cd ../../../../../ 
```

Enter fullscreen mode Exit fullscreen mode

既然已经创建了函数，我们需要创建 GraphQL API。为此，运行 Amplify `add`命令:

```
amplify add api

? Please select from one of the below mentioned services: GraphQL
? Provide API name: currencyapi
? Choose an authorization type for the API: API key
? Enter a description for the API key: public
? After how many days from now the API key should expire (1-365): 365 (or your preferred expiration)
? Do you want to configure advanced settings for the GraphQL API: N
? Do you have an annotated GraphQL schema? N
? Do you want a guided schema creation? Y
? What best describes your project: Single object with fields
? Do you want to edit the schema now? Y 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在**amplify/back end/API/current API/schema . graph QL**中，用下面的代码更新模式:

```
type  Coin  {  id:  String!  name:  String!  symbol:  String!  price_usd:  String!  }  type  Query  {  getCoins(limit:  Int  start:  Int):  [Coin]  @function(name:  "currencyfunction-${env}")  } 
```

Enter fullscreen mode Exit fullscreen mode

现在 API 和 Lambda 函数都已经创建好了。要部署它们并使它们生效，您可以运行`push`命令:

```
amplify push

Current Environment: dev

| Category | Resource name    | Operation | Provider plugin   |
| -------- | -------------    | --------- | ----------------- |
| Api      | currencyapi      | Create    | awscloudformation |
| Function | currencyfunction | Create    | awscloudformation |
? Are you sure you want to continue? (Y/n) Y 
```

Enter fullscreen mode Exit fullscreen mode

现在，资源已经部署完毕，您可以尝试查询了！您可以在 AWS AppSync 控制台中测试该查询。要打开 API 仪表板，请在您的终端中运行以下命令:

```
amplify console api

? Please select from one of the below mentioned services: GraphQL 
```

Enter fullscreen mode Exit fullscreen mode

在查询编辑器中，运行以下查询:

```
# basic request  query  listCoins  {  getCoins  {  price_usd  name  id  symbol  }  }  # request with arguments  query  listCoinsWithArgs  {  getCoins(limit:3  start:  10)  {  price_usd  name  id  symbol  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这个查询应该返回一组加密货币信息。

## 更新 API 以对 NoSQL 数据库执行 CRUD 操作

既然基本的 API 已经启动并运行，那么让我们创建一个数据库并更新 API 以对其执行创建和读取操作。

首先，我们将创建数据库:

```
amplify add storage

? Please select from one of the below mentioned services: NoSQL Database
? Please provide a friendly name for your resource that will be used to label this category in the project: currencytable
? Please provide table name: currencytable
? What would you like to name this column: id
? Please choose the data type: string
? Would you like to add another column? Y
? What would you like to name this column: name
? Please choose the data type: string
? Would you like to add another column? Y
? What would you like to name this column: symbol
? Please choose the data type: string
? Would you like to add another column? Y
? What would you like to name this column: price_usd
? Please choose the data type: string
? Would you like to add another column? N
? Please choose partition key for the table: id
? Do you want to add a sort key to your table? N
? Do you want to add global secondary indexes to your table? N
? Do you want to add a Lambda Trigger for your Table? N 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们更新函数以使用新的数据库。

```
amplify update function

? Please select the Lambda Function you would want to update: currencyfunction
? Do you want to update permissions granted to this Lambda function to perform on other resources in your project? Y
? Select the category: storage
? Select the operations you want to permit for currencytable:
  ◉ create
  ◉ read
  ◉ update
 ❯◉ delete
? Do you want to invoke this function on a recurring schedule? N
? Do you want to edit the local lambda function now? Y 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将更新 lambda 函数。现在函数代码只存在于一个文件中，即位于**amplify/back end/function/currency function/src/index . js**的 **index.js** 。在 **src** 文件夹中，新建两个文件: **createCoin.js** 和 **getCoins.js** 。在接下来的步骤中，我们将更新 **index.js** ，并使用代码填充另外两个新文件。

#### index.js

```
const getCoins = require('./getCoins')
const createCoin = require('./createCoin')

exports.handler = function (event, _, callback) {
  if (event.typeName === 'Mutation') {
    createCoin(event, callback)
  }
  if (event.typeName === 'Query') {
    getCoins(callback)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在函数的`event`参数中，有一个`typeName`字段，它将告诉我们操作是突变还是查询。还有一个`fieldName`参数，如果您有多个查询或变异，它会告诉您实际执行的字段。

我们将使用`typeName`字段根据操作类型调用`createCoin`或`getCoins`。

#### getCoins.js

```
const AWS = require('aws-sdk')
const region = process.env.REGION
const storageCurrencytableName = process.env.STORAGE_CURRENCYTABLE_NAME
const docClient = new AWS.DynamoDB.DocumentClient({region})

const params = {
  TableName: storageCurrencytableName
}

function getCoins(callback) {
  docClient.scan(params, function(err, data) {
    if (err) {
      callback(err)
    } else {
      callback(null, data.Items)
    }
  });
}

module.exports = getCoins 
```

Enter fullscreen mode Exit fullscreen mode

在 **getCoins** 中，我们调用 DynamoDB `scan`操作来读取数据库并返回数组中的所有值。我们还使用了 [DynamoDB。DocumentClient](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html) sdk 使用 JavaScript 简化 Amazon DynamoDB 中的项目。

#### create oin . js

```
const AWS = require('aws-sdk')
const { v4: uuid } = require('uuid')
const region = process.env.REGION
const ddb_table_name = process.env.STORAGE_CURRENCYTABLE_NAME
const docClient = new AWS.DynamoDB.DocumentClient({region})

function write(params, event, callback){
  docClient.put(params, function(err, data) {
    if (err) {
      callback(err)
    } else {
      callback(null, event.arguments)
    }
  })
}

function createCoin(event, callback) {
  const args = { ...event.arguments, id: uuid() }
  var params = {
    TableName: ddb_table_name,
    Item: args
  };

  if (Object.keys(event.arguments).length > 0) {
    write(params, event, callback)
  } 
}

module.exports = createCoin 
```

Enter fullscreen mode Exit fullscreen mode

在 **createCoin** 中，我们对传入参数的 DynamoDB 表执行`putItem`操作。我们还在服务器上自动生成 ID，使用 **uuid** 库为商品填充一个惟一的 ID。

最后，我们将在**amplify/back end/API/currency API/Schema . GraphQL**更新 graph QL 模式，以添加变异定义:

```
# amplify/backend/api/currencyapi/schema.graphql  type  Coin  {  id:  String!  name:  String!  symbol:  String!  price_usd:  String!  }  type  Query  {  getCoins(limit:  Int  start:  Int):  [Coin]  @function(name:  "currencyfunction-${env}")  }  # new mutation definition  type  Mutation  {  createCoin(name:  String!  symbol:  String!  price_usd:  String!):  Coin  @function(name:  "currencyfunction-${env}")  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，部署变更:

```
amplify push 
```

Enter fullscreen mode Exit fullscreen mode

## 试探一下

现在，资源已经部署完毕，您可以尝试查询了！您可以在 AWS AppSync 控制台中测试该查询。要打开您的项目，请在您的终端中运行以下命令:

```
amplify console api

? Please select from one of the below mentioned services: GraphQL 
```

Enter fullscreen mode Exit fullscreen mode

测试以下查询:

```
query  listCoins  {  getCoins  {  price_usd  name  id  symbol  }  }  mutation  createCoin  {  createCoin(  name:  "Monero"  price_usd:  "86.85"  symbol:  "XMR"  )  {  name  price_usd  symbol  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 在客户端进行测试

如果您想在 React 应用程序中测试它，您可以使用 Amplify:
中的`API`类别

```
import { API, graphqlOperation } from 'aws-amplify'
import { getCoins } from './graphql/queries'
import { createCoin } from './graphql/mutations'

// mutation
const coin = { name: "Bitcoin", symbol: "BTC", price: "10000" }
API.graphql(graphqlOperation(createCoin, coin))
  .then(data => console.log({ data }))
  .catch(err => console.log('error: ', err))

// query
API.graphql(graphqlOperation(getCoins))
  .then(data => console.log({ data }))
  .catch(err => console.log('error: ', err)) 
```

Enter fullscreen mode Exit fullscreen mode

> 要查看该项目的最终源代码，请单击此处的。