# 构建 Reddit 刮刀:使用 Reddit OAuth 进行认证

> 原文：<https://dev.to/imtyrelchambers/build-a-reddit-scraper-authenticating-with-reddit-oauth-4519>

# **创建你的 Reddit 开发者应用**

我们将继续打造 Reddit Scraper 系列。这将使用 JavaScript 来完成！

为了开始使用 Reddit API 进行身份验证，我们需要创建一个开发人员应用程序，它将允许我们实际使用 Reddit API 的更高级的功能。

作为参考，你可以在这里找到我所说的一切:[https://github.com/reddit-archive/reddit/wiki/oauth2](https://github.com/reddit-archive/reddit/wiki/oauth2)

我将向你展示我是如何创建一个在我看来相当稳定的系统的。我做过一系列不同的测试，但那是主观的。

### **寻找合适的范围**

好的，我们需要做的第一件事是将用户引导到一个特殊的端点，该端点将为他们提供一个选择，要么允许我们的应用程序使用他们的帐户进行身份验证，要么不允许。

你必须自己填写需要你的应用名称和密码的部分。您可以在他们网站上创建 Reddit 应用程序的页面上找到这些值。

我的链接是这样的-

```
 const link = `https://www.reddit.com/api/v1/authorize?client_id=${process.env.REACT_APP_REDDIT_APP_NAME}&response_type=code&state=RANDOM_STRING&redirect_uri=${process.env.REACT_APP_REDDIT_REDIRECT}/signup&duration=permanent&scope=privatemessages identity`; 
```

Enter fullscreen mode Exit fullscreen mode

文档很好地解释了这里的每一个需求，但是有一件事让我犯了个错误，那就是试图解释实际的文档，以确定您需要什么范围。

范围是:您需要访问用户资料的哪些部分？如你所见，通过我的应用程序，我希望能够看到并获得用户的个人资料本身，所以我选择了身份，我需要能够发送/接收私人消息。

如果你去 https://reddit.com/dev/api，你会看到一份文件清单。刚开始的时候，这是非常令人难以承受的，而且没有很多东西可以帮助你度过难关。希望我能帮上忙。

在左侧栏中，它显示了您可以访问的端点。查找您认为在访问方面需要的内容，在特定部分的顶部，您会找到范围。这个范围是你需要添加到上面的 url 的范围部分。如果您需要多个范围，请确保在每个范围项目之间添加一个空格。

在右侧主内容栏中的每个副标题旁边，您还可以找到代表该端点的 REST 操作，以及访问它所需的范围(绿色矩形)。

所以现在我们可以使用我们的望远镜了。你可以把这个链接放到一个函数中，这个函数在点击时被调用，然后让浏览器打开一个新的标签，或者只是改变当前标签中的 url。用户将被带到 Reddit，在那里他们将允许或拒绝您的应用程序。

如果他们批准或拒绝，他们将被重定向回您的应用程序，到您首次创建 Reddit 应用程序时指定的 URL。Reddit 做出决定后，重定向 uri 是 Reddit 将重定向到的 URL，因此请确保您重定向回处理您注册过程的页面。

### **获取访问令牌**

现在，用户已经假设对我们的应用程序的请求说“是”。

我们现在必须向另一个端点发出 post 请求。但是在此之前，我们需要解析 URL 查询参数。

当用户被重定向回来时，URL 包含一个“code”参数，我们将使用该参数向该 URL 发出 POST 请求。

```
 const getParams = () => {
    const params = (new URL(window.location)).searchParams;
    const approvalStatus = params.get("code") ? params.get("code") : false;

    if ( approvalStatus !== false ) {
      UserStore.getAccessToken(approvalStatus).then(res => {
        setCredentials({...credentials, access_token: res.access_token, refresh_token: res.refresh_token})
      }).catch(console.log);
      setFlow(2);
      setApproved(true);
    } 
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果对你有帮助的话，这是我的代码，它解析 URL，然后按照解析 URL 的方式做它的事情，并决定之后做什么。然后，它调用另一个函数，这是我们授权流的核心部分。

```
 getAccessToken = async (token) => {
    if (!token) return null;
    const encode = window.btoa(`${process.env.REACT_APP_REDDIT_APP_NAME}:${process.env.REACT_APP_REDDIT_APP_SECRET}`);
    const redditTokens = await Axios.post('https://www.reddit.com/api/v1/access_token', 
      `grant_type=authorization_code&code=${token}&redirect_uri=${process.env.REACT_APP_REDDIT_REDIRECT}/signup`,
    {
      headers: {
        "Authorization": `Basic ${encode}`,
        'Content-Type': 'application/x-www-form-urlencoded'
      }
    })
    .then(res => {
      if (res.data.error) {
        return toast.error("Please re-authenticate");
      };
      return res.data;
    })
    .catch(console.log);

    return redditTokens;
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果成功的话，你将会是金色的，但是只有一个小时，然后你需要得到另一个密码。

当我们解析 URL 并取出“code”参数时，我们使用它来获取我们的访问令牌，我将它传递给上面的函数。

我们必须向
发出 POST 请求

```
https://www.reddit.com/api/v1/access_token 
```

Enter fullscreen mode Exit fullscreen mode

其中请求的主体是

```
grant_type=authorization_code&code=${token}&redirect_uri=${process.env.REACT_APP_REDDIT_REDIRECT}/signup 
```

Enter fullscreen mode Exit fullscreen mode

我们获取以前获得的代码，并将其传递到新 URL 的代码部分(此时只有一堆确认)。请务必阅读我在本文开头提到的 OAuth2 文档，它将帮助您了解如何构建 POST to 链接。我们必须确保对我们的应用密码和应用名称进行编码，并将其包含在我们的授权头中。这一开始对我来说有点难以理解，主要是如何编码。他们不会直截了当地告诉你如何去做，而主要是暗示一下。经过一些谷歌搜索和朋友的帮助，我们来到了这里:

```
window.btoa(`${process.env.REACT_APP_REDDIT_APP_NAME}:${process.env.REACT_APP_REDDIT_APP_SECRET}`); 
```

Enter fullscreen mode Exit fullscreen mode

哪个管用！

一旦我们收到 Reddit 的认证许可，就调用 getAccessToken 函数。一旦完成，我们都准备好了。

一个小时后，当我们需要向 OAuth 端点发出另一个请求时，问题就来了。

我所做的是为我的应用程序创建一个后端，保存我的用户模型。在注册过程中，我发送访问令牌和刷新令牌，并将其保存到数据库中。

我还创建了一个函数来检索这些令牌。在另一篇文章中，我将用我实际的后端而不是 Reddit 来检查验证流程。

我的 app 每次加载，只是为了方便起见，我调用刷新我的访问令牌的函数。

```
export const renewRefreshToken = async () => {
  const encode = window.btoa(`${process.env.REACT_APP_REDDIT_APP_NAME}:${process.env.REACT_APP_REDDIT_APP_SECRET}`);
  const token = await fetchTokens();
  const jwt = window.localStorage.getItem('token');

  if ( !token || !token.access_token ) return null;

  await Axios.post('https://www.reddit.com/api/v1/access_token', 
    `grant_type=refresh_token&refresh_token=${token.refresh_token}`,
  {
    headers: {
      "Authorization": `Basic ${encode}`,
      'Content-Type': 'application/x-www-form-urlencoded'
    }
  })
  .then(res => {
    saveTokensToDb(res.data.access_token, token.refresh_token, jwt);
    getCurrentAuthenticatedUser(res.data.access_token);
  })
  .catch(console.log);
} 
```

Enter fullscreen mode Exit fullscreen mode

重要的部分是调用 fetchTokens 函数并等待它完成，因为它所做的是，获取我们本地(localStorage)保存的 JWT 令牌，在后端解码它，并使用用户 ID 来查找用户。

然后，它返回两个令牌，这使得代码能够工作。到目前为止，我这样做还没有遇到任何问题。

希望这能阐明如何以一种基本的方式导航 Reddit 文档，也希望它能帮助你使用 JavaScript 进行 Reddit 认证。如果你能坚持到现在，我会尝试在这个系列中写更多的文章，非常感谢你的阅读！