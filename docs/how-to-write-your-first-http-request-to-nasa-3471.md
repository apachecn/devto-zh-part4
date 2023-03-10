# 如何给 NASA 写第一个 HTTP 请求

> 原文：<https://dev.to/stacktracy/how-to-write-your-first-http-request-to-nasa-3471>

我很高兴向您介绍 HTTP 请求，并为您提供启动和运行的工具和资源。

学习如何使用 API 是提升前端开发人员水平的好方法。这也是了解如何检索数据以及哪些数据可供您使用的一种有趣方式。

你需要做的第一件事是通过填写这张[表格](https://api.nasa.gov/index.html#apply-for-an-api-key)向 NASA 申请 API 密匙。

你可以在这个[视频](https://www.youtube.com/watch?v=1yFggyk--Zo)中了解更多关于什么是 API key 的信息。
API 密匙会给你与 NASA API 沟通的能力。注册后，您将立即被定向到一个带有新 API 密钥的页面。
应该是这样的:

[![](img/41da01842b7d1da993749ab44e401cd8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0UdBjlh4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9hahe5ymk1xcq6s21t8l.png) 
*敏感信息被模糊化以保护用户的身份和个人 API 密钥*

您可以使用新的 NASA API 密钥提出许多不同的请求。

我个人最喜欢的包括:

*   [小行星——近地小行星](https://api.nasa.gov/api.html#NeoWS):根据它们最接近地球的日期识别小行星。
*   火星车照片:传回美国宇航局的好奇号、机遇号和勇气号火星车收集的照片
*   允许用户每天浏览整个地球，并在自然事件发生时寻找它们。风暴经常出现在热带地区，沙漠上的沙尘暴，夏天的森林火灾。

请参见 [NASA API 文档](https://api.nasa.gov/index.html)获取完整的功能列表。

# 代码

我将使用 JavaScript 的 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 并一步一步地向您展示如何让一切运行起来。

### 第一步

设置 Fetch 以使用您选择的 NASA API 发出请求。您将需要您试图从中请求数据的 API 的 URL。我选择使用我的第一个例子——小行星新闻的 URL:

`https://api.nasa.gov/neo/rest/v1/neo/browse?api_key=DEMO_KEY`

### 第二步

您会注意到 URL 末尾的`DEMO_KEY`。这是您将使用自己的个人 API 密钥替换的内容。
一旦添加了 API 密钥，URL 看起来应该与此非常相似:

`https://api.nasa.gov/neo/rest/v1/neo/browseapi_key=34oLy4FhODjKGewo2VIy8dsfgSGq6Qb1TQEpAX5s`

唯一的区别是您的 API 密匙是唯一的，与上面的不同。

Fetch 方法将上面列出的 URL 作为字符串形式的强制参数，如下所示:

`fetch("https://api.nasa.gov/neo/rest/v1/neo/browseapi_key=34oLy4FhODjKGewo2VIy8dsfgSGq6Qb1TQEpAX5s")`

### 第三步

现在，我们需要使用 then 将一个承诺附加到 fetch 请求，该请求将被返回并解析为该请求的响应。
你会注意到我们构建了两个案例，一个是 then，一个是 catch——分别代表成功和失败: