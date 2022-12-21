# “快速中间件”只是一种将一系列功能链接起来的奇特方式。在 3 分钟内解释😎

> 原文：<https://dev.to/getd/express-middleware-is-just-a-fancy-way-of-chaining-a-bunch-of-functions-explained-in-3-mins-43jf>

让我们从创建世界上最简单的 Express 服务器开始:

```
import express from "express";

const app = express();

app.get('/hello', (req, res) => {
  res.send('world'));
} 
```

Enter fullscreen mode Exit fullscreen mode

它创建一个路由`/hello`来响应主体`world`。

现在让我们稍微修改一下最后一行，添加第三个参数`next` :

```
app.get('/hello', (req, res, next) => {
  res.send('world'));
  next();
} 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。🎉🎉🎉您刚刚创建了一个快速中间件。就这么简单！

# 中间件==功能同`next()`

中间件就是一个有三个参数`(req, res, next)`的函数，其中`next`是一个允许你**链接**多个函数的函数。让我们看另一个例子:

```
 // returns true if server time is in AM
function isMorning() {...}

app.get("/hello",
  // middleware #1
  (req, res, next) => {
    if (isMorning()) {
      res.send("morning");
    } else {
      next();
    }
  },
  // middleware #2: called when isMorning() === false
  (req, res, next) => {
    res.send("afternoon");
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们链接了两个中间件函数来处理`/hello`路由。我们使用`next()`将控制权从第一个中间件传递给第二个中间件。

在现实场景中，中间件对于在路由之间共享公共代码非常有用。

# 例 1:某些路由需要用户认证。

这里我们创建了一个中间件，它只在用户被认证时调用`next()`。中间件由两个路由共享。注意，当用户没有被认证时，我们不调用`next()`，这将**停止链**。

```
function RequireUserAuthentication(req, res, next) {
  if (req.user == null) {
    res.status("401").send("User is unauthenticated.");
  } else {
    next();
  }
}

app.get("/me/name", RequireUserAuthentication, (req, res, next) => {
  res.send(req.user.name);
});

app.get("/me/avatar", RequireUserAuthentication, (req, res, next) => {
  res.send(req.user.avatar);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在所有路由上使用 RequireUserAuthentication，而不是特定路由，该怎么办？

# 例 2:所有路由都需要用户认证。

我们可以使用`app.use(RequireUserAuthentication)`，因此`RequireUserAuthentication`中间件被‘注入’到所有路由中。

需要注意的一点是**中间件是**订购的。在下面的代码中，任何在`app.use(RequireUserAuthentication)`之前定义的路线都不会受到影响。

```
// This route is not affected by RequireUserAuthentication
app.get("/home", (req, res, next) => res.send(...));

// Require user auth for all routes defined after this line.
app.use(RequireUserAuthentication);

// Routes below are user sign-in required
app.get("/me/name", (req, res, next) => {
  res.send(req.user.name);
});

app.get("/me/avatar", (req, res, next) => {
  res.send(req.user.avatar);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 例 3:将 JSON 请求体解析成对象。

有时将请求体自动转换成 JSON 对象是很有用的，因此我们不必为每一条路由都编写相同的逻辑:

```
// Converts request body into req.body as a javascript object
function JSONParser(req, res, next) {
  if (req.headers['content-type'].startsWith('application/json')) {
    const rawBody = readStreamIntoString(req);
    req.body = JSON.parse(rawBody);
  }
  next();
}

// Apply JSONParser middleware to all routes defined after this line
app.use(JSONParser);

// Reads post name and content from req.body
app.get("/new/post", (req, res, next) => {
  const postTitle = req.body.title;
  const postContent = req.body.content;
  ...
});

// Reads username from req.body
app.get("/new/user", (req, res, next) => {
  const userName = req.body.username;
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个`JSONParser`中间件，它将 JSON 请求体解析成一个对象，并将该对象设置为`req.body`。后来，该对象被从路线`/new/post`和任何其他路线之后定义的路线中读取。

# 示例 4:可配置中间件

让我们通过创建一个“工厂”函数来返回中间件函数。函数的函数🤖

```
function BodyParser(options) {
  if (options.type === "JSON") {
    return (req, res, next) => {
      if (req.headers["content-type"].startsWith("application/json")) {
        const rawBody = readStreamIntoString(req);
        req.body = JSON.parse(rawBody);
      }
      next();
    };
  } else if (options.type === "URL_ENCODED") {
    return (req, res, next) => {
      if (
        req.headers["content-type"].startsWith(
          "application/x-www-form-urlencoded"
        )
      ) {
        const rawBody = readStreamIntoString(req);
        req.body = new URLSearchParams(rawBody);
      }
      next();
    };
  }
}

app.use(BodyParser({ type: "JSON" }));
app.use(BodyParser({ type: "URL_ENCODED" })); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们允许传入一个`options`参数来控制返回哪个中间件函数。

事实上，这正是 [bodyParser](https://github.com/expressjs/body-parser) 解析请求体所做的事情(当然是用更复杂的代码)。

**您在生产中使用了哪些中间件功能？请在下面留言，分享你最喜欢的❤️❤️❤️！**

查看 [getd.io](https://getd.io) 并留下一些反馈，告诉我们你希望在下一个❤️❤️❤️中看到哪些功能！