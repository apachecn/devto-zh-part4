# 如何用 TypeScript 在 Koa 中重定向

> 原文：<https://dev.to/suin/how-to-redirect-in-koa-with-typescript-4ei2>

这篇文章解释了如何用 TypeScript 在 Koa 中重定向。

## 如何在 Koa 中重定向

`redirect()`方法在`ctx`对象中可用。所以要重定向，就叫它。

```
ctx.redirect(url) 
```

Enter fullscreen mode Exit fullscreen mode

## 完整的例子`ctx.redirect`

```
import Koa from "koa";
import _ from "koa-route";

const app = new Koa()
app.use(_.get('/old', async ctx => {
    ctx.redirect('/new')
}))
app.use(_.get('/new', async ctx => {
    ctx.body = 'redirected!'
}))

app.listen(4000) 
```

Enter fullscreen mode Exit fullscreen mode

当您向路径`/old`发送请求时，服务器将响应 302 状态。另外，`Location`头值将是传递给`redirect`方法的 URL。

```
HTTP/1.1 302 Found
Connection: keep-alive
Content-Length: 39
Content-Type: text/html; charset=utf-8
Date: Mon, 02 Sep 2019 06:01:51 GMT
Location: /new

Redirecting to <a href="/new">/new</a>. 
```

Enter fullscreen mode Exit fullscreen mode