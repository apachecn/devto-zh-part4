# XMLHttpRequest 介绍(XHR)

> 原文：<https://dev.to/attacomsian/introduction-to-xmlhttprequest-xhr-142j>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/http-requests-xhr)。*

* * *

XMLHttpRequest (XHR)是微软在 90 年代早期发明的，在 21 世纪前十年中期成为异步服务器交互的首选技术。

多亏了 XHR，这是第一次，更新网页的一部分而不需要重新加载整个页面成为可能。

`XMLHttpRequest`是所有现代浏览器中的内置浏览器对象，可用于在 JavaScript 中发出 HTTP 请求，以在 web 浏览器和服务器之间交换数据。

尽管名称中有“XML”这个词，`XMLHttpRequest`可以用来检索任何类型的数据，而不仅仅是 XML。我们可以用它来上传/下载文件，提交表单数据，跟踪进度等等。

## 基本 XHR 要求

要使用 XHR 发送 HTTP 请求，创建一个`XMLHttpRequest`对象，打开到 URL 的连接，然后发送请求。一旦请求完成，对象将包含有用的信息，如响应正文和 HTTP 状态代码。

让我们使用[JSONPlaceholder](https://jsonplaceholder.typicode.com/)test REST API 发送一个使用 XHR:
的 GET 请求

```
// create an XHR object
const xhr = new XMLHttpRequest();

// listen for `onload` event
xhr.onload = () => {
    // process response
    if (xhr.status == 200) {
        // parse JSON data
        console.log(JSON.parse(xhr.response));
    } else {
        console.error('Error!');
    }
};

// create a `GET` request
xhr.open('GET', 'https://jsonplaceholder.typicode.com/users');

// send request
xhr.send(); 
```

> `xhr.onload` event 只在现代浏览器中有效(IE10+，FireFox，Chrome，Safari)。如果想支持老浏览器，用`xhr.onreadystatechange`事件代替。

## `xhr.open()`法

在上面的例子中，我们向`open()`方法传递了 HTTP 方法和请求的 URL。该方法通常在`new XMLHttpRequest()`之后立即调用。我们可以用这个方法来指定请求的主要参数:

下面是这个方法的语法:

```
xhr.open(method, URL, [async, user, password]) 
```

*   `method` — HTTP 请求方式。可以是`GET`、`POST`、`DELETE`、`PUT`等。
*   `URL` —请求的 URL，一个字符串或一个 [URL 对象](https://attacomsian.com/blog/javascript-url-object)
*   `asnyc` —指定请求是否应该异步进行。默认值为`true`
*   `username` & `password` —用于基本 HTTP 认证的凭证

`open()`方法不打开到 URL 的连接。它只配置 HTTP 请求。

## `xhr.send()`法

```
xhr.send([body]) 
```

`send()`方法打开网络连接并将请求发送给服务器。它接受一个包含请求体的可选参数`body`。对于像`GET`这样的请求方法，你不需要传递主体参数。

## XHR 事件

三个最广泛使用的 XHR 事件如下:

*   `load` —当结果准备好时调用该事件。用`xhr.readyState == 4`等同于`xhr.onreadystatechange`事件。
*   `error` —当请求因网络中断或 URL 无效而失败时，将触发此事件。
*   `progress` —此事件在响应下载期间定期触发。它可用于报告大型网络请求的进度。

```
// listen for `load` event
xhr.onload = () => {
    console.log(`Data Loaded: ${xhr.status}  ${xhr.response}`);
};

// listen for `error` event
xhr.onerror = () => {
    console.error('Request failed.');
}

// listen for `progress` event
xhr.onprogress = (event) => {
    // event.loaded returns how many bytes are downloaded
    // event.total returns the total number of bytes
    // event.total is only available if server sends `Content-Length` header
    console.log(`Downloaded ${event.loaded} of ${event.total}`);
} 
```

## 请求超时

您可以通过以毫秒为单位指定时间来轻松配置请求超时:

```
// set timeout
xhr.timeout = 5000; // 5 seconds

// listen for `timeout` event
xhr.ontimeout = () => console.log('Request timeout.', xhr.responseURL); 
```

> `xhr.responseURL`属性在跟踪所有重定向后返回一个`XMLHttpRequest`实例的最终 URL。这是取回`Location`报头的唯一方法。

## 响应类型

我们可以使用`xhr.responseType`属性来设置期望的响应格式:

*   空(默认)或`text` —纯文本
*   `json` —解析后的 JSON
*   `blob` —二进制数据块
*   `document` — XML 文档
*   `arraybuffer` — `ArrayBuffer`用于二进制数据

让我们调用一个 RESTful API 来获得 JSON:
形式的响应

```
const xhr = new XMLHttpRequest();

xhr.open('GET', 'https://api.jsonbin.io/b/5d5076e01ec3937ed4d05eab/1');

// set response format
xhr.responseType = 'json';

xhr.send();

xhr.onload = () => {
    // get JSON response
    const user = xhr.response;

    // log details
    console.log(user.name); // John Doe
    console.log(user.email); // john.doe@example.com
    console.log(user.website); // http://example.com
} 
```

## 请求国事(`xhr.readyState`)

随着请求的进行，`XMLHttpRequest`对象改变状态。我们可以使用`xhr.readyState`属性访问当前状态。

这些状态是:

*   `UNSENT` (0) —初始状态
*   `OPENED` (1) —请求开始
*   `HEADERS_RECEIVED` (2) —收到的 HTTP 头
*   `LOADING` (3) —响应正在加载
*   `DONE` (4) —请求已完成

我们可以通过使用`onreadystatechange`事件:
来跟踪请求状态

```
xhr.onreadystatechange = function () {
    if(xhr.readyState == 1) {
        console.log('Request started.');
    }

    if(xhr.readyState == 2) {
        console.log('Headers received.');
    }

    if (xhr.readyState == 3) {
        console.log('Data loading..!');
    }
    if (xhr.readyState == 4) {
        console.log('Request ended.');
    }
}; 
```

## 中止请求

我们可以随时通过调用`xhr`对象
上的`abort()`方法来轻松中止 XHR 请求

```
xhr.abort(); // cancel request 
```

## 同步请求

默认情况下，XHR 发出异步请求，这对性能有好处。但是如果你想做一个明确的同步请求，只需将`false`作为第三个参数传递给`open()`方法。它将在`send()`暂停 JavaScript 执行，并在响应可用时继续:

```
xhr.open('GET', 'https://api.jsonbin.io/b/5d5076e01ec3937ed4d05eab/1', false); 
```

> **小心！** Chrome 对同步 XHR 请求显示以下警告:*【弃用】主线程上的同步 XMLHttpRequest 因其对最终用户体验的不利影响而被弃用。*

## HTTP 头

`XMLHttpRequest`允许我们设置请求头和读取响应头。我们可以通过调用`xhr`对象
上的`setRequestHeader()`方法来设置请求`Content-Type` & `Accept`头

```
// set request headers
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.setRequestHeader('Accept', '*/*'); // accept all 
```

类似地，如果你想读取响应头(除了`Set-Cookie`)，在`xhr`对象上调用`getResponseHeader()`:

```
// read response headers
xhr.getResponseHeader('Content-Type');
xhr.getResponseHeader('Cache-Control'); 
```

想要立即获得响应头吗？用`getAllResponseHeaders()`代替:

```
xhr.getAllResponseHeaders(); 
```

## XHR 发文请求

`XMLHttpRequest`提交表单数据的 POST 请求可以通过两种方式发送:

1.  仅使用 Ajax
2.  使用`FormData` API

第一种方法已经足够好了，除非你想上传一个文件并需要`multipart/form-data`编码。下面是我们如何用 URL 编码的表单数据发出 POST 请求:

```
const xhr = new XMLHttpRequest();

// configure a `POST` request
xhr.open('POST', '/login');

// prepare form data
let params = 'username=attacomsian&password=123456';

// set `Content-Type` header
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

// pass `params` to `send()` method
xhr.send(params);

// listen for `load` event
xhr.onload = () => {
   console.log(xhr.responseText);
} 
```

**想做一个 JSON POST 请求？**确保使用 [JSON.stringify()](https://attacomsian.com/blog/json-parse-stringify#jsonstringify) 将 JSON 数据转换成字符串，并将`Content-Type`头设置为`application/json` :

```
const xhr = new XMLHttpRequest();

// configure a `POST` request
xhr.open('POST', '/login');

// create a JSON object
const json = {
    username: 'attacomsian',
    password: '123456'
};

// set `Content-Type` header
xhr.setRequestHeader('Content-Type', 'application/json');

// pass `params` to `send()` method
xhr.send(JSON.stringify(params));

// listen for `load` event
xhr.onload = () => {
   console.log(xhr.responseText);
} 
```

## 跨产地请求&cookie

`XMLHttpRequest`可以发送跨来源请求，但需要采取特殊的安全措施。要从不同的服务器请求资源，服务器必须使用 CORS(跨源资源共享)明确支持这一点。

就像[获取 API](https://attacomsian.com/blog/javascript-fetch-api#fetch-and-cookies) ，XHR 不发送 cookies 和 HTTP-authorization 到另一个来源。要发送 cookies，您可以使用`xhr`对象的`withCredentials`属性:

```
xhr.withCredentials = true; 
```

## XHR vs jQuery

像`$.ajax()`这样的 jQuery 包装方法使用了 XHR，并提供了一个更高层次的抽象来简化开发人员的工作。使用 jQuery，我们可以将上面的代码翻译成几行:

```
$.ajax('https://jsonplaceholder.typicode.com/users')
    .done(data => {
        console.log(data);
    }).fail(err => {
        console.error('Error:', err);
    }); 
```

## XHR vs 获取 API

[获取 API](https://attacomsian.com/blog/javascript-fetch-api) 是一个基于[承诺的](https://attacomsian.com/blog/promises-javascript)XHR 的现代替代品。它很干净，更容易理解，在 [PWA 服务人员](https://attacomsian.com/blog/service-workers-javascript)中大量使用。

上面的 XHR 例子可以转换成更简单的基于`fetch()`的代码，甚至可以自动解析返回的 JSON:

```
fetch('https://jsonplaceholder.typicode.com/users')
    .then(res => res.json())
    .then(json => console.log(json))
    .catch(err => console.error('Error:', err)); 
```

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。