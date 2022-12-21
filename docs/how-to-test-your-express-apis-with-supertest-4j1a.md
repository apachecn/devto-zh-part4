# 如何用 Supertest 测试您的 Express APIs

> 原文：<https://dev.to/rahmanfadhil/how-to-test-your-express-apis-with-supertest-4j1a>

*阅读原文[此处](https://rahmanfadhil.com/test-express-with-supertest)T3】*

## 简介

Express 是构建 web 应用程序，尤其是 REST APIs 最流行的 Node.js 框架。在本文中，我将向您展示如何使用一个名为 SuperTest 的酷库来测试您的 API 端点。

SuperTest 是一个 HTTP 断言库，允许您测试 Node.js HTTP 服务器。它构建在 [SuperAgent](https://github.com/visionmedia/superagent) 库之上，后者是 Node.js 的 HTTP 客户端

## 入门

让我们首先创建一个简单的 Express 服务器，它有一个返回简单 json 的端点。

```
// index.js

const express = require("express")
const app = express()

app.get("/", (req, res) => {
  res.send({ name: "John Doe" })
})

app.listen(8000, () => {
  console.log("Server has started!")
}) 
```

要手动测试，只需运行`node index.js`并用 Postman 或 cURL 向`http://localhost:8000`发送请求。

```
$  curl http://localhost:8000
{"name":"John Doe"} 
```

为了使我们的代码可测试，我们需要将我们的`listen`方法分离到另一个文件中，这样我们就可以在不启动服务器的情况下需要我们的 express 实例。

```
// index.js

const server = require("./server.js")

server.listen(8000, () => {
  console.log("Server has started!")
}) 
```

```
// server.js

const express = require("express")

const app = express()

app.get("/", (req, res) => {
  res.send({ name: "John Doe" })
})

module.exports = app 
```

我们已经启动并运行了我们的服务器，现在是时候写一些测试了。

## SuperTest 基本用法

在开始使用 SuperTest 之前，我们需要安装一个测试框架。这是一个编写自动化测试的便利工具，因此您将知道您的应用程序的哪个部分出错了。

在本教程中，我们将使用 [Jest](https://jestjs.io) 。这是迄今为止我用过的最简单的 JavaScript 测试框架。

```
$  npm install --save-dev jest 
```

然后，我们需要通过在`package.json`中添加一个测试脚本来设置我们的测试命令。

```
{  //  ...  "scripts":  {  "test":  "jest"  }  //  ...  } 
```

Jest 安装和配置之后，我们现在可以通过创建新的测试文件来编写我们的第一个测试。

```
// server.test.js

const app = require("./server")

test("GET /", done => {
  supertest(app)
    .get("/")
    .expect(200, JSON.stringify({ name: "John Doe" }))
    .end(done)
}) 
```

这里我们需要我们的`server.js`文件来获取我们的 Express.js 服务器实例。然后，创建一个名为“GET /”的新测试，向`/`端点运行一个`GET`请求，并期望结果是定义的对象。我们还想确保响应具有`200` HTTP 状态，这意味着我们的请求是 OK 的。

如果你注意到，当我们断言我们的响应时，我们正在字符串化我们的 JSON 对象。这是因为默认情况下，supertest 会将我们的响应作为字符串进行比较。

我们现在可以通过运行`npm test`或`npm run test`来运行我们的测试。

## 使用回调

还有其他方法来断言您服务器响应。我们可以传递一个回调来获取并断言我们的响应，而不是将预期的结果作为参数传递。

```
supertest(app)
  .get("/")
  .expect(response => {
    expect(response.status).toBe(200)
    expect(response.body).toEqual({ name: "John Doe" })
    done()
  }) 
```

通过使用回调方法，我们直接在响应回调中断言您的响应体和状态代码。当断言完成时，我们还需要运行 Jest `done`函数来完成我们的测试。

我们还以 JavaScript `Object`的形式获得我们的响应体，因此我们可以直接将其与 Jest matchers 提供的`toEqual`方法进行比较。

## 发送表单数据

表单输入是当今动态 web 应用程序最基本的特征。用表单数据测试端点在 SuperTest 中是小菜一碟。

为了处理表单数据，我们需要安装另一个名为 [Body Parser](https://github.com/expressjs/body-parser) 的第三方库。Body Parser 是一个 Express 中间件，我们可以用它来处理 Express 应用程序中的表单数据。

如果你不知道 middlware 是什么，本质上，它只是一个可以拦截用户请求的函数。在这种情况下，我们可以用它来获取表单数据。

我们可以通过运行下面的命令来安装 Body Parser。

```
$  npm install body-parser 
```

然后，我们可以在我们的`server.js`文件中使用主体解析器中间件。

```
// server.js

const express = require("express")
const bodyParser = require("body-parser")

const app = express()
app.use(bodyParser.urlencoded({ extended: false }))

// ... 
```

我们现在可以通过访问`req.body`变量来访问 route 处理程序中的用户表单数据。

```
// server.js

// ...

app.post("/form-data", (req, res) => {
  const formData = {
    name: req.body.name,
    age: req.body.age,
  }

  res.send(formData)
})

// ... 
```

为了测试它，我们可以通过调用 supertest 请求中表单的每个字段的`field`方法来发送表单数据。

```
supertest(app)
  .get("/form-data")
  .field("name", "John Doe")
  .field("age", "25")
  .expect(response => {
    expect(response.status).toBe(200)
    expect(response.body).toEqual({ name: "John Doe", age: "24" })
    done()
  }) 
```

## JSON 请求体

默认情况下，supertest 将发送带有`application/x-www-form-urlencoded`的`Content-Type`的表单数据。如果客户端想发送更复杂的数据类型，你可以使用 JSON 格式，这就是`application/json`。为此，我们可以使用 supertest 中的另一个方法`send`。

`send`方法让你发送一个请求，请求体的`Content-Type`为`application/json`。这样你就可以发送更复杂的数据，比如数字、数组、嵌套对象等等。

```
const formData = {
  name: "John Doe",
  age: 25,
  fruits: ["Apple", "Orange"],
}

supertest(app)
  .get("/form-data")
  .send(formData)
  .expect(response => {
    expect(response.status).toBe(200)
    expect(response.body).toEqual(formData)
    done()
  }) 
```

然后，您需要从`body-parser`包中添加另一个 express 中间件，它允许您用`application/json`的`Content-Type`解码请求体。

```
// server.js

const express = require("express")
const bodyParser = require("body-parser")

const app = express()
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

// ... 
```

## 文件上传

我们也可以用 SuperTest 上传文件到我们的服务器！

为此，我们需要首先设置我们的服务器，这样我们就可以处理从客户端上传的文件。在 Express 中处理文件上传最简单的方法是使用另一个名为 [Multer](https://github.com/expressjs/multer) 的第三方库。所以，让我们先安装它！

```
$  npm install multer 
```

然后，我们可以初始化一个新的 Multer 对象，并指定我们的上传目录。

```
// server.js

const express = require("express")
const multer = require("multer")

const app = express()
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

const upload = multer({ dest: "uploads/" })

// ... 
```

最后，我们可以通过从 Multer 实例添加 Multer 中间件来处理文件上传。这让我们将文件上传到客户端表单的`avatar`字段中。

```
// server.js

// ...

app.post("/upload", upload.single("avatar"), (req, res) => {
  // req.file is the `avatar` file
  // req.body will hold the text fields, if there were any
})

// ... 
```

我们可以通过使用`attach`方法来测试它，在这里我们可以指定想要上传的字段名和文件路径。

```
supertest(app)
  .get("/")
  .field("name", "John Doe")
  .attach("avatar", "/path/to/file.png")
  .expect(response => {
    expect(response.status).toBe(200)
    done()
  }) 
```

默认情况下，我们的表单数据将以`multipart/form-data`的`Content-Type`发送。