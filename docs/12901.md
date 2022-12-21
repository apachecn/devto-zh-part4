# 用 Osgood 托管一个静态站点和联系表单

> 原文：<https://dev.to/tlhunter/hosting-a-static-site-and-contact-form-with-osgood-5c1g>

我们新发布的项目， [Osgood](https://github.com/IntrinsicLabs/osgood) ，是一个构建在服务器上运行 JavaScript 的 HTTP 应用程序的工具。它强调安全性，并在默认情况下强制不同路由之间的隔离。

我们添加的特性之一是提供静态内容的能力。在这篇文章中，我们将展示建立一个几乎完全静态的网站是多么容易，但是需要一些服务器端的逻辑，在这个例子中是一个联系表单。

这个应用程序将利用 [Mailgun](https://www.mailgun.com/) 发送电子邮件。请注意，我们使用的是演示 Mailgun API 密钥和演示 URL——如果您想将此应用程序用于您自己的用途，您需要创建一个帐户并使用真实的凭据。

## 申请文件(app.js)

这个文件处理 Osgood 的全局配置，如何路由请求，以及不同路由的策略。每个应用程序都需要一个应用程序文件。

```
#!/usr/bin/env osgood 
app.port = 3000;

app.route('POST', '/contact', 'contact.js', policy => {
  // Mailgun demo URL
  policy.outboundHttp.allowPost('https://api.mailgun.net/v3/samples.mailgun.org/messages');
});

// GET requests beginning with / will map to the ./static directory
app.static('/', './static'); 
```

该文件指定了两条不同的路线。第一个比较具体，捕获对`/contact`端点的 POST 请求。当发出这些请求时，它将触发`contact.js`工人文件。根据策略配置，只允许该路由向指定的 Mailgun 端点发出 POST 请求。发出 I/O 请求的任何其他尝试，比如向 GitHub API 发出 GET 请求，都会失败。

另一条路线是静态内容。任何其他请求(即以`/`开始的`GET`请求，又名非`POST /contact`)将转换为在`./static`目录中的查找。当请求一个目录时，Osgood 默认提供一个名为`index.html`的文件。

## 职工档案(contact.js)

这个 worker 文件将处理由我们的应用程序定义的唯一路由，它与提供静态内容无关。

Osgood workers 通过导出一个默认函数来工作，通常带有`async`关键字。这个函数返回的值将用于确定这个特定 HTTP 请求的响应。

这个函数本质上可以被认为是应用程序的控制器。在这个函数中，我们执行许多验证逻辑。一旦我们满意了，我们就生成了一个 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 类的实例。一旦实例附加了必要的值，我们就可以通过发出`fetch`请求将数据发送给 Mailgun。(如果您习惯于编写在 web 浏览器中运行的 JavaScript，那么可用的 API 应该看起来非常熟悉。)

最后，一旦请求被发送，我们通知用户他们的电子邮件已经成功发送。

```
export default async (request) => {
  try {
    var req = await request.json();
  } catch (e) {
    return json({error: 'CANNOT_PARSE', message: "Invalid JSON Payload Provided"}, 400);
  }

  const email = new FormData();

  if (!('email' in req) || !('name' in req) || !('message' in req)) {
    return json({error: 'MISSING_FIELDS', message: "Invalid JSON Payload Provided"}, 400);
  } else if (typeof req.email !== 'string' || typeof req.name !== 'string' || typeof req.message !== 'string') {
    return json({error: 'INVALID_FIELD_TYPES', message: "Invalid JSON Payload Provided"}, 400);
  } else if (!req.name || !req.email || !req.message) {
    return json({error: 'EMPTY_VALUE', message: "Please supply all fields"}, 400);
  }

  email.append('from', `${req.name} <${req.email}>`);
  email.append('to', 'spam@intrinsic.com');
  email.append('subject', "Contact form email");
  email.append('text', req.message);

  try {
    // Mailgun demo URL
    await fetch('https://api.mailgun.net/v3/samples.mailgun.org/messages', {
      method: 'POST',
      headers: new Headers({
        // Mailgun demo key
        Authorization: 'Basic ' + btoa('api:key-3ax6xnjp29jd6fds4gc373sgvjxteol0')
      }),
      body: email
    });
  } catch (e) {
    return json({error: 'CANNOT_SEND', message: "Cannot parse provided JSON"}, 500);
  }

  return json({success: true, message: "Email has been sent"});
}

// helper for setting status code
function json(obj, status = 200) {
  const headers = new Headers({
    'Content-Type': 'application/json'
  });

  const body = JSON.stringify(obj);

  const response = new Response(body, { headers, status });

  return response;
} 
```

## 运行项目

我们的项目具有以下文件层次结构来支持该应用程序:

*   `static/`
    *   `scripts/`
    *   `contact.js`
    *   `styles/`
    *   `main.css`
    *   `index.html`
*   `app.js`
*   `contact.js`

然后，我们通过执行以下命令来运行应用程序:

```
$ osgood app.js 
```

此后，可通过访问`http://localhost:3000`查看应用程序。

本例中使用的文件可以从这里下载: [osgood/examples/contact](https://github.com/IntrinsicLabs/osgood/tree/master/examples/contact)

您还需要安装一个最近的 [Osgood 版本](https://github.com/IntrinsicLabs/osgood/releases)来运行这个例子。

* * *

看看这个系列的下一篇文章，Osgood 和 CouchDB。