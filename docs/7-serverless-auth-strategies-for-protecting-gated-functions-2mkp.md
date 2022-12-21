# 保护门控功能的 7 种无服务器授权策略

> 原文：<https://dev.to/davidwells/7-serverless-auth-strategies-for-protecting-gated-functions-2mkp>

> 最初发布于 [davidwells.io](https://davidwells.io/blog/serverless-function-auth-strategies)

我们如何保护某些功能，并将其限制在特定用户(即管理员/付费用户)的范围内？

在本帖中，我们将介绍授权访问无服务器功能的不同策略。

本报告中的[代码主要涵盖 AWS lambda 功能，但一般策略可以应用于作为服务提供商的任何功能。](https://github.com/DavidWells/serverless-auth-strategies/)

## 选择您的授权提供商

有很多服务可以为你的应用提供开箱即用的认证。推荐使用其中的一个，主要是因为它很容易搞乱安全链中的某个环节。

一些选项包括:

*   [Auth0](https://github.com/DavidWells/serverless-auth-strategies/tree/master/providers/auth0)
*   [Netlify](https://github.com/DavidWells/serverless-auth-strategies/tree/master/providers/netlify-identity)
*   [AWS 认知到](https://docs.amazonaws.cn/en_us/cognito/latest/developerguide/what-is-amazon-cognito.html)
*   [秋田](https://www.okta.com)
*   [燃烧基地](https://firebase.google.com/docs/auth/)
*   [...添加更多](https://github.com/DavidWells/serverless-auth-strategies/issues)

滚动你自己的授权是不明智的，也违背了无服务器的理念:“关注对客户的价值，而不是管道”

选择提供商并继续！

## 选择一个策略

有许多方法可以保护您的功能。

1.  [内嵌授权检查](#1-inline-auth-checking)
2.  [中间件](#2-middleware)
3.  [“遗留”中间件](#3-legacy-middleware)
4.  [授权装饰者](#4-auth-decorators)
5.  [自定义授权人](#5-custom-authorizers)
6.  [代理级别](#6-proxy-level)
7.  [一次性访问令牌](#7-single-use-access-token)
8.  如果你知道更多，请在帖子上留下评论！

下面的列表将详细介绍它们以及各自的优缺点。

### 1。内嵌验证检查

内联函数认证发生在函数代码内部

我们对请求的 auth 头或主体进行检查，以验证用户是否可以访问该函数。

```
const checkAuth = require('./utils/auth')

exports.handler = (event, context, callback) => {
  // Use the event data auth header to verify
  checkAuth(event).then((user) => {
    console.log('user', user)
    // Do stuff
    return callback(null, {
      statusCode: 200,
      body: JSON.stringify({
        data: true
      })
    })
  }).catch((error) => {
    console.log('error', error)
    // return error back to app
    return callback(null, {
      statusCode: 401,
      body: JSON.stringify({
        error: error.message,
      })
    })
  })
} 
```

**这种方法的好处:**

*   对于单个函数来说很容易做到。装运它！

**这种方法的缺点:**

*   随着 API 的增长，这种身份验证方法很难在多个函数之间共享，并且可能导致非干代码
*   缓存可能是一个挑战，如果您的身份验证是一个昂贵的操作或需要一段时间，这可能会导致较慢的 UX，并花费更多的计算时间。

### 2。中间件

接下来，我们使用中间件方法进行身份验证。这仍然发生在代码级别，但是现在验证用户是否被允许访问该功能的逻辑被抽象到一个可重用的中间件级别。

MiddyJs 在 lambda 函数中启用一种合理的中间件方法方面做得很好

```
const middy = require('middy')
const authMiddleware = require('./utils/middleware')

const protectedFunction = (event, context, callback) => {
  // Do my custom stuff
  console.log('⊂◉‿◉つ This is a protected function')

  return callback(null, {
    statusCode: 200,
    body: JSON.stringify({
      data: 'auth true'
    })
  })
}

exports.handler = middy(protectedFunction).use(authMiddleware()) 
```

我们的正午中间件看起来是这样的:

```
const checkAuth = require('./auth')

module.exports = function authMiddleware(config) {
  return ({
    before: (handler, next) => {
      checkAuth(handler.event).then((user) => {
        console.log('user', user)
        // set user data on event
        handler.event.user = user
        // We have the user, trigger next middleware
        return next()
      }).catch((error) => {
        console.log('error', error)
        return handler.callback(null, {
          statusCode: 401,
          body: JSON.stringify({
            error: error.message
          })
        })
      })
    }
  })
} 
```

你也可以像在电影演示中看到的那样自己操纵它

### 3。“遗留”中间件

这种中间件方法是使用一个熟悉的带有 express PR flask 的 web 框架，并使用他们生态系统中的一个 auth 模块。

在 express 的情况下，您可以在 lambda 函数中使用 passport 策略

```
const express = require('express')
const cors = require('cors')
const bodyParser = require('body-parser')
const compression = require('compression')
const morgan = require('morgan')
const serverless = require('serverless-http')
const customLogger = require('./utils/logger')
const auth0CheckAuth = require('./utils/auth0')

/* initialize express */
const app = express()
const router = express.Router()

/*  gzip responses */
router.use(compression())

/* Setup protected routes */
router.get('/', auth0CheckAuth, (req, res) => {
  res.json({
    super: 'Secret stuff here'
  })
})

/* Attach request logger for AWS */
app.use(morgan(customLogger))

/* Attach routes to express instance */
const functionName = 'express'
const routerBasePath = (process.env.NODE_ENV === 'dev') ? `/${functionName}` : `/.netlify/functions/${functionName}/`
app.use(routerBasePath, router)

/* Apply express middlewares */
router.use(cors())
router.use(bodyParser.json())
router.use(bodyParser.urlencoded({ extended: true }))

/* Export lambda ready express app */
exports.handler = serverless(app) 
```

**这种方法的好处:**

*   利用现有代码进行 GSD

**这种方法的缺点:**

*   这在“无服务器”的做事方法上后退了一步，因为您有一个完整的 express 应用程序来引导每个传入的请求
*   由于快速开销，随着时间的推移，这将花费更多的 ms 运行时间
*   这引入了单片可以在 lambda 函数中工作的思想，这被认为是一种反模式

### 4。授权装饰者

类似于 auth 中间件，decorators 包装函数代码并返回另一个函数

与中间件相比，一些开发人员更喜欢这种更明确的方法

```
@AuthDecorator // <-- ref to auth wrapper function
function protectedFunction(event, context, callback) {
  // protected logic
} 
```

### 5。自定义授权人

[自定义授权者](https://www.alexdebrie.com/posts/lambda-custom-authorizers/)是 AWS API gateway 的一个特性。

它们本质上是另一个功能，检查用户是否被授权访问下一个功能。如果 auth 检查通过，那么 request 调用下一个 lambda 函数。

**该方法的优势:**

*   授权可以用 TTL(生存时间)缓存。这可以节省后续请求，因为缓存的身份验证不需要每次都进行可能很慢的身份验证检查。这节省了计算时间，因此节省了$美元

**这种方法的缺点:**

*   您需要使用 AWS API gateway 来使用自定义授权器

### 6。代理级别

与自定义授权者类似，您可以在代理级别验证请求。

这在 [Netlify](https://www.netlify.com/docs/redirects/#role-based-redirect-rules) 中通过检查仅 http 的安全 cookie 来实现。

如果请求头中存在`nf_jwt` cookie，Netlify 将对其进行反序列化，并将其传递给 lambda 函数的上下文对象

如果 cookie 无效，可以将请求发送到一个未授权的端点(http code X)

```
# If visitor has 'nf_jwt' with role set, let them see site.
/.netlify/functions/protected-function /.netlify/functions/protected-function 200! Role=*

# Else, redirect them to login portal site.
/.netlify/functions/protected-function /not-allowed 401! 
```

### 7。一次性访问令牌

一些第三方服务，如 AWS 和 faunaDB，使得在客户端使用一次性令牌直接调用它们的 API 成为可能。

这意味着没有函数中间人来对其他服务进行 API 调用。

**该方法的优势:**

*   更便宜(不需要为函数运行时付费)
*   更快(中间没有函数延迟)

**这种方法的缺点:**

*   设置起来更复杂
*   提供商必须支持安全的一次性访问令牌

有关此方法的更多信息，请参见 [AWS Cognito](https://docs.amazonaws.cn/en_us/cognito/latest/developerguide/authentication-flow.html) 文档。