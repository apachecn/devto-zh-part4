# Axios 和 fetch():应该使用哪个？

> 原文：<https://dev.to/bnevilleoneill/axios-or-fetch-which-should-you-use-95i>

**作者:[法拉兹·凯尔希尼](https://blog.logrocket.com/author/farazkelhini/)T3】✏️**

在我最近的文章“[如何像专业人士一样使用 Axios](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/#new_tab) 进行 HTTP 请求”中，我讨论了使用 Axios 库的好处。尽管如此，重要的是要承认 Axios 并不总是一个理想的解决方案，有时有更好的选项来发出 HTTP 请求。

毫无疑问，一些开发人员更喜欢 Axios 而不是内置 API，因为它易于使用。但是许多人高估了对这样一个图书馆的需求。API 完全能够再现 Axios 的关键特性，而且它还有一个额外的优势，即可以在所有现代浏览器中随时使用。

在本文中，我们将比较`fetch()`和 Axios，看看它们如何用于执行不同的任务。希望在本文结束时，您会对这两种 API 有更好的理解。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 基本语法

在我们深入研究 Axios 的更多高级功能之前，让我们将它的基本语法与`fetch()`进行比较。下面是如何使用 Axios 向 URL 发送带有自定义头的`POST`请求。Axios 会自动将数据转换为 JSON，所以您不必这样做。

```
// axios

const options = {
  url: 'http://localhost/test.htm',
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json;charset=UTF-8'
  },
  data: {
    a: 10,
    b: 20
  }
};

axios(options)
  .then(response => {
    console.log(response.status);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在将这段代码与产生相同结果的`fetch()`版本进行比较:

```
// fetch()

const url = 'http://localhost/test.htm';
const options = {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json;charset=UTF-8'
  },
  body: JSON.stringify({
    a: 10,
    b: 20
  })
};

fetch(url, options)
  .then(response => {
    console.log(response.status);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

请注意:

*   为了发送数据，`fetch()`使用 body 属性，而 Axios 使用 data 属性
*   `fetch()`中的数据是字符串型的
*   URL 作为参数传递给`fetch()`。然而，在 Axios 中，URL 是在选项对象中设置的

## 向后兼容

Axios 的主要卖点之一是其广泛的浏览器支持。即使像 IE11 这样的老浏览器也可以毫无问题地运行 Axios。而`Fetch()`则只支持 Chrome 42+，Firefox 39+，Edge 14+，Safari 10.1+(你可以在[上看到完整的兼容表我能用](https://caniuse.com/#search=Fetch)……)。

如果您使用 Axios 的唯一原因是向后兼容，那么您并不真正需要 HTTP 库。相反，你可以使用`fetch()`和像 [this](https://github.com/github/fetch) 这样的聚合填充，在不支持`fetch()`的 web 浏览器上实现类似的功能。要开始使用 fetch polyfill，请通过 npm 命令安装它:

```
npm install whatwg-fetch --save 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以这样提出请求:

```
import 'whatwg-fetch'
window.fetch(...) 
```

Enter fullscreen mode Exit fullscreen mode

请记住，您可能还需要一些旧浏览器中的 promise polyfill。

## 响应超时

在 Axios 中设置超时的简单性是一些开发者更喜欢它而不是`fetch()`的原因之一。在 Axios 中，可以使用 config 对象中可选的`timeout`属性来设置请求中止前的毫秒数。比如:

```
axios({
  method: 'post',
  url: '/login',
  timeout: 4000,    // 4 seconds timeout
  data: {
    firstName: 'David',
    lastName: 'Pollock'
  }
})
.then(response => {/* handle the response */})
.catch(error => console.error('timeout exceeded')) 
```

Enter fullscreen mode Exit fullscreen mode

`Fetch()`通过`AbortController`接口提供类似的功能。不过，它不像 Axios 版本那么简单:

```
const controller = new AbortController();
const options = {
  method: 'POST',
  signal: controller.signal,
  body: JSON.stringify({
    firstName: 'David',
    lastName: 'Pollock'
  })
};  
const promise = fetch('/login', options);
const timeoutId = setTimeout(() => controller.abort(), 4000);

promise
  .then(response => {/* handle the response */})
  .catch(error => console.error('timeout exceeded')); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用`AbortController.AbortController()`构造函数创建一个`AbortController`对象，这允许我们稍后中止请求。`signal`是`AbortController`的只读属性，提供了一种与请求通信或中止请求的方法。如果服务器在四秒钟内没有响应，调用`controller.abort()`，操作终止。

## 自动 JSON 数据转换

如前所述，Axios 在发送请求时会自动对数据进行字符串化(尽管您可以覆盖默认行为并定义不同的转换机制)。然而，当使用`fetch()`时，你必须手动操作。比较:

```
// axios
axios.get('https://api.github.com/orgs/axios')
  .then(response => {
    console.log(response.data);
  }, error => {
    console.log(error);
  });

// fetch()
fetch('https://api.github.com/orgs/axios')
  .then(response => response.json())    // one extra step
  .then(data => {
    console.log(data) 
  })
  .catch(error => console.error(error)); 
```

Enter fullscreen mode Exit fullscreen mode

数据的自动转换是一个很好的特性，但是同样，这不是你不能用`fetch()`做的事情。

## HTTP 拦截器

Axios 的一个关键特性是它能够拦截 HTTP 请求。当您需要检查或更改从应用程序到服务器的 HTTP 请求时，HTTP 拦截器就派上了用场，反之亦然(例如，日志记录、身份验证等)。).使用拦截器，您不必为每个 HTTP 请求编写单独的代码。

下面是如何在 Axios 中声明请求拦截器:

```
axios.interceptors.request.use(config => {
  // log a message before any HTTP request is sent
  console.log('Request was sent');

  return config;
});

// sent a GET request
axios.get('https://api.github.com/users/sideshowbarker')
  .then(response => {
    console.log(response.data);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，`axios.interceptors.request.use()`方法用于定义在发送 HTTP 请求之前要运行的代码。

默认情况下，`fetch()`没有提供拦截请求的方法，但是想出一个变通办法并不难。您可以覆盖全局 fetch 方法并定义自己的拦截器，就像这样:

```
fetch = (originalFetch => {
  return (...arguments) => {
    const result = originalFetch.apply(this, arguments);
      return result.then(console.log('Request was sent'));
  };
})(fetch);

fetch('https://api.github.com/orgs/axios')
  .then(response => response.json())
  .then(data => {
    console.log(data) 
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 下载进度

在加载大型资产时，进度指示器非常有用，尤其是对于网速较慢的用户。以前，JavaScript 程序员使用`XMLHttpRequest.onprogress`回调处理程序来实现进度指示器。

获取 API 没有`onprogress`处理程序。相反，它通过响应对象的 body 属性提供了一个`ReadableStream`的实例。

以下示例说明了如何使用`ReadableStream`在图像下载期间向用户提供即时反馈:

```
// original code: https://github.com/AnthumChris/fetch-progress-indicators

<div id="progress" src="">progress</div>
<img id="img">

<script>

'use strict'

const element = document.getElementById('progress');

fetch('https://fetch-progress.anthum.com/30kbimg/sunrise-baseline.jpg')
  .then(response => {

    if (!response.ok) {
      throw Error(response.status+'  '+response.statusText)
    }

    // ensure ReadableStream is supported
    if (!response.body) {
      throw Error('ReadableStream not yet supported in this browser.')
    }

    // store the size of the entity-body, in bytes
    const contentLength = response.headers.get('content-length');

    // ensure contentLength is available
    if (!contentLength) {
      throw Error('Content-Length response header unavailable');
    }

    // parse the integer into a base-10 number
    const total = parseInt(contentLength, 10);

    let loaded = 0;

    return new Response(

      // create and return a readable stream
      new ReadableStream({
        start(controller) {
          const reader = response.body.getReader();

          read();
          function read() {
            reader.read().then(({done, value}) => {
              if (done) {
                controller.close();
                return; 
              }
              loaded += value.byteLength;
              progress({loaded, total})
              controller.enqueue(value);
              read();
            }).catch(error => {
              console.error(error);
              controller.error(error)                  
            })
          }
        }
      })
    );
  })
  .then(response => 
    // construct a blob from the data
    response.blob()
  )
  .then(data => {
    // insert the downloaded image into the page
    document.getElementById('img').src = URL.createObjectURL(data);
  })
  .catch(error => {
    console.error(error);
  })

function progress({loaded, total}) {
  element.innerHTML = Math.round(loaded/total*100)+'%';
}

</script> 
```

Enter fullscreen mode Exit fullscreen mode

在 Axios 中实现进度指示器更简单，尤其是如果您使用 [Axios 进度条](https://github.com/rikmms/progress-bar-4-axios/)模块。首先，你需要包括以下风格和脚本:

```
<link rel="stylesheet" type="text/css" href="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/nprogress.css" />

<script src="https://cdn.rawgit.com/rikmms/progress-bar-4-axios/0a3acf92/dist/index.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以这样实现进度条:

```
<img id="img">

<script>

loadProgressBar();

const url = 'https://fetch-progress.anthum.com/30kbimg/sunrise-baseline.jpg';

function downloadFile(url) {
  axios.get(url, {responseType: 'blob'})
    .then(response => {
      const reader = new window.FileReader();
      reader.readAsDataURL(response.data); 
      reader.onload = () => {
        document.getElementById('img').setAttribute('src', reader.result);
      }
    })
    .catch(error => {
      console.log(error)
    });
}

downloadFile(url);

</script> 
```

Enter fullscreen mode Exit fullscreen mode

这段代码使用`FileReader` API 异步读取下载的图像。`readAsDataURL`方法将图像数据作为 Base64 编码的字符串返回，然后将其插入到`img`标签的`src`属性中以显示图像。

## 同时请求

为了同时发出多个请求，Axios 提供了`axios.all()`方法。只需将一个请求数组传递给这个方法，然后使用`axios.spread()`将响应数组的属性分配给不同的变量:

```
axios.all([
  axios.get('https://api.github.com/users/iliakan'), 
  axios.get('https://api.github.com/users/taylorotwell')
])
.then(axios.spread((obj1, obj2) => {
  // Both requests are now complete
  console.log(obj1.data.login + ' has ' + obj1.data.public_repos + ' public repos on GitHub');
  console.log(obj2.data.login + ' has ' + obj2.data.public_repos + ' public repos on GitHub');
})); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用内置的`Promise.all()`方法获得相同的结果。将所有获取请求作为数组传递给`Promise.all()`。接下来，通过使用一个`async`函数来处理响应，就像这样:

```
Promise.all([
  fetch('https://api.github.com/users/iliakan'),
  fetch('https://api.github.com/users/taylorotwell')
])
.then(async([res1, res2]) => {
  const a = await res1.json();
  const b = await res2.json();
  console.log(a.login + ' has ' + a.public_repos + ' public repos on GitHub');
  console.log(b.login + ' has ' + b.public_repos + ' public repos on GitHub');
})
.catch(error => {
  console.log(error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Axios 在一个紧凑的包中提供了一个易于使用的 API，可以满足您的大多数 HTTP 通信需求。但是，如果您更喜欢使用原生 API，没有什么可以阻止您实现 Axios 特性。

正如本文所讨论的，使用 web 浏览器提供的`fetch()`方法完全有可能重现 Axios 库的关键特性。最终，是否值得加载客户端 HTTP API 取决于您是否习惯使用内置 API。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

post [Axios 和 fetch():应该使用哪个？](https://blog.logrocket.com/axios-or-fetch-api/)最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。