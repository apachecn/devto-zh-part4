# 用 Netlify 函数隐藏你的 API 键

> 原文：<https://dev.to/fabiorosado/hide-your-api-keys-with-netlify-functions-93m>

三月份我发表了一篇关于如何在 Gatsby 中处理表单提交并使用 Airtable 作为后端的教程。我很确信通过用`process.env`隐藏你的 API 密匙，它们会被隐藏起来。

我看的教程和博客帖子，都说如果你只是用`process.env`的话，你的钥匙会被藏起来。在某种程度上是真的，如果你检查你的浏览器上的代码，密钥将被隐藏。

然后 [Fran Caballero](https://twitter.com/fjcero) 在我的博客上评论说，在你提出请求后，密钥会显示在网络标签中。发生这种情况是因为 Airtable 希望您在请求头中将 API 键作为参数传递。

提醒一下，教程中的代码是这样的:

```
 handleSubmit = e => {
 const fields = {"fields": {"Name": this.state.name, "Notes": this.state.notes}}
 fetch("<https://api.airtable.com/v0/><account id>/<table name>", {
 method: "POST",
 headers: {"Authorization": `Bearer ${process.env.AIRTABLE_API}`,
 "Content-Type": "application/json"},
 body: JSON.stringify(fields)
 })
 .then(() => alert("Form Sent!"))
 .catch(error => alert(error))

 e.preventDefault();
 } 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，API 键在头中传递，当发出请求时，头通过 network 选项卡公开。这意味着，每个人都可以看到 API 密钥。

我需要一个隐藏 API 密钥的解决方案。

## Netlify 函数来救援！

我的大多数网站都由 Netlify 提供服务，Netlify 函数看起来是这个问题的解决方案。

网络功能就是运行在服务器上的功能。所以 API 密匙应该隐藏起来，不被窥探。

Netlify functions 的一个很棒的地方是，你可以在每个站点一个月内免费使用 125k 通话和 100 分钟。

为你的网站设置功能相当容易。你所需要做的就是创建一个文件夹，在你的项目的根目录下，在这个文件夹里面，你将放置你的函数。然后告诉 Netlify 在哪里找到这个文件夹。

我喜欢把所有和网络生活有关的东西放在一个叫`.netlify`的文件夹里。在这个`.netlify`文件夹中，我刚刚创建了另一个名为`functions`的文件夹。

现在我需要做的就是告诉 Netlify 我的函数在哪里。为此，您只需登录您的 Netlify 帐户，选择将使用这些功能的站点，然后按“功能”选项卡。在这个标签中，你只需要写下你的文件夹的路径。

就我而言，我只写了`.netlify/functions`。然后你可以推送新的更改或重新部署你的站点，Netlify 会自动找到该文件夹——你会收到一封电子邮件，告诉你现在正在使用你站点上的功能。

## Netlify 函数规则

网络功能允许您部署 AWS 功能，而无需拥有 AWS 帐户。现在，你可以使用 Javascript 或 Go 编写你的函数。

您的 javascript 函数将需要导出一个处理程序，应该如下所示:

```
 exports.handler = function(event, context, callback) {
 // your server-side functionality
 } 
```

Enter fullscreen mode Exit fullscreen mode

当你调用一个函数时，处理程序将会得到一个事件对象，看起来像这样:

```
 {  "path":  "Path parameter",  "httpMethod":  "Incoming request's method name"  "headers":  {Incoming  request  headers}  "queryStringParameters":  {query  string  parameters  }  "body":  "A JSON string of the request payload."  "isBase64Encoded":  "A boolean flag to indicate if the applicable request payload is Base64-encode"  } 
```

Enter fullscreen mode Exit fullscreen mode

所以当你调用你的 netlify 函数时，你可以通过写`event.path`、`event.headers`和`event.method`来获得你添加的头文件、方法和路径。

最后，回调将是您运行 Netlify 函数时返回的内容。这是你处理成功/失败和你可能需要的任何反应的地方。

## 为 airtable 创建服务器端函数

我将使用上面的例子来说明如何处理表单提交并将它们发送到 airtable。

首先，在 netlify functions 文件夹中创建一个名为`airtable.js`的文件。

现在您需要混合处理程序格式和上面显示的获取请求。该函数将如下所示:

```
exports.handler = async (event, context, callback) => {
  const pass = (body) => {callback(null, {statusCode: 200, body: JSON.stringify(body)})}

  try {
    let response = await fetch("https://api.airtable.com/v0/<account id>/<table name>", 
  {
   method: event.httpMethod,
   headers: {
     "Authorization": `Bearer ${process.env.AIRTABLE_API}`, 
     "Content-Type": "application/json"
  },
   body: event.body
  })
   let data = await response.json()
   await pass(data)
 } catch(err) {
     let error = {
       statusCode: err.statusCode || 500,
       body: JSON.stringify({error: err.message})
 }
  await pass(error)
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用`process.env`来获取 Airtable API 键。Netlify 将自动从环境变量中获取密钥，因为它们是服务器端的。

## 将表单连接到 Netlify 函数

现在我们有了自己的函数，剩下要做的就是将表单连接到 netlify 函数。Netlify 让我们很容易做到这一点，你所需要做的就是在 fetch 请求中使用函数的路径。

假设我们的表单位于名为`Notes`的组件上，它的完整路径是`your-awesome-site/src/components/Notes.js`，我们需要将表单改为:

```
handleSubmit = e => {
  const fields = {
    "fields": {
      "Name": this.state.name, 
      "Notes": this.state.notes
      }
     }
    fetch("../../.netlify/functions/airtable", {
    method: "POST",
    body: JSON.stringify(fields)
    })
    .then(() => alert("Form Sent!"))
    .catch(error => alert(error))

    e.preventDefault();
 } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们正在从根文件夹中获取 Netlify 函数 airtable。此外，我们从请求中获取 headers 参数，因为我们在服务器端函数中使用它。

因此，这个获取请求将只调用 netlify 函数，并将表单字段传递给服务器端函数，而服务器端函数就是调用 Airtable API 的函数。

这就是隐藏 API 键和使用 Netlify 函数所需要做的全部工作。如果你想记录你的网络生活功能，你可以使用`console.log`,这个日志可以在你的网络生活账户的功能界面中找到——从功能标签中。