# 我叫 CORS。CORS。

> 原文：<https://dev.to/aidiri/the-name-s-cors-rack-cors-27og>

我最新的项目需要使用 Rails 来创建一个 API 后端。Ruby 有一个漂亮的`--api`标志，可以创建一个只包含您需要的文件夹的 repo。它减少了你在创建一个新的 Rails 应用程序时会得到的很多额外的文件夹。它还会自动在您的 gem 文件中添加一行，以包含 Rack-CORS。

按照说明，我在 gem 文件中取消了对`rack-cors`的注释，以确保它“工作”，并将一些额外的代码复制粘贴到我的`application.rb`文件中，“以允许 CORS 工作”。完成了。酷毙了。

像往常一样，我完全被难住了。我刚刚做了什么，任何事情实际上在做什么，这有什么意义，甚至什么是生命的意义？？我继续前进，希望以后能明白。

我从来没有得到过这样的解释。所以我决定自己研究一下。瞬间就后悔了。

* * *

## 什么是 CORS？

任何时候你想把来自不同来源的资源带入你的网络应用程序，你可以实现跨来源资源共享，简称 CORS。CORS 标准化了我们检索跨来源资源的方式。怎么会？…

> [...它使用额外的 HTTP 头来告诉浏览器，让在一个源(域)上运行的 web 应用程序有权从另一个源的服务器上访问选定的资源。](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

这意味着 CORS 会告诉你的浏览器，当你的应用试图访问一个不在其原始域的*资源时，你的应用可以访问哪些资源。*

CORS 需要这样做的唯一原因是单一来源政策，该政策规定只能请求来自单一来源的资源。出于明显的安全原因，浏览器默认遵守这一策略。但是这意味着我们需要想办法允许跨来源的请求通过。

我们通过在请求中包含正确的 CORS 报头来做到这一点。

*边注:* JSONP(带填充的 JSON)是解决同源策略的另一种方式。因为 HTML 脚本标签是跨来源限制的例外，所以 JSONP 只是将数据转换成 HTML 脚本标签来绕过单来源策略。不可否认它有点“粗糙”,所以不再真正使用，除非浏览器不支持 CORS。 [*](https://dev.socrata.com/docs/cors-and-jsonp.html)

* * *

## 什么是齿条::CORS？

Rack::CORS 是一个中间件宝石，用于 Rack 兼容的 web 应用程序，帮助您更轻松地实现 CORS。它将事情分解开来，使那些跨域请求成为一个更干净、更容易的过程。另外，错误消息非常有用。

### 如何使用

Rack CORS 的[文档](https://github.com/cyu/rack-cors)有助于解释在哪里添加什么，但如果你不太了解 CORS 的总体情况，可能很难理解你在看什么。

首先，正如文档所解释的，您希望打开您的`config/application.rb`文件，并将类似这样的内容添加到您的代码中:

```
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'example.com', 'localhost:3000'
    resource '/publicStuff/*', headers: :any, methods: [:get, :post]
    resource '/myStuff/*', headers: :any, methods: :any
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   第一行使用`insert_before 0`,这样 Rack CORS 可以在任何其他可能干扰它的中间件之前运行。

*   第三行是您定义应用程序将从哪个来源接受资源的地方。这将是您想要通过的源的域名。

*   第四行做了同样的事情，除了我们定义了我们希望允许通过的资源、*或路径*。

*   在定义了允许的资源路径的同一行中，您将定义允许哪些请求参数。最有可能的是，您将使用方法和头参数。如果你想允许多种方法，你可以使用括号符号来列出你的方法:`methods: [:get, :post, :options]`

让我们再看看上面的例子，把事情分解一下。

```
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'example.com', 'localhost:3000'
    resource '/publicStuff/*', headers: :any, methods: [:get, :post]
    resource '/myStuff/*', headers: :any, methods: :any
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   它允许来自`example.com`域或`localhost:3000`的请求。

*   允许的路径有:

    *   任何以`/publicStuff/`开头的路径
    *   `/myStuff/`路径。
    *   这些可能的路径可能类似于`example.com/publicStuff/records/`或`localhost:3000/myStuff/`。
*   对于`'/publicStuff/'`路径，它允许带有任何头的请求，但是只允许带有 GET 和 POST 方法的请求。

*   允许通过任何方法*和*任何头请求`'/myStuff/'`路径。

* * *

如果你正在创建一个只在你自己的电脑或本地主机上运行的小程序，你可能不需要担心定义具体的来源和路径:

你可能已经注意到了`'*'`通配符是一种“万事俱备”的符号。在定义来源时使用`'*'`通配符将允许来自任何来源的所有请求。对资源路径使用通配符时，情况也是如此。对于资源路径，您可以允许任何和所有资源路径(`resource '*'`)或者允许特定路径根的任何扩展(`resource '/examplepath/* '`)。

在定义参数时，您可能会希望使用类似于`headers: :any`的东西，而不是`'*'`通配符。

因此，如果您在小型 localhost 应用程序中专门使用`'*'`通配符，它可能看起来像这样:

```
config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*', headers: :any, methods: :any
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

那很好也很简单。

* * *

除了这里写的，还有很多关于 CORS 的内容，可能是一篇或两篇完整的博文。所以如果概念还是觉得有点摇摇欲坠，那也没关系！要成为一名优秀的程序员，您不一定需要理解其中的许多内容，但是如果您能够理解这几行代码在做什么，它将使一些获取请求错误变得更有意义。

编码快乐！

* * *

[同源策略- MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#Definition_of_an_origin)
[跨源资源共享(CORS) - MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
[CORS&JSONP](https://dev.socrata.com/docs/cors-and-jsonp.html)
[机架 CORS 文档](https://github.com/cyu/rack-cors)