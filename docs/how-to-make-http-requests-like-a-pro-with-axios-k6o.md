# 如何像专业人士一样用 Axios 发出 HTTP 请求

> 原文：<https://dev.to/bnevilleoneill/how-to-make-http-requests-like-a-pro-with-axios-k6o>

[![](img/4619116f2408d5480c20714c85c2367f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JUnLhqVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ej4uidryhropvnolwe9.jpg)

前端程序与服务器通信的最常见方式是通过 HTTP 协议。您可能熟悉 Fetch API 和`XMLHttpRequest`接口，它们允许您获取资源并发出 HTTP 请求。

如果您使用的是 JavaScript 库，那么它可能会附带一个客户端 HTTP API。例如，jQuery 的`$.ajax()`函数特别受前端开发人员的欢迎。但是，随着开发人员放弃这种库，转而支持本地 API，专用的 HTTP 客户机出现了，填补了这一空白。

在这篇文章中，我们将仔细研究 Axios，这是一个基于浏览器提供的`XMLHttpRequest`接口的客户端 HTTP API，并研究它在前端开发人员中受欢迎的关键特性。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 为什么是 Axios？

和 Fetch 一样，Axios 也是基于承诺的。但是，它提供了更强大、更灵活的特性集。相对于原生获取 API 的优势包括:

*   请求和响应拦截
*   简化的错误处理
*   XSRF 防护
*   支持上传进度
*   响应超时
*   取消请求的能力
*   对旧浏览器的支持
*   自动 JSON 数据转换

## 安装

您可以使用以下工具安装 Axios:

*   npm:

```
$ npm install axios 
```

Enter fullscreen mode Exit fullscreen mode

*   鲍尔包装经理:

```
$ bower install axios 
```

Enter fullscreen mode Exit fullscreen mode

*   或者内容传递网络:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 发出请求

发出 HTTP 请求就像将 config 对象传递给 Axios 函数一样简单。最简单的形式是，对象必须有一个`url`属性；如果没有提供方法，`GET`将被用作默认值。让我们看一个简单的例子:

```
// send a POST request
axios({
  method: 'post',
  url: '/login',
  data: {
    firstName: 'Finn',
    lastName: 'Williams'
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这对于那些使用过 jQuery 的`$.ajax`函数的人来说应该很熟悉。这段代码只是指示 Axios 向`/login`发送一个 POST 请求，将一个键/值对对象作为其数据。Axios 会自动将数据转换成 JSON，并作为请求体发送。

## 速记方法

Axios 还为执行不同类型的请求提供了一套简化方法。这些方法如下:

*   `axios.request(config)`
*   `axios.get(url[, config])`
*   `axios.delete(url[, config])`
*   `axios.head(url[, config])`
*   `axios.options(url[, config])`
*   `axios.post(url[, data[, config]])`
*   `axios.put(url[, data[, config]])`
*   `axios.patch(url[, data[, config]])`

例如，下面的代码展示了如何使用`axios.post()`方法编写前面的示例:

```
axios.post('/login', {
  firstName: 'Finn',
  lastName: 'Williams'
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 处理响应

一旦发出 HTTP 请求，Axios 会根据后端服务的响应，返回一个完成或拒绝的承诺。要处理结果，可以像这样使用`then()`方法:

```
axios.post('/login', {
  firstName: 'Finn',
  lastName: 'Williams'
})
.then((response) => {
  console.log(response);
}, (error) => {
  console.log(error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果承诺兑现，会调用`then()`的第一个自变量；如果承诺被拒绝，将调用第二个参数。根据[文档](https://www.npmjs.com/package/axios#response-schema)，履行值是包含以下信息的对象:

```
{
  // `data` is the response that was provided by the server
  data: {},

  // `status` is the HTTP status code from the server response
  status: 200,

  // `statusText` is the HTTP status message from the server response
  statusText: 'OK',

  // `headers` the headers that the server responded with
  // All header names are lower cased
  headers: {},

  // `config` is the config that was provided to `axios` for the request
  config: {},

  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance the browser
  request: {}
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，下面是从 GitHub API 请求数据时的响应:

```
axios.get('https://api.github.com/users/mapbox')
  .then((response) => {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });

// logs:
// => {login: "mapbox", id: 600935, node_id: "MDEyOk9yZ2FuaXphdGlvbjYwMDkzNQ==", avatar_url: "https://avatars1.githubusercontent.com/u/600935?v=4", gravatar_id: "", …}
// => 200
// => OK
// => {x-ratelimit-limit: "60", x-github-media-type: "github.v3", x-ratelimit-remaining: "60", last-modified: "Wed, 01 Aug 2018 02:50:03 GMT", etag: "W/"3062389570cc468e0b474db27046e8c9"", …}
// => {adapter: ƒ, transformRequest: {…}, transformResponse: {…}, timeout: 0, xsrfCookieName: "XSRF-TOKEN", …} 
```

Enter fullscreen mode Exit fullscreen mode

## 同时发出请求

Axios 更有趣的特性之一是它能够通过向`axios.all()`方法传递一组参数来并行发出多个请求。此方法返回单个 promise 对象，该对象仅在作为数组传递的所有参数都已解析时解析。这里有一个简单的例子:

```
// execute simultaneous requests 
axios.all([
  axios.get('https://api.github.com/users/mapbox'),
  axios.get('https://api.github.com/users/phantomjs')
])
.then(responseArr => {
  //this will be executed only when all requests are complete
  console.log('Date created: ', responseArr[0].data.created_at);
  console.log('Date created: ', responseArr[1].data.created_at);
});

// logs:
// => Date created:  2011-02-04T19:02:13Z
// => Date created:  2017-04-03T17:25:46Z 
```

Enter fullscreen mode Exit fullscreen mode

这段代码向 GitHub API 发出两个请求，然后记录对控制台的每个响应的`created_at`属性值。请记住，如果任何一个论点被拒绝，那么承诺将立即拒绝，原因是第一个承诺拒绝。

为了方便起见，Axios 还提供了一个名为`axios.spread()`的方法，将响应数组的属性分配给单独的变量。你可以这样使用这个方法:

```
axios.all([
  axios.get('https://api.github.com/users/mapbox'),
  axios.get('https://api.github.com/users/phantomjs')
])
.then(axios.spread((user1, user2) => {
  console.log('Date created: ', user1.data.created_at);
  console.log('Date created: ', user2.data.created_at);
}));

// logs:
// => Date created:  2011-02-04T19:02:13Z
// => Date created:  2017-04-03T17:25:46Z 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的输出与前面的示例相同。唯一的区别是`axios.spread()`方法用于从响应数组中解包值。

## 发送自定义标题

用 Axios 发送自定义头非常简单。只需传递一个包含头的对象作为最后一个参数。例如:

```
const options = {
  headers: {'X-Custom-Header': 'value'}
};

axios.post('/save', { a: 10 }, options); 
```

Enter fullscreen mode Exit fullscreen mode

## 转换请求和响应

默认情况下，Axios 会自动将请求和响应转换成 JSON。但是它也允许您覆盖默认行为并定义不同的转换机制。当使用只接受特定数据格式(如 XML 或 CSV)的 API 时，这种能力特别有用。

要在将请求数据发送到服务器之前对其进行更改，请在 config 对象中设置`transformRequest`属性。注意，这个方法只对`PUT`、`POST`和`PATCH`请求方法有效。你可以这样做:

```
const options = {
  method: 'post',
  url: '/login',
  data: {
    firstName: 'Finn',
    lastName: 'Williams'
  },
  transformRequest: [(data, headers) => {
    // transform the data

    return data;
  }]
};

// send the request
axios(options); 
```

Enter fullscreen mode Exit fullscreen mode

要在将数据传递给`then()`或`catch()`之前修改数据，可以设置`transformResponse`属性:

```
const options = {
  method: 'post',
  url: '/login',
  data: {
    firstName: 'Finn',
    lastName: 'Williams'
  },
  transformResponse: [(data) => {
    // transform the response

    return data;
  }]
};

// send the request
axios(options); 
```

Enter fullscreen mode Exit fullscreen mode

## 拦截请求和响应

HTTP 拦截是 Axios 的一个流行功能。有了这个特性，您可以检查和更改从您的程序到服务器的 HTTP 请求，反之亦然，这对于各种隐式任务非常有用，比如日志记录和身份验证。

乍一看，拦截器看起来非常像转换，但是它们在一个关键方面有所不同:与只接收数据和头作为参数的转换不同，拦截器接收整个响应对象或请求配置。

您可以像这样在 Axios 中声明一个请求拦截器:

```
// declare a request interceptor
axios.interceptors.request.use(config => {
  // perform a task before the request is sent
  console.log('Request was sent');

  return config;
}, error => {
  // handle the error
  return Promise.reject(error);
});

// sent a GET request
axios.get('https://api.github.com/users/mapbox')
  .then(response => {
    console.log(response.data.created_at);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

每当发送请求时，这段代码都会向控制台记录一条消息，然后一直等待，直到从服务器获得响应，此时它会将 GitHub 上的帐户创建时间打印到控制台。使用拦截器的一个优点是，您不必再为每个 HTTP 请求分别实现任务。

Axios 还提供了一个响应拦截器，允许您将来自服务器的响应转换回应用程序:

```
// declare a response interceptor
axios.interceptors.response.use((response) => {
  // do something with the response data
  console.log('Response was received');

  return response;
}, error => {
  // handle the response error
  return Promise.reject(error);
});

// sent a GET request
axios.get('https://api.github.com/users/mapbox')
  .then(response => {
    console.log(response.data.created_at);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 客户端支持针对 XSRF 的防护

跨站点请求伪造(或简称为 XSRF)是一种攻击 web 托管应用程序的方法，攻击者将自己伪装成合法的可信用户，以影响应用程序与用户浏览器之间的交互。执行这样的攻击有很多种方式，包括`XMLHttpRequest`。

幸运的是，Axios 通过允许您在发出请求时嵌入额外的身份验证数据来抵御 XSRF。这使服务器能够发现来自未授权位置的请求。下面是如何用 Axios 做到这一点:

```
const options = {
  method: 'post',
  url: '/login',
  xsrfCookieName: 'XSRF-TOKEN',
  xsrfHeaderName: 'X-XSRF-TOKEN',
};

// send the request
axios(options); 
```

Enter fullscreen mode Exit fullscreen mode

## 监控发文请求进度

Axios 的另一个有趣特性是监控请求进度的能力。这在下载或上传大文件时特别有用。Axios 文档中的[提供的示例](https://github.com/axios/axios/blob/master/examples/upload/index.html)让您很好地了解了如何做到这一点。但是为了简单和风格，我们将在本教程中使用 [Axios 进度条](https://github.com/rikmms/progress-bar-4-axios/)模块。

要使用这个模块，我们首先要做的是包含相关的样式和脚本:

```
<link rel="stylesheet" type="text/css" href="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/nprogress.css" />

<script src="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/index.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以这样实现进度条:

```
loadProgressBar()

const url = 'https://media.giphy.com/media/C6JQPEUsZUyVq/giphy.gif';

function downloadFile(url) {
  axios.get(url)
  .then(response => {
    console.log(response)
  })
  .catch(error => {
    console.log(error)
  })
}

downloadFile(url); 
```

Enter fullscreen mode Exit fullscreen mode

要改变进度条的默认样式，我们可以覆盖以下样式规则:

```
#nprogress .bar {
    background: red !important;
}

#nprogress .peg {
    box-shadow: 0 0 10px red, 0 0 5px red !important;
}

#nprogress .spinner-icon {
    border-top-color: red !important;
    border-left-color: red !important;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 取消请求

在某些情况下，你可能不再关心结果，想取消已经发出的请求。这可以通过使用取消令牌来完成。Axios 1.5 版本增加了取消请求的功能，该功能基于[可取消承诺提议](https://github.com/tc39/proposal-cancelable-promises)。这里有一个简单的例子:

```
const source = axios.CancelToken.source();

axios.get('https://media.giphy.com/media/C6JQPEUsZUyVq/giphy.gif', {
  cancelToken: source.token
}).catch(thrown => {
  if (axios.isCancel(thrown)) {
    console.log(thrown.message);
  } else {
    // handle error
  }
});

// cancel the request (the message parameter is optional)
source.cancel('Request canceled.'); 
```

Enter fullscreen mode Exit fullscreen mode

您还可以通过向`CancelToken`构造函数传递一个 executor 函数来创建一个取消令牌，如下所示:

```
const CancelToken = axios.CancelToken;
let cancel;

axios.get('https://media.giphy.com/media/C6JQPEUsZUyVq/giphy.gif', {
  // specify a cancel token
  cancelToken: new CancelToken(c => {
    // this function will receive a cancel function as a parameter
    cancel = c;
  })
}).catch(thrown => {
  if (axios.isCancel(thrown)) {
    console.log(thrown.message);
  } else {
    // handle error
  }
});

// cancel the request
cancel('Request canceled.'); 
```

Enter fullscreen mode Exit fullscreen mode

## 库

Axios 在开发人员中越来越受欢迎，这导致了丰富的第三方库选择来扩展其功能。从测试人员到日志记录人员，在使用 Axios 时，您可能需要的几乎所有附加功能都有一个库。以下是目前可用的一些流行库:

*   axios-vcr :用 JavaScript 记录和重放请求
*   [axios-response-logger](https://github.com/srph/axios-response-logger) :记录响应的 axios 拦截器
*   [Axios-方法-覆盖](https://github.com/jacobbuck/axios-method-override) : Axios 请求方法覆盖插件
*   [axios 扩展](https://github.com/kuitos/axios-extensions) : Axios 扩展库，包括节流和缓存获取请求特性
*   [axios-api-versioning](https://weffe.github.io/axios-api-versioning) :为 axios 添加易于管理的 api 版本
*   [axios-cache-plugin](https://github.com/jin5354/axios-cache-plugin) :帮助你在使用 axios 时缓存 GET 请求
*   axios-cookiejar-support :为 axios 添加 tough-cookie 支持
*   [react-hooks-axios](https://github.com/use-hooks/react-hooks-axios) :定制 axios 的 react 挂钩
*   [moxios](https://github.com/axios/moxios) :模拟 Axios 的测试请求
*   Redux-Saga-requests:Redux-Saga 插件，用于简化 AJAX 请求的处理
*   axios-fetch :由 axios 客户端支持的 Web API Fetch 实现
*   [axios-curlize](https://www.npmjs.com/package/axios-curlirize):将任何 Axios 请求记录为控制台中的 curl 命令
*   [axios-actions](https://github.com/davestewart/axios-actions) :将端点捆绑为可调用、可重用的服务
*   [mocha-axios](https://github.com/jdrydn/mocha-axios) :使用 axios 的 mocha 的 HTTP 断言
*   axios-mock-adapter :允许您轻松模拟请求的 axios 适配器
*   [axios-debug-log](https://github.com/Gerhut/axios-debug-log) :使用调试库记录请求和响应的 axios 拦截器
*   [Redux-Axios-中间件](https://github.com/svrcekmichal/redux-axios-middleware) : Redux 中间件，用于通过 Axios HTTP 客户端获取数据
*   [axiolist](https://github.com/Gerhut/axiosist):基于 Axios 的 supertest:将 nNode.js 请求处理程序转换为 Axios 适配器，用于 Node.js 服务器单元测试

## 浏览器支持

说到浏览器支持，Axios 是非常可靠的。即使是 IE 11 等较老的浏览器也能很好地与 Axios 配合使用。

| 铬合金 | 火狐 | 狩猎之旅 | 边缘 | IE |
| ![heavy check mark](img/dc1651d404ba5b3f1b76bf088073fb64.png "heavy check mark") | ![heavy check mark](img/dc1651d404ba5b3f1b76bf088073fb64.png "heavy check mark") | ![heavy check mark](img/dc1651d404ba5b3f1b76bf088073fb64.png "heavy check mark") | ![heavy check mark](img/dc1651d404ba5b3f1b76bf088073fb64.png "heavy check mark") | 11 |

## 包装完毕

Axios 在开发者中如此受欢迎有一个很好的原因:它包含了许多有用的功能。在这篇文章中，我们仔细研究了 Axios 的几个关键特性，并学习了如何在实践中使用它们。但是 Axios 还有很多方面我们没有讨论。因此，请务必查看 Axios GitHub 页面以了解更多信息。

有没有一些使用 Axios 的小技巧？请在评论中告诉我们！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://logrocket.com/signup/)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[如何像专业人士一样用 Axios](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) 发出 HTTP 请求最先出现在[博客](https://blog.logrocket.com)上。