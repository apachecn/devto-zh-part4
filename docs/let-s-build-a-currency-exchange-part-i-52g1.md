# 让我们建立一个货币兑换第一部分

> 原文：<https://dev.to/marlonanthony/let-s-build-a-currency-exchange-part-i-52g1>

我开始这个项目有两个目标:

1.  巩固我对 GraphQL 的理解。

2.  学习并实现 Apollo 服务器/客户端。

我决定深入研究 Apollo，更好地理解它的用例，并找出如何让它与其他技术配合得更好。在我看来，学习一项技术的最好方法是用这项技术做点什么——然后写下来。

我们将建立一个有`GraphQL`服务器的货币交易所。我们将使用`MongoDB/Mongoose`来保存我们的数据。我们将在后端实现`ApolloServer`，在前端实现`ApolloClient`。`ApolloClient`将为我们提供我们的远程数据— `React`，我们的本地状态。Apollo 提供了一个我们将在前端使用的`InMemoryCache`。

我们将从 [Alpha Vantage 财务 API](https://www.alphavantage.co) 中提取货币数据。如果您正在寻找更广泛的选择，[这篇评论文章](https://medium.com/@patrick.collins_58673/stock-api-landscape-5c6e054ee631)还涵盖了其他股票和货币 API 供您考虑。有了这些数据，我们将允许用户买入或卖出多头货币对。稍后，我们将使用`Chartjs`来实现一个图表。😉

我们开始工作吧！

我们需要安装一些依赖项。当我们需要它们时，我将深入检查每一个，但是现在让我们只安装它们。

创建一个新的项目文件夹，并在您的终端中运行以下命令:

```
 npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了 package.json 文件，让我们开始安装我们的依赖项。

首先，让我们将 nodemon 安装为一个开发依赖项。

```
 npm i -D nodemon 
```

Enter fullscreen mode Exit fullscreen mode

现在为其余的:

```
 npm i apollo-datasource apollo-datasource-rest apollo-server-express bcryptjs express express-session graphql isemail mongoose 
```

Enter fullscreen mode Exit fullscreen mode

进入`package.json`，删除测试脚本并添加以下内容:

```
 "scripts": {
    "start": "nodemon index.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

创建一个`index.js`文件并添加下面的代码:

```
 // index.js

  const app = require('express')()

  app.get('/', (req, res) => res.send('Hello world!'))

  const PORT = 4000

  app.listen(PORT, () => console.log(`Server running on port ${PORT}`)) 
```

Enter fullscreen mode Exit fullscreen mode

在您的终端中键入`npm start`,然后前往 localhost:4000。问候你应该是一个古老而庄严的仪式，那就是，“你好，世界！”抛开仪式上的细节，让我们开始阿波罗计划。

现在我们只是运行一个快速服务器。Apollo 不要求我们安装 express。我决定这样做的原因是因为我想整合`express-session`。出于这个原因，我们使用了`express`和`apollo-server-express`，而不是`apollo-server`。

前往 [Alpha Vantage](https://www.alphavantage.co/,) 并获取您的 API 密钥。很简单。点击绿色的“立即获取免费 API 密匙”按钮，一切就绪。

[![What is Apollo](img/03e6b1b7e1e5ddc1703083621e411b95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hBboKqqi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/WhatisApollo.png%3Fraw%3Dtrue)

当我们开始使用它的时候，阿波罗的优势就会显现出来。转至`index.js`并进行以下调整:

```
 // index.js

  const app = require('express')()
  const { ApolloServer } = require('apollo-server-express')

  const typeDefs = require('./typeDefs')
  const resolvers = require('./resolvers') 
  const CurrencyAPI = require('./datasources/currencies')

  const server = new ApolloServer({ 
    typeDefs,
    resolvers,
    dataSources: () => ({
      currencyAPI: new CurrencyAPI() 
    })
  })

  server.applyMiddleware({ app })

  app.listen(PORT, () => {
    console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们从`apollo-server-express`导入`ApolloServer`类，并在名为`server`的变量中存储一个新实例。我们还导入了三个我们仍然需要创建的本地文件。我们将 GraphQL 模式(或 typeDefinitions)传递给`ApolloServer`配置对象的`typeDefs`属性。我们对我们的解析器和`dataSources`做同样的事情(这将被详细解释)。之后，我们暂时将`app`作为我们唯一的中间件。

创建一个名为`typeDefs.js`的新文件，并添加以下内容:

```
 // typeDefs.js

  const { gql } = require('apollo-server-express')

  const typeDefs = gql`
    type Query {
      currencyPairInfo(fc: String, tc: String): PairDisplay!
    }

    type PairDisplay {
      fromCurrency: String!
      fromCurrencyName: String
      toCurrency: String!
      toCurrencyName: String
      exchangeRate: String
      lastRefreshed: String
      timeZone: String
      bidPrice: String
      askPrice: String
    }
  `

  module.exports = typeDefs 
```

Enter fullscreen mode Exit fullscreen mode

与 REST API 不同，GraphQL 只使用一条路线。您不需要为每个任务 ping 不同的端点。相反，模式(或 typeDefs)确切地描述了您想要什么数据以及您想要如何接收它。

使用 GraphQL 时，有三件事你必须理解:查询、变异和解析器。一切都围绕着他们。你可以把它想象成 GraphQL 查询描述你如何`get`数据，GraphQL 变异描述你如何变异(post/put/delete)数据。您准确地描述了需要什么变量(如果有的话)以及响应应该是什么样子。解析器只是处理查询和变异执行的函数。

声明你想要什么，然后写函数来实现它。

在上面的查询中，我们让 GraphQL 知道每当我们请求`currencyPairInfo`时，这两个参数可能会也可能不会被提供。在冒号之后，我们声明响应应该以我们命名为`PairDisplay`的`type`所描述的形状返回。结尾的感叹号声明需要此响应。

我没有要求`currencyPairInfo`的参数，因为我们将在请求上设置默认参数。我们将把默认参数`fc` (fromCurrency)设置为 EUR，将默认参数`tc` (toCurrency)设置为 USD。如果我们希望这些参数是必需的，我们只需在`type`后面加上一个感叹号，就像这样:`String!`。

让我们添加我们的解析器。创建一个名为`resolvers.js`的新文件，并添加以下代码:

```
// resolvers.js

const resolvers = {
  Query: {
    currencyPairInfo: async (_, { fc, tc }, { dataSources }) => {
      try {
        const currencyPairs = await dataSources.currencyAPI.getCurrencyPair(fc, tc)
        return currencyPairs
      } catch (error) { throw err }
    }
  }
}

module.exports = resolvers 
```

Enter fullscreen mode Exit fullscreen mode

在 GraphQL 中，解析器可以访问`context`。上下文是所有解析器共享的对象。它有助于跟踪诸如认证信息、当前用户、数据库连接和数据源等信息。上下文可作为每个解析器的第三个参数。

解析器函数签名:

1.  第一个参数= parent。
2.  第二个参数= args。
3.  第三个参数=上下文。

保持解析器干净简洁被认为是最佳实践，所以我们将繁重的工作抽象到另一个文件中。这是我们导入到`index.js`中的文件，仍然需要创建。数据源可以访问 GraphQL 上下文。这就是为什么我们不需要把它导入 resolvers.js，我们只需要从上下文对象中析构它。

创建一个名为`datasources`的新文件夹。在里面新建一个文件，命名为`currencies.js`。添加下面的代码:

```
// currencies.js

const { RESTDataSource } = require('apollo-datasource-rest') 
const keys = require('../config/keys')

class CurrencyAPI extends RESTDataSource {
  constructor() {
    super() 
    this.baseURL = ''
  }

  async getCurrencyPair(fc='EUR', tc='USD') {
    try {
      const data = await this.get(`https://www.alphavantage.co/query?
function=CURRENCY_EXCHANGE_RATE&from_currency=${fc} &to_currency=${tc}&apikey=${keys.alphaVantageAPIKey}`),
            response = data['Realtime Currency Exchange Rate'],
            fromCurrency = response['1\. From_Currency Code'],
            fromCurrencyName = response['2\. From_Currency Name'],
            toCurrency = response['3\. To_Currency Code'],
            toCurrencyName = response['4\. To_Currency Name'],
            exchangeRate = response['5\. Exchange Rate'],
            lastRefreshed = response['6\. Last Refreshed'],
            timeZone = response['7\. Time Zone'],
            bidPrice = response['8\. Bid Price'],
            askPrice = response['9\. Ask Price']
      return data && response && {
          fromCurrency,
          fromCurrencyName,
          toCurrency,
          toCurrencyName,
          exchangeRate,
          lastRefreshed,
          timeZone,
          bidPrice,
          askPrice
        }
    } catch (err) { throw err }
  }
}

module.exports = CurrencyAPI 
```

Enter fullscreen mode Exit fullscreen mode

我们从`apollo-datasource-rest`导入`RESTDataSource`。我们扩展这个类(创建一个子类)来定义我们的数据源。Apollo 数据源是一个类，它封装了所有的数据获取逻辑，以及特定服务的缓存和重复数据删除。

从文档中:

> Apollo RESTDataSource 还设置了一个内存缓存，用于缓存来自 REST 资源的响应，无需额外设置。我们称之为部分查询缓存。这个缓存的伟大之处在于，您可以重用 REST API 公开的现有缓存逻辑。Apollo REST 数据源也有对应于 HTTP 动词的 helper 方法，比如`GET`和`POST`。

一旦我们到达`ApolloClient`，我们将更详细地讨论这个缓存。

这个文件所做的就是从 Alpha Vantage API 中获取一些数据。我们扩展了`RESTDataSource`类，并在构造函数中初始化了`baseURL`。`baseURL`是阿波罗赐予我们的屈膝礼。一个简单的例子来说明这是如何有用的，如果我们在这个类中有两个方法，它们必须命中同一个 URL 的不同端点。

例如:

```
 constructor() {
    super()
    this.baseURL = 'https://github.com/'
  }

  // Later in some method
  this.get('marlonanthony') // https://github.com/marlonanthony

  // In some other method
  this.get('peggyrayzis') // https://github.com/peggyrayzis 
```

Enter fullscreen mode Exit fullscreen mode

您也可以动态设置 URL。让我们来看看文档中的一个例子:

```
 get baseURL() {
    if (this.context.env === 'development') {
      return 'https://movies-api-dev.example.com/';
    } else {
      return 'https://movies-api.example.com/';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在构造函数之后，我们实现了在解析器中调用的方法`getCurrencyPair`。这个方法负责获取我们的实时货币汇率数据。我们利用 Alpha Vantage 给我们的 URL，添加我们的参数和 API 密钥。

Alpha Vantage API 是免费的，这意味着方便。也就是说，它们的命名约定有点愚蠢，需要我们使用括号符号，因此显得冗长。

默认情况下，阿波罗服务器支持 [GraphQL Playground](http://localhost:4000/graphql) 。Playground 是一个交互式的浏览器内 GraphQL IDE，用于探索您的模式和测试您的查询/变化。想想邮差，而不是 GraphQL。

用`npm start`启动您的服务器。然后转到 localhost:4000/graphql 看一看。

[![currenyPairInfo query](img/1d4b7ee55513bcca0db2acb431a13ab9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J2wVJRvI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/gqlplayground.png%3Fraw%3Dtrue)

在播放按钮的左边，我们声明我们想要`query`一些数据。然后我们解释哪个查询并提供必要的参数。如果您按 control +空格键(在 Mac 上),您应该会得到关于您的模式的自动完成建议。之后，我们声明我们想要返回的数据。一旦你按下播放按钮，你会在操场的右半部分看到回应。

在我们的`getCurrencyPair`方法中，我们定义了这个查询可能返回的所有内容。GraphQL 和 REST 之间的区别在于，如果我们愿意，我们可以将我们的请求限制到我们喜欢的任何数据片段。

太棒了。我们从 Alpha Vantage API 中获取实时货币汇率！也就是说，我们还没说完。我前面说过，我们将实现一个图表来显示货币对数据的每月时间序列。为此，我们需要向 CurrencyAPI 类添加另一个方法。

```
 // currencies.js

  async getMonthlyTimeSeries(fc='EUR', tc='USD') {
    try {
      const data = await this.get(`https://www.alphavantage.co/query?
function=FX_MONTHLY&from_symbol=${fc}&to_symbol=${tc}&apikey=${keys.alphaVantageAPIKey}`),
            timeSeries = data && data['Time Series FX (Monthly)'],
            timesArray = timeSeries && Object.keys(timeSeries).reverse(),
            valuesArray = timeSeries && Object.values(timeSeries).map(val => val['4\. close']).reverse()

      return { timesArray, valuesArray }

    } catch (error) { throw error }
  } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们利用不同的阿尔法优势端点。我们像以前一样提供参数和 API 密钥。我们返回一个包含两个数组的对象，即 timesArray (x 轴)和 valuesArray (y 轴)。这就是我们的图表所需要的。

我们需要创建一个解析器来调用这个方法，并向我们的 typeDefs 添加一个查询。进入`typeDefs.js`，将查询类型调整为:

```
// typeDefs.js 

  type Query {
    currencyPairInfo(fc: String, tc: String): PairDisplay!
    monthlyTimeSeries(fc: String, tc: String): TimeSeries!
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们期望收到一个 fromCurrency ( `fc`)和 toCurrency ( `tc`)参数。同样，我们不需要参数，因为我们只是在请求中设置默认参数。我选择这样做的原因是，当用户导航到图表时，页面将加载数据，而不是空白，直到用户输入货币对。

我们的 monthlyTimeSeries 查询要求我们返回类型为`TimeSeries`的数据。让我们确切地定义这是什么。将以下类型添加到`typeDefs.js` :

```
// typeDefs.js 

  type TimeSeries {
    timesArray: [String!]!
    valuesArray: [String!]!
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们声明必须返回两个数组，并且这些数组必须用字符串填充。字符串和数组都是必需的。).

最后，让我们添加解析器。调整`resolvers.js`，使其类似如下:

```
// resolvers.js

  const resolvers = {
    Query: {
      currencyPairInfo: async (_, { fc, tc }, { dataSources }) => {
        try {
          const currencyPairs = await dataSources.currencyAPI.getCurrencyPair(fc, tc)
          return currencyPairs
        } catch (error) { throw err }
      },
      monthlyTimeSeries: async (_, { fc, tc }, { dataSources }) => {
        try {
          const timeSeries = await dataSources.currencyAPI.getMonthlyTimeSeries(fc, tc)
          return timeSeries
        } catch (error) { throw error }
      }
    }
  }

module.exports = resolvers 
```

Enter fullscreen mode Exit fullscreen mode

打开 GraphQL Playground，查询`monthlyTimeSeries`。

[![montly time series](img/e76ba8c9774f9631027fdb8c5659efee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QZZGBVGq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/mtseries.png%3Fraw%3Dtrue)

GraphQL 模式现在应该变得清晰了。

*   创建一个查询/变异。
*   创建解析器来处理所述查询/变异。

就这样，我们完成了 Alpha Vantage API！

我们慢慢熟悉了 GraphQL 和 Apollo。让我们更轻松地处理身份验证问题。处理认证/授权是一个广泛讨论的话题。我们将只关注与 Apollo 的集成。

我们应该做的第一件事是创建一个数据库。我们将使用 MongoDB/mongose。前往 [MongoDB 图集](https://www.mongodb.com/cloud/atlas)报名/签到。用 Atlas 创建远程数据库相当简单。登录后，单击“新建项目”按钮。从这里，只需选择您的云提供商，选择您的地区，并命名您的集群。构建好集群后，单击 connect 按钮。将您的 IP 地址列入白名单，并为该项目创建一个管理员用户。选择“连接您的应用程序”选项，并复制提供的连接字符串。最后，点击“收藏”按钮。这是我们将看到数据的地方。

用您的用户密码替换您的连接字符串中的`<password>`,但是将它存储在一个变量中，并将其放在一个 env 文件或 config 文件夹中。只要不推给 GitHub。

让我们连接到数据库并定义我们的用户模式。回到`index.js`导入`mongoose`，导入你的 MongoDB 密码，然后将`index.js`调整为如下:

```
// index.js

const app = require('express')()
const { ApolloServer } = require('apollo-server-express')
const mongoose = require('mongoose')

const typeDefs = require('./typeDefs')
const resolvers = require('./resolvers') 
const CurrencyAPI = require('./datasources/currencies')
const { mongoPassword } = require('./config/keys')

const server = new ApolloServer({ 
  typeDefs,
  resolvers,
  dataSources: () => ({
    currencyAPI: new CurrencyAPI() 
  })
})

server.applyMiddleware({ app })

mongoose
.connect(`mongodb+srv://marlon:${mongoPassword}@cluster0-o028g.mongodb.net/forex?retryWrites=true&w=majority`, { useNewUrlParser: true })
.then(() => app.listen(4000, () => {
  console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`)
})).catch(err => console.log(err)) 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到，在 URL 的末尾，我们添加了一点配置来消除讨厌的 MongoDB/mongose 警告。一旦您保存`index.js`，我们将连接到我们的数据库。

现在来看模式。创建一个名为`models`的文件夹。在`models`中创建一个名为`User.js`的新文件，并插入以下内容:

```
// User.js

const mongoose = require('mongoose') 
const Schema = mongoose.Schema

const User = new Schema({
  email: {
    type: String,
    required: true 
  },
  password: {
    type: String,
    required: true 
  },
  name: {
    type: String,
    required: true 
  },
  bankroll: {
    type: Number,
    default: 1000000,
    required: true
  },
  pairs: [
    {
      type: Schema.Types.ObjectId,
      ref: 'Pair'
    }
  ]
}, {
  timestamps: true
})

module.exports = mongoose.model('User', User) 
```

Enter fullscreen mode Exit fullscreen mode

我们从`mongoose`导入了`Schema`类，并创建了一个新的实例，命名为`User`。之后，我们定义我们的模式。每个用户都会有一个 MongoDB 赋予他们的 ID，所以我们不需要定义它。用户必须提供电子邮件、密码和姓名。我们给每个用户 100 万美元——因为我们可以。每个用户都希望跟踪他们已经开立的货币对头寸。我们分配一个`pairs`属性，为用户打开的每一对提供一个 id 数组。最后，通过向我们的模式添加`timestamps: true`，Mongoose 为我们提供了两个属性:`createdAt`和`updatedAt`。

在 models 文件夹中创建一个新文件，并将其命名为`Pair.js`。在里面写下下面的代码:

```
// Pair.js

const mongoose = require('mongoose') 
const Schema = mongoose.Schema

const Pair = new Schema({
  user: {
    type: Schema.Types.ObjectId,
    ref: 'User'
  },
  pair: {
    type: String,
    required: true 
  },
  lotSize: {
    type: Number,
    required: true 
  },
  position: {
    type: String,
    required: true 
  },
  openedAt: {
    type: Number,
    required: true 
  },
  closedAt: {
    type: Number,
  },
  pipDif: {
    type: Number,
  },
  profitLoss: {
    type: Number
  },
  open: {
    type: Boolean,
    required: true,
    default: false
  }
}, {
  timestamps: true
})

module.exports = mongoose.model('Pair', Pair) 
```

Enter fullscreen mode Exit fullscreen mode

我们将用户 ID 存储在一个名为`user`的属性中。`pair`属性看起来类似这样:EUR/USD。`lotSize`是用户放在头寸上的金额。`position`不是“长”就是“短”`pipDif`稍后将详细解释，但现在只需知道我们将如何计算货币对之间的相对价值差异，以及头寸的盈亏。`open`通知我们该头寸是否已经平仓。

打开`typeDefs.js`，增加两种类型:`User`和`Pair`。

```
// typeDefs.js

  type User {
    id: ID!
    email: String!
    name: String!
    bankroll: Float!
    pairs: [Pair]
    createdAt: String!
    updatedAt: String!
  }

  type Pair {
    id: ID!
    user: ID!
    pair: String!
    lotSize: Int!
    position: String!
    openedAt: Float!
    closedAt: Float
    pipDif: Float
    profitLoss: Float
    open: Boolean!
    createdAt: String!
    updatedAt: String!
  } 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，如果模型模式中需要某些东西，那么 GraphQL 模式中也应该需要。

是时候加入我们的第一个突变了。在`typeDefs.js`内添加`Mutation`类型。

```
// typeDefs.js

  type Mutation {
    register(email: String!, password: String!, name: String!): Boolean!
  } 
```

Enter fullscreen mode Exit fullscreen mode

用户必须提交电子邮件、密码和他们的名字。我们根据用户注册的成功返回 true 或 false。

我们已经处理了 typeDefs，现在是解析器。我们需要向 resolvers 对象添加一个`Mutation`属性。

```
// resolvers.js

const resolvers = {
  Query: {
    currencyPairInfo: async (_, { fc, tc }, { dataSources }) => {
      try {
        const currencyPairs = await dataSources.currencyAPI.getCurrencyPair(fc, tc)
        return currencyPairs
      } catch (error) { throw err }
    },
    monthlyTimeSeries: async (_, { fc, tc }, { dataSources }) => {
      try {
        const timeSeries = await dataSources.currencyAPI.getMonthlyTimeSeries(fc, tc)
        return timeSeries
      } catch (error) { throw error }
    }
  },

  Mutation: {
    register: async (_, { email, password, name }, { dataSources }) => {
      try {
        const newUser = await dataSources.userAPI.createNewUser({ email, password, name })
        return newUser
      } catch (error) { throw error }
    },
  }
}

module.exports = resolvers 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们保持解析器干净，并将繁重的工作抽象到另一个文件中。但是什么文件？`RESTDataSource`负责从 REST API 获取数据。这不是我们在这里做的。Apollo 允许我们用通用的`apollo-datasource`包创建定制数据源。这是我们将要使用的。

在`datasources`文件夹中创建一个新文件，并将其命名为`user.js`。

```
// user.js

const { DataSource } = require('apollo-datasource')
const { UserInputError } = require('apollo-server-express')
const isEmail = require('isemail')
const bcrypt = require('bcryptjs')

const User = require('../models/User') 

class UserAPI extends DataSource {

  // gain access to the GraphQL context
  initialize(config) {
    this.context = config.context
  }

  async createNewUser({ email, password, name }) {
    try {
      if(!isEmail.validate(email)) { throw new UserInputError('Invalid Email!') }
      const existingUser = await User.findOne({ email })
      if(existingUser) { throw new UserInputError('User already exist!') }
      const hashedPassword = await bcrypt.hash(password, 12)
      const user = await new User({
        name,
        email,
        password: hashedPassword
      })
      await user.save()
      return true 
    } catch (error) { throw error }
  }
}

module.exports = UserAPI 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们导入阿波罗的`DataSource`类。然后我们通过扩展`DataSource`创建一个子类，并将其命名为`UserAPI`。通过添加`initialize`函数，Apollo 允许我们从这个类内部访问上下文。这是一个由 ApolloServer 在设置时调用的函数。这个函数通过数据源配置来调用，包括缓存和上下文。这允许我们利用`this.context`，授予我们访问请求上下文的权限，因此我们可以知道发出请求的用户。

我们也从`apollo-server-express`导入`UserInputError`。这允许我们区分错误类型。阿波罗客户端区分了两种错误:`graphQLErrors`和`networkError`。我们来看看阿波罗团队去年写的一篇博文。

[![graphQLError vs networkError](img/666a06a9f211c94c46a591b7d7e1e98b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f5ArChQu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/graphQLErrorvsnetworkError.png%3Fraw%3Dtrue)

我们的解析器中抛出的这些 graphQLErrors 怎么办？还是那句话，我们来看看这篇[博文](https://blog.apollographql.com/full-stack-error-handling-with-graphql-apollo-5c12da407210)。

[![resolver errors](img/cbb0c8479b3f332f76850e3719e8d840.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sySfSdQv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/apolloServerErrors.png%3Fraw%3Dtrue)

我们导入`isemail`以确保提供了有效的电子邮件。在将用户密码保存到数据库之前，我们还导入了`bcrypt`来散列用户密码。最后，我们导入我们的用户模式。

转到 index.js 并导入我们新创建的数据源。然后将我们的 UserAPI 类的一个新实例添加到 ApolloServer 的配置对象:

```
// index.js

const UserAPI = require('./datasources/user')

const server = new ApolloServer({ 
  typeDefs,
  resolvers,
  dataSources: () => ({
    currencyAPI: new CurrencyAPI(),
    userAPI: new UserAPI()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

保存您的文件，看看 GraphQL 操场。

[![register mutation](img/f8a96793737becfd1d490231ec0023d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lg-feiLW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/register2.png%3Fraw%3Dtrue)

如果您尝试注册同一个用户两次，您应该得到我们之前定义的`UserInputError`(“用户已经存在！”).您还应该能够在数据库中看到我们新创建的用户。现在我们可以注册用户了，让我们让他们登录。

我们将使用`express-session`来跟踪我们的用户。这个想法是，一旦用户成功登录，我们将把用户 id 附加到请求对象的会话上。我们将在解析器中访问上下文对象的请求对象，或者通过`UserAPI`类中的`this.context`——一旦我们将它放在上下文中。

转至`index.js`并进行以下调整:

```
// index.js

const app = require('express')()
const { ApolloServer } = require('apollo-server-express')
const mongoose = require('mongoose')
// Import express-session
const session = require('express-session')

const typeDefs = require('./typeDefs')
const resolvers = require('./resolvers') 
const CurrencyAPI = require('./datasources/currencies')
const UserAPI = require('./datasources/user')
// import your session secret
const { mongoPassword, secret } = require('./config/keys') 

const server = new ApolloServer({ 
  typeDefs,
  resolvers,
  dataSources: () => ({
    currencyAPI: new CurrencyAPI(),
    userAPI: new UserAPI()
  }),
  // add req Object to context
  context: ({ req }) => ({ req })
})

// add express-session to middleware
app.use(session({
  secret,
  resave: false,
  saveUninitialized: false
}))

// add cors to middleware
server.applyMiddleware({ 
  app, 
  cors: {
      credentials: true,
      origin: 'http://localhost:3000'
  }
})

mongoose
.connect(`mongodb+srv://marlon:${mongoPassword}@cluster0-o028g.mongodb.net/forex?retryWrites=true&w=majority`, { useNewUrlParser: true })
.then(() => app.listen(4000, () => {
  console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`)
})).catch(err => console.log(err)) 
```

Enter fullscreen mode Exit fullscreen mode

导入`express-session`然后创建并导入您的会话密码。任何字符串都可以。然后将`request`对象添加到`context`中，并通过我们的`express-session`和`cors`中间件。

让我们将`login`添加到我们的 typeDefs 中。

```
// typeDefs.js

type Mutation {
  register(email: String!, password: String!, name: String!): Boolean!
  login(email: String!, password: String!): User
} 
```

Enter fullscreen mode Exit fullscreen mode

`login`解析器:

```
// resolvers.js 

Mutation: {
  register: async (_, { email, password, name }, { dataSources }) => {
    try {
      const newUser = await dataSources.userAPI.createNewUser({ email, password, name })
      return newUser
    } catch (error) { throw error }
  },
  login: async (_, { email, password }, { dataSources }) => {
    try {
      const user = await dataSources.userAPI.loginUser({ email, password })
      return user 
    } catch (error) { throw error }
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

转到`datasources/user.js`，向`UserAPI`类添加一个名为`loginUser`的方法。

```
// datasources/user.js

async loginUser({ email, password }) {
  try {
    if (!isEmail.validate(email)) { throw new UserInputError('Invalid Email') }
    const user = await User.findOne({ email }) 
    if(!user) { throw new UserInputError('Email or password is incorrect!') }
    const isEqual = await bcrypt.compare(password, user.password)
    if(!isEqual) { throw new UserInputError('Email or password is incorrect!') }
    this.context.req.session.userId = user.id 
    return user 
  } catch (error) { throw error }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用`isemail`验证邮件。接下来，我们将给定的密码与数据库中的散列密码进行比较。如果一切顺利，我们在`req.session`上放置一个`userId`属性。这是我们跟踪用户的方式。为了简单起见，我在这里返回整个用户对象。也就是说，在生产应用程序中，你永远不会想要返回用户密码。

前往 [GraphQL 游乐场](http://localhost:4000/graphql)并运行`login`突变。

[![login mutation](img/00469311647f7930393a6e9ff159bca2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uGm5dAfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/login.png%3Fraw%3Dtrue)

我们在前端跟踪用户的方法是使用一个`me`查询。这个`me`查询将告诉我们哪个用户正在试图执行一个动作，因此允许我们决定这个用户是否有执行所述动作的授权。

我们开始吧！

首先，将`me`查询添加到`typeDefs.js`中。

```
// typeDefs.js

type Query {
  currencyPairInfo(fc: String, tc: String): PairDisplay!
  monthlyTimeSeries(fc: String, tc: String): TimeSeries!
  me: User
} 
```

Enter fullscreen mode Exit fullscreen mode

将`me`查询添加到解析器`Query`对象。

```
// resolvers.js 

me: async (_, __, { dataSources }) => {
  try {
    const user = await dataSources.userAPI.getMe()
    return user
  } catch (error) { throw error }
}, 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要将`getMe`方法添加到数据源中。转到`datasources/user.js`并添加以下内容:

```
// datasources/user.js

  async getMe() {
    try {
      if(!this.context.req.session.userId) return null 
      const user = await User.findById(this.context.req.session.userId) 
      return user 
    } catch (error) { throw error }
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在回到 GraphQL 游乐场。点击操场右上角的设置档位图标，将`"request.credentials"`调整为:`"request.credentials":"include"`。登录，然后执行`me`查询，你应该得到登录的用户。

[![me query](img/635d51b7da3e393f6fcbb6f08dcb9a3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a5y-hOqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/me.png%3Fraw%3Dtrue)

现在用不同的用户登录，当您执行`me`查询时，它会提供新用户的信息。这是因为每个请求都有一个新的`context`被构建。因此，`req.session.userId`将永远属于发出请求的用户。

太棒了。这是创造`logout`突变的好时机。我们开始吧！前往`typeDefs.js`并添加`logout`突变。

```
// typeDefs.js

type Mutation {
  register(email: String!, password: String!, name: String!): Boolean!
  login(email: String!, password: String!): User!
  logout: Boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

将`logout`添加到`resolvers.js`中的`Mutation`对象中。

```
// resolvers.js

Mutation: {
  register: async (_, { email, password, name }, { dataSources }) => {
    try {
      const newUser = await dataSources.userAPI.createNewUser({ email, password, name })
      return newUser
    } catch (error) { throw error }
  },
  login: async (_, { email, password }, { dataSources }) => {
    try {
      const user = await dataSources.userAPI.loginUser({ email, password })
      return user 
    } catch (error) { throw error }
  },
  logout: async (_, __, { req }) => {
    try { req.session.destroy(() => false) } 
    catch (error) { throw error }
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户点击注销时，我们销毁会话并返回 false。当你执行`logout`突变时，你应该回到`null`。

看哪！用户可以注销！

因为这是一个货币兑换，如果我们允许用户兑换货币可能是最好的。🤔打开`typeDefs.js`并添加`openPosition`突变。

```
// typeDefs.js

type Mutation {
  register(email: String!, password: String!, name: String!): Boolean!
  login(email: String!, password: String!): User!
  logout: Boolean
  openPosition(pair: String!, lotSize: Int, openedAt: Float!, position: String!): PairUpdateResponse!
} 
```

Enter fullscreen mode Exit fullscreen mode

现在把`PairUpdateResponse`加到`typeDefs.js`上:

```
// typeDefs.js

type PairUpdateResponse {
  success: Boolean!
  message: String!
  pair: Pair!
} 
```

Enter fullscreen mode Exit fullscreen mode

当用户试图打开头寸(买入/卖出一个货币对)时，他们将得到一个成功响应(对/错)，一条描述所采取的操作的消息，以及关于货币对的信息。

将`openPosition`突变添加到`resolvers.js`中。

```
// resolvers.js

openPosition: async (_, { pair, lotSize, openedAt, position }, { dataSources }) => {
  try {
    const open = await dataSources.userAPI.newPosition({ 
      pair, 
      lotSize, 
      openedAt, 
      position 
    })
    return open 
  } catch (error) { throw error }
}, 
```

Enter fullscreen mode Exit fullscreen mode

需要一些论据。`pair`看起来类似于:“欧元/美元”。`lotSize`是你的仓位大小(你在仓位上投入了多少钱)。`openedAt`是你买入/卖出的价格。`position`将是“多头”或“空头”，取决于用户是想买多头(赌价格会上涨)还是卖空(赌价格会下跌)。

将`newPosition`方法添加到`datasources/user.js`，但是首先从`apollo-server-express`导入`AuthenticationError`和`ForbiddenError`。我们还需要导入我们的`Pair`模式。

```
// datasources/user.js

const { 
  UserInputError, 
  AuthenticationError, 
  ForbiddenError 
} = require('apollo-server-express')

const Pair = require('../models/Pair')

async newPosition({ pair, lotSize, openedAt, position }) {
  try {
    const user = await User.findById(this.context.req.session.userId)
    if(!user) throw new AuthenticationError('Invalid Crendentials!')
    if(user.bankroll < lotSize) throw new ForbiddenError(`Insufficient funds!`)

    const newPair = new Pair({
      pair,
      lotSize,
      openedAt,
      position,
      open: true,
      user: this.context.req.session.userId
    })
    const pairResult = await newPair.save()
    user.pairs.unshift(pairResult)
    user.bankroll -= lotSize
    await user.save()
    const message = `Congrats ${user.name}! You've opened a ${position} position on ${pair} at ${openedAt}!`
    const success = true
    return { success, message, pair: pairResult }
  } catch (error) { throw error }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们检查用户是否有足够的钱来完成交易。如果是这样，我们创建一个对，并将其添加到`pairs`数组中。我们从用户`bankroll`中减去位置大小，并以`PairUpdateResponse`的形式返回一个响应。

打开 GraphQL Playground，登录并运行`openPosition`突变。

[![openPosition](img/6d5d8170f1ad34aad3e705a59c5bc0a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CqyzRVrO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/openPosition.png%3Fraw%3Dtrue)

既然我们的用户可以打开一个位置，它可能是一个好主意，提供一种方法来关闭上述位置。我们给`typeDefs.js`加一个`closePosition`突变。

```
// typeDefs.js

type Mutation {
  register(email: String!, password: String!, name: String!): Boolean!
  login(email: String!, password: String!): User!
  logout: Boolean
  openPosition(pair: String!, lotSize: Int, openedAt: Float!, position: String!): PairUpdateResponse!
  closePosition(id: ID!, closedAt: Float!): PairUpdateResponse!
} 
```

Enter fullscreen mode Exit fullscreen mode

`closePosition`变异将对`id`和退出价格(`closedAt`)作为参数。然后它以`PairUpdateResponse`的形式返回一个响应。

让我们来处理分解器。

```
// resolvers.js

closePosition: async(_, { id, closedAt }, { dataSources }) => {
  try {
    const close = await dataSources.userAPI.exitPosition({ id, closedAt })
    return close 
  } catch (error) { throw error }
}, 
```

Enter fullscreen mode Exit fullscreen mode

回到`datasource/user.js`执行`exitPosition`方法。

```
// datasources/user.js

async exitPosition({ id, closedAt }) {
  try {
    const user = await User.findById(this.context.req.session.userId) 
    if(!user) throw new AuthenticationError('Invalid credentials!')

    const pair = await Pair.findById(id) 
    if(!pair) throw new AuthenticationError('Invalid credentials!')
    if(!pair.open) throw new ForbiddenError('Transaction already complete!')
    let pipDifFloat
    pair.position === 'long' 
      ? pipDifFloat = (closedAt - pair.openedAt).toFixed(4) 
      : pipDifFloat = (pair.openedAt - closedAt).toFixed(4)   
    pair.pipDif = pipDifFloat
    pair.closedAt = closedAt
    pair.profitLoss = pipDifFloat * pair.lotSize
    pair.open = false 
    const savedPair = await pair.save()

    user.bankroll += (pair.lotSize + savedPair.profitLoss) 
    await user.save() 

    const success = true 
    const message = `${ savedPair.profitLoss > 0 
      ? 'Congrats!' 
      : ''
      }  ${user.name} you've closed your ${savedPair.position} position on ${savedPair.pair} at ${closedAt}${ savedPair.profitLoss > 0 
      ? '! For a profit of '+Math.round(savedPair.profitLoss)+'!' 
      : '. For a loss of '+Math.round(savedPair.profitLoss)+'.'}`
    return { success, message, pair: savedPair }
  }
  catch (error) { throw error }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们找到我们的配对，我们就创建一个名为`pipDifFloat`的变量。如果`position`很长，我们从`closedAt`价格中减去`openedAt`价格。如果`position`是空头，我们从`openedAt`价格中减去`closedAt`价格。我们将结果存储在`pipDifFloat`中，然后将 pairs `pipDif`属性设置为`pipDifFloat`。

接下来，我们设置`closedAt`价格，并通过将`pipDifFloat`乘以`lotSize`来计算`profitLoss`。之后，我们将`open`属性设置为`false`并保存我们的配对。一旦我们保存了配对，我们就相应地调整用户`bankroll`。最后，我们返回`PairUpdateResponse`并告诉用户好/坏消息。

看看 GraphQL 操场:

[![closePosition](img/2f73f21264b802ac3ba21d81972acb78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Y6f_d_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/closePosition.png%3Fraw%3Dtrue)

我们取得了一些重大进展。让我们多做一些！

我们还有两个相关的查询，让我们一起来处理它们。在`typeDefs.js`内部，将`Query`类型调整为如下:

```
// typeDefs.js

type Query {
  currencyPairInfo(fc: String, tc: String): PairDisplay!
  monthlyTimeSeries(fc: String, tc: String): TimeSeries!
  me: User
  findPair(id: ID!): Pair!
  getPairs: [Pair!]
} 
```

Enter fullscreen mode Exit fullscreen mode

通过 id 获取一对的查询。检索所有用户对的另一个查询。让我们来解决这个问题。调整`Query`对象，使其类似于下面的代码:

```
// resolvers.js

Query: {
  currencyPairInfo: async (_, { fc, tc }, { dataSources }) => {
    try {
      const currencyPairs = await dataSources.currencyAPI.getCurrencyPair(fc, tc)
      return currencyPairs
    } catch (error) { throw err }
  },
  monthlyTimeSeries: async (_, { fc, tc }, { dataSources }) => {
    try {
      const timeSeries = await dataSources.currencyAPI.getMonthlyTimeSeries(fc, tc)
      return timeSeries
    } catch (error) { throw error }
  },
  me: async (_, __, { dataSources }) => {
    try {
      const user = await dataSources.userAPI.getMe()
      return user
    } catch (error) { throw error }
  },
  findPair: async (_, { id }, { dataSources }) => {
    try {
      const foundPair = await dataSources.userAPI.getPair({ id })
      return foundPair
    } catch (error) { throw error }
  },
  getPairs: async (_, __, { dataSources }) => {
    try {
      const foundPairs = await dataSources.userAPI.findPairs()
      return [...foundPairs]
    } catch (error) { throw error }
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

在`datasources/user.js`上定义`getPair`和`findPairs`。

```
// datasources/user.js

async getPair({ id }) {
  try {
    const pair = await Pair.findById(id)
    if(!pair || pair.user.toString() !== this.context.req.session.userId) { 
      throw new AuthenticationError('Invalid credentials!') 
    } 
    return pair
  } catch (error) { throw error }
}

async findPairs() {
  try {
    const pairs = await Pair
      .find({ user: this.context.req.session.userId })
      .sort({ updatedAt: -1 })
    if(!pairs.length) throw new UserInputError('Nothing to show!')
    return [...pairs] 
  } catch (error) { throw error }
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该在 GraphQL Playground 中看到类似的内容:

[![findPair](img/96198ce412661161b489f72f9521a1b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fkjgCuQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/findpair.png%3Fraw%3Dtrue)

[![getPairs](img/c4470c5de12a587ebd034a10bcbff884.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cRec1Nlx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/getpairs.png%3Fraw%3Dtrue)

最后一个突变，我们完成了后端！我们最后的标本——`addFunds`。用户会想把钱加到他们的账户上。我们决不会让他们无所事事。

我们将从`typeDefs.js`开始。创建`addFunds`突变，并定义其响应类型— `AddFundsResponse`。

```
// typeDefs.js

type Mutation {
  register(email: String!, password: String!, name: String!): Boolean!
  login(email: String!, password: String!): User!
  logout: Boolean
  openPosition(pair: String!, lotSize: Int, openedAt: Float!, position: String!): PairUpdateResponse!
  closePosition(id: ID!, closedAt: Float!): PairUpdateResponse!
  addFunds(amount: Int!): AddFundsResponse!
}

  type AddFundsResponse {
    success: Boolean!
    message: String!
    user: User!
  } 
```

Enter fullscreen mode Exit fullscreen mode

`addFunds`将`amount`作为单独的参数，因为我们已经通过`context`了解了用户。让我们来解决最后一个解析器。一旦我们实现了`addFunds`，我们的`Mutation`对象应该如下所示:

```
// resolvers.js

Mutation: {
  register: async (_, { email, password, name }, { dataSources }) => {
    try {
      const newUser = await dataSources.userAPI.createNewUser({ email, password, name })
      return newUser
    } catch (error) { throw error }
  },
  login: async (_, { email, password }, { dataSources }) => {
    try {
      const user = await dataSources.userAPI.loginUser({ email, password })
      return user 
    } catch (error) { throw error }
  },
  logout: async (_, __, { req }) => {
    try { req.session.destroy(() => false) }
    catch (error) { throw error }
  },
  openPosition: async (_, { pair, lotSize, openedAt, position }, { dataSources }) => {
    try {
      const open = await dataSources.userAPI.newPosition({ pair, lotSize, openedAt, position })
      return open 
    } catch (error) { throw error }
  },
  closePosition: async(_, { id, closedAt }, { dataSources }) => {
    try {
      const close = await dataSources.userAPI.exitPosition({ id, closedAt })
      return close 
    } catch (error) { throw error }
  },
  addFunds: async (_, { amount }, { dataSources }) => {
    try {
      const weeee = await dataSources.userAPI.additionalFunds({ amount })
      return weeee
    } catch (error) { throw error }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`datasources/user.js`上:

```
// datasources/user.js

async additionalFunds({ amount }) {
  try {
    const user = await User.findById(this.context.req.session.userId)
    if(!user) throw new AuthenticationError('Invalid credentials!')
    user.bankroll += amount 
    const savedUser = await user.save()
    const success = true
    const message = `Congrats ${user.name} you've added ${amount} to your bankroll!`
    return { bankroll: savedUser.bankroll, success, message } 
  } catch (error) { throw error }
} 
```

Enter fullscreen mode Exit fullscreen mode

您的 GraphQL 操场应该看起来像这样:

[![addFunds](img/9cb6789caa2a5f57b61fa714ed20fa02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DqL45hYn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/marlonanthony/currency_exchange/blob/mastimg/addfunds.png%3Fraw%3Dtrue)

看哪！我们完成了后端！[前端](https://dev.to/marlonanthony/let-s-build-a-currency-exchange-part-ii-eh0)等待！

这个项目的代码在我的 GitHub 上。

伸手:[Twitter](https://twitter.com/marlonanthony10)|[Medium](https://medium.com/@marlonanthony)|[GitHub](https://github.com/marlonanthony)