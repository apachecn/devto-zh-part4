# 带 React、Express 和 Docker 的 Cookies

> 原文：<https://dev.to/macru/cookies-with-react-express-and-docker-4h97>

最近，我不得不将 JWT 移到 cookies 中，以便在 Docker 微服务之间无缝地使用它。我们用 was tricker 在单独的 url 前缀上运行每个容器，以确定当 url 改变时 cookie 必须在那里。我找不到任何直截了当的解决方案，所以我决定写下它，因为它可能对某人甚至对未来的我有用。

我只是假设你有工作的前端和后端容器，一切运行良好。我不会解释什么是 cookies，因为有很多关于这个主题的更好的文章。

### 基本设置

让我们以简单快捷服务器为例来说明如何发送 cookies。

```
// index.js

const express = require("express")
const session = require("express-session");

const app = express()

app.use(
  session({
    secret: process.env.LOGIN_SERVER_SECRET,
    saveUninitialized: true,
    resave: true,
    cookie: {
      httpOnly: false,
      secure: false,
    },
  }),
);

app.get("/cookie", (req, res) => {
  const options = {
    secure: false,
    httpOnly: false,
    domain: ".your.domain.com"
  }

  return res
    .cookie("cookieName", "cookieValue", options)
    .status(200)
    .send("cookie sent")
})

app.listen(8080) 
```

在这种情况下，每当我们向 *localhost:8080/cookie* 发送请求时，服务器都会用 Set-Cookie 头进行响应。当你直接在浏览器或类似 Postman 的应用程序中输入时，效果很好。当你在 client.your.domain.com 的 T2 运行客户端，在 server.your.domain.com 的 T4 运行服务器时，问题就出现了。我们开始遇到 CORS 问题。

让我们看看我们的客户端应用程序的基本设置。我使用了 create-react-app，并通过添加 [superagent](https://www.npmjs.com/package/superagent) (伟大的请求库)和每当我点击链接时发送请求来修改它。

```
// App.js

import React from "react";
import superagent from "superagent";
import logo from "./logo.svg";
import "./App.css";

function App() {

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          onClick={() =>
            superagent
              .get("http://localhost:8080/cookie")
              .then(response => {
                console.log(response);
              })
          }
        >
          Get Cookie
        </a>
      </header>
    </div>
  );
}

export default App; 
```

### CORS

因为我们从不同的发起者发送请求，我们得到 CORS 问题。简单的解决方法是安装 [cors](https://www.npmjs.com/package/cors) 包，并通过简单的例子将其添加到文档中。

同样，带有通配符`(*/*)`的简单 cors 不会起作用。我们必须为 cors 设置一些自定义配置，并进行飞行前选项检查。

```
// index.js

const express = require("express")
const session = require("express-session");
const cors = require("cors")
const app = express()

app.use(
  session({
    secret: process.env.LOGIN_SERVER_SECRET,
    saveUninitialized: true,
    resave: true,
    cookie: {
      httpOnly: false,
      secure: false,
    },
  }),
);

const corsOptions = {
  origin: /\.your.domain\.com$/,    // reqexp will match all prefixes
  methods: "GET,HEAD,POST,PATCH,DELETE,OPTIONS",
  credentials: true,                // required to pass
  allowedHeaders: "Content-Type, Authorization, X-Requested-With",
}

// intercept pre-flight check for all routes
app.options('*', cors(corsOptions))

// add cors middleware to route 
app.get("/cookie", cors(corsOptions), (req, res) => {
  const options = {
    secure: false,
    httpOnly: false,
    domain: ".your.domain.com"
  }

return res
    .cookie("cookieName", "cookieValue", options)
    .status(200)
    .send("cookie sent")
})

app.listen(8080) 
```

前端还有一个变化。因为我们的服务器现在接受带有凭证的请求，所以我们必须发送一个凭证来传递 cookie。这实际上是多了一行

```
// App.js

import React from "react";
import superagent from "superagent";
import logo from "./logo.svg";
import "./App.css";

function App() {

return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          onClick={() =>
            superagent
              .get("http://localhost:8080/cookie")
              .withCredentials()           // it's simple as that
              .then(response => {
                console.log(response);
              })
          }
        >
          Get Cookie
        </a>
      </header>
    </div>
  );
}

export default App; 
```

### 安全 cookies

正如你可能注意到的，我在上面的例子中使用了不安全的 cookies。这些仅用于开发/本地目的。如果你想在生产中使用它，你必须考虑安全性。安全 cookies 只能在 https 上工作，所以你也必须注意这一点。一个好主意是将 cookies 的安全性设置为依赖于 NODE_ENV，这样我们在开发和部署到 prod 时就不必记住它。

```
// index.js

===

app.use(
  session({
    secret: process.env.LOGIN_SERVER_SECRET,
    saveUninitialized: true,
    resave: true,
    cookie: {
      httpOnly: true,            // change both to true
      secure: true,
    },
  }),
);

===

// dynamic change
const isCookieSecure = 
  process.env.NODE_ENV === "production" ? true : false;

// add cors middleware to route 
app.get("/cookie", cors(corsOptions), (req, res) => {
  const options = {
    secure: isCookieSecure,
    httpOnly: isCookieSecure,
    domain: ".your.domain.com"
  }

return res
    .cookie("cookieName", "cookieValue", options)
    .status(200)
    .send("cookie sent")
}) 
```

基本就是这样。你可以添加任意多的应用程序和服务器到你的 docker 中，并在任何地方享受 cookies。它们将在请求和响应头中自动传递。感谢大家走到这一步，希望这对某些人有用:)

在 MDN 上阅读更多关于 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 、[选项](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS)和[饼干](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)的信息。如有任何问题或反馈，请发表评论。谢谢🙏