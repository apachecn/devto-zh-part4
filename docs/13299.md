# Express 中的中央错误处理

> 原文：<https://dev.to/nedsoft/central-error-handling-in-express-3aej>

> Express 是 Node.js - [docs](https://expressjs.com/) 的一个快速、非个性化、极简的 web 框架

无论是用 Express 还是其他语言构建应用程序，错误处理都是一个不可或缺的例程。

当使用 Express 或任何其他框架/库构建 API 端点时，验证检查对于每个用例总是必要的，并且总是需要向用户返回错误响应。处理这些错误并为每个验证返回一个响应变得非常乏味，并使代码库变得混乱。让我们考虑下面的一个例子:

```
const validateUser = async (req, res, next) => {
  try {
    const { email, password } = req.body
    if (!email || !password) {
      return res.status(400).json({
        status: 'error',
        message: 'Missing required email and password fields',
      })
    }
    const user = await db.User.findOne({ where: { email }});
    if (!user) {
      return res.status(404).json({
        status: 'error',
        message: 'User with the specified email does not exists',
      })
    }
    next()
  } catch (error) {
    return res.status(500).json({
      status: 'error',
      message: 'An error occurred trying to process your request',
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看了上面的片段，你会同意我的观点，在每个检查点都返回错误响应，看起来已经很混乱了。如果你的代码库中只有这些代码，那就没关系，当你必须在你的代码库中的几个方法或函数中重复同样的方法时，问题就出现了。

在我们深入寻找使上面的片段更好的解决方案之前，让我们看看本文需要什么:

## 要求

*   已安装的节点
*   安装了 npm 或纱线
*   Nodejs/Express 知识

> 注意:本文假设读者已经对 NodeJs/Express 有了基本的了解。因此，一些细节可能会被跳过。

为了跟进，在这里克隆用于本文的库[。](https://github.com/oriechinedu/express-error-handling.git)

**第一步**。*创建自定义* *错误* *构造函数*
我们需要创建一个自定义错误构造函数，它扩展了 [JavaScript 错误构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)。

在您之前克隆的项目中，创建一个名为 **helpers** 的目录。在**助手**目录下，创建一个名为 **error.js** 的文件

将下面的片段添加到 **error.js**

```
class ErrorHandler extends Error {
  constructor(statusCode, message) {
    super();
    this.statusCode = statusCode;
    this.message = message;
  }
}
module.exports = {
  ErrorHandler
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们导出了 ErrorHandler，这样我们就可以从 **index.js** 文件中导入它。

接下来，我们需要创建一个向用户返回格式化错误响应的函数。

将下面的代码片段添加到`error.js`文件中。

```
const handleError = (err, res) => {
  const { statusCode, message } = err;
  res.status(statusCode).json({
    status: "error",
    statusCode,
    message
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

更新`module.exports`块以包含`handleError`函数，如下所示:

```
module.exports = {
  ErrorHandler,
  handleError
} 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**。*创建错误处理中间件*

> 中间件功能是可以访问请求对象(req)、响应对象(res)以及应用程序的请求-响应周期中的下一个中间件功能的功能。下一个中间件功能通常由名为 next 的变量表示。[快递单据](https://expressjs.com/en/guide/using-middleware.html)

错误处理中间件是一种特殊类型的中间件，与常规中间件相反，它接受四个参数。第一个参数是**错误**对象。
下面的代码片段展示了一个错误处理中间件的例子:

```
function(err, req, res, next) {
  //code goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

在`index.js`中，让我们添加一个错误处理中间件，在此之前，让我们在`index.js`中导入`handleError`函数；

`index.js`文件应该如下所示:

```
const express = require('express')
const { handleError } = require('./helpers/error')
const app = express()

app.use(express.json())
const PORT = process.env.PORT || 3000

app.get('/', (req, res) => {
  return res.status(200).json('Hello world'); 
})

app.use((err, req, res, next) => {
  handleError(err, res);
});
app.listen(PORT, () => console.log(`server listening at port ${PORT}`)) 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们是如何调用`handleError`函数并将错误对象和响应对象传递给它的。

> 注意:错误处理中间件必须是其他中间件和路由中的最后一个，它才能正常工作。

现在，在应用程序中任何你想检查错误的地方，你需要做的就是抛出`ErrorHandler`构造函数。
我们现在可以应用错误处理机制来重构我们之前的混乱代码。它应该看起来如下所示:

```
const validateUser = async (req, res, next) => {
  try {
    const { email, password } = req.body
    if (!email || !password) {
      throw new ErrorHandler(404, 'Missing required email and password fields')
    }
    const user = await  db.User.findOne({ where: { email }});
    if (!user) {
      throw new ErrorHandler(404, 'User with the specified email does not exists')
    }
    next()
  } catch (error) {
    next(error)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何将错误传递给上面的`next`函数的。它只是将错误传递给我们在`index.js`中定义的错误处理中间件。

让我们添加一个路由来测试我们刚刚创建的错误处理机制。在`index.js`中添加下面的代码片段:

```
app.get('/error', (req, res) => {
  throw new ErrorHandler(500, 'Internal server error');
}) 
```

Enter fullscreen mode Exit fullscreen mode

记得在`index.js`中导入 ErrorHandler。它应该如下所示:

```
const { handleError, ErrorHandler } = require('./helpers/error') 
```

Enter fullscreen mode Exit fullscreen mode

通过运行 npm start 启动服务器，然后访问路由`/error`。您将得到类似如下所示的响应:

```
{
    "status": "error",
    "statusCode": 500,
    "message": "Internal server error"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

在本文中，我们已经确定了在 Express 应用程序中需要一个中央错误处理程序。我们还演示了实现中央错误处理程序所需的步骤。

如果你对这篇文章有任何问题或贡献，请通过 Twitter 联系我。
感谢您通读。✌️