# 我在 node 中的 jsend 实现

> 原文：<https://dev.to/rkristelijn/my-jsend-implementation-in-node-28b2>

# JSend

这是我对 jsend 的实现。你可以叉/克隆回购，给我你的反馈。我试着坚持:

1.  所有响应都返回 json(包括错误)
2.  将 HTTP.status 限制为
    *   200 好吧
    *   400 错误请求(客户端故障)
    *   500 内部服务器错误(服务器故障)
3.  在 1 和 2 之上，使用 [jsend](https://github.com/omniti-labs/jsend) 标准，但是所有 4 个字段总是被返回:
    *   状态-[正常、失败、错误]，
    *   数据-数据或堆栈
    *   消息-尽可能短的消息
    *   代码- [200，400，500]

这 3 点的驱动因素是，这个 REST API 应该服务于一个前端，在有限的特殊情况下，服务器突然用 HTML 或不同的结构进行响应。

代码可以在这里找到:[https://github.com/rkristelijn/jsend](https://github.com/rkristelijn/jsend)。

我还没有实现 POST、PUT 和 DELETE，所以这不是一个完整的 REST 服务。

# 安装

带 SSH:

```
git clone git@github.com:rkristelijn/jsend.git
cd jsend && npm i 
```

带 HTTP:

```
git clone https://github.com/rkristelijn/jsend.git
cd jsend && npm i 
```

# 用法

| 目的 | 命令 |
| --- | --- |
| 开始 | `npm start -s` |
| 试验 | `npm test -s`(服务器运行后) |

# 演示

[![](img/bb586b0aebb9cb598787cae4af4aad74.png)](//github.com/rkristelijn/jsend/blob/master/doc/demo.gif?raw=true)

# 回应

## `GET /`

```
{  "status":  "SUCCESS",  "data":  {  "hello":  "world",  "api":  "http://localhost:3000/api"  },  "message":  "200: OK",  "code":  200  } 
```

## `GET /api`

```
{  "status":  "FAIL",  "message":  "Route '/api' not found",  "data":  [  "400: BAD REQUEST",  "Route '/api' not found",  {  "available":  [  ["http://localhost:3000/api/users",  "http://localhost:3000/api/fail"]  ]  }  ],  "code":  400  } 
```

## `GET /api/users`

```
{  "status":  "SUCCESS",  "data":  {  "users":  [  {  "name":  "admin",  "pass":  "admin"  },  {  "name":  "system",  "pass":  "system"  },  {  "name":  "henk",  "pass":  "annie"  }  ]  },  "message":  "200: OK",  "code":  200  } 
```

## `GET /api/users/henk`

```
{  "status":  "SUCCESS",  "data":  {  "user":  {  "name":  "henk",  "pass":  "annie"  }  },  "message":  "200: OK",  "code":  200  } 
```

## `GET /api/fail`

我故意创建了一个错误，调用不存在的`i_will_crash_on_purpose()`来查看是否返回了正确的 JSON。

```
{  "status":  "ERROR",  "message":  "i_will_crash_on_purpose is not defined",  "data":  [  "500: INTERNAL SERVER ERROR",  "ReferenceError: i_will_crash_on_purpose is not defined",  "at /home/gius/jsend/api/fail/fail-router.js:10:5"  ],  "code":  500  } 
```

# 关键外卖

## npm 警告

在非 mac 系统上，忽略 npm [中 chokidar(通过 nodemon)的 WARN per 大量讨论的 fsevent 可选依赖项。似乎没有办法绕过它，就像我对 jQuery 依赖项](https://github.com/yarnpkg/yarn/issues/3738)[所做的那样](https://github.com/rkristelijn/login)

## console.log() vs debug()

Debug 是 express 的一个依赖项。调试可以在每个文件开始时定制，并在启动应用程序时过滤。

您可以使用一个. env 文件和 [dotenv](https://www.npmjs.com/package/dotenv) 或者简单地将以下内容添加到 package.json 中的启动脚本:

```
"scripts":  {  "start":  "PORT=3000 DEBUG=jsend* nodemon ./bin/www" 
```

## 测试中的自定义验证器

为了保持测试的简洁，我为 jsend 编写了 3 个简单的验证器，这些验证器可以重用，并保持测试简短、可管理和可重用。

## e2e 测试无需自举 app

测试还不如跳过之前在()中设置 app。这将删除多余的代码，使测试更简单，并测试实际的应用程序。

## 箭头-功能/测试中的λs

这不是最佳做法，因为此作用域已不存在。摩卡强烈建议在测试中避免使用λ:

不鼓励将箭头函数(又名“lambdas”)传递给 Mocha。Lambdas 在词汇上绑定了这个，不能访问 Mocha 上下文。

# 来源

*   [关于 JSON 标准的问题](https://stackoverflow.com/questions/12806386/standard-json-api-response-format)
*   [已经存在的 jsend 实现](https://jsonapi.org/implementations/#server-libraries-node-js)
*   [节点](https://expressjs.com/en/guide/error-handling.html)中的错误处理