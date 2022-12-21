# RESTful API 版本控制

> 原文：<https://dev.to/chris_l_ayers/restful-api-versioning-bo6>

我做了很长时间的开发者，编写 API 和客户端来消费它们。当一个 API 存在足够长的时间，它需要改变。我过去曾使用许多不同的技术对 API 进行版本控制。有些成功，有些痛苦。现在我意识到这种讨论就像 VI/Emacs 冲突、Tab/Space 战争和西班牙宗教裁判所，但这是一个很好的话题。谈到 API 版本控制，有几种主要的风格:

*   统一资源定位器
*   查询参数
*   接受标题
*   自定义请求标题

## 网址

这是很多人开始的地方，你可能已经看到了。您可以很容易地看到您正在调用的 api 的版本。而且很多时候在后台，基础 api 指向最新版本。包括我自己在内的许多人都认为 RESTful APIs 中的 URL 应该代表一个实体，而不是实体的一个版本。例如，/api/v1/users/27 和/api/v2/users/27 不是两个不同的用户。当您添加新版本时，就断开了与资源的永久链接。并且你绑定了一个资源的 url，而不是它的一个版本。

```
/api/users -> /api/v2/users
/api/v1/users
/api/v2/users 
```

Enter fullscreen mode Exit fullscreen mode

## 查询参数

人们可以对 API 进行版本控制的另一种方式是通过查询参数。这保持了 URL 的一致性，但是增加了一个额外的参数。在我看来这很好，你是在给*请求*而不是**资源**添加额外的信息。它可以根据请求而改变，但不会改变资源的永久链接或 url。

```
/api/users -> /api/users?api-version=1.0
/api/users?api-version=1.0
/api/users?api-version=2.0 
```

Enter fullscreen mode Exit fullscreen mode

## 接受表头

我看到的 api 版本控制的第三种方式是 http(s)请求上的 accept 头。接受标题描述了您希望如何接收数据。作为 http 头的一部分，这可以在缓存期间生效，如果没有明确指定，可以返回最新版本。我很喜欢这种方法，客户端告诉服务器它希望如何接收数据，格式*和版本*。

```
GET api/users HTTP/1.1
host: localhost
accept: application/json;v=2.0 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义表头

我看到的最后一种方法是定义一个定制的 http 头。只是将请求的版本作为标题添加到请求中。这不像其他方法那样容易与客户端兼容。

```
api-version: 1.0 
```

Enter fullscreen mode Exit fullscreen mode

## 实现

过去，我不得不自己卷。我从 URL 版本控制开始。当我使用 RESTful APIs 时，我意识到有一种更好的方法，并开始转向查询参数或接受头。现在有一些非常好的库可以为你处理很多这方面的事情。

微软有一个库。Net Framework 4.5 和。网芯。它叫做 ASPNet Api 版本控制，[https://github.com/microsoft/aspnet-api-versioning](https://github.com/microsoft/aspnet-api-versioning)。他们还发布了与 [Swagger](https://docs.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?view=aspnetcore-2.2) 合作的 api 文档库。老实说，它非常好，支持许多可以很好定制的版本控制方法。这遵循了[微软 REST Api 指南](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md#12-versioning)。

在[http://apistylebook.com/design/topics/versioning](http://apistylebook.com/design/topics/versioning)还有更多的参考资料可以查阅