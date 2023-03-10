# 将 google 登录添加到您的 web 应用程序- pt 1

> 原文：<https://dev.to/intricatecloud/adding-google-sign-in-to-your-web-app-pt-1-435k>

[https://www.youtube.com/embed/KwOmVpd1DUA](https://www.youtube.com/embed/KwOmVpd1DUA)

*   请查看我的新视频，我在视频中浏览了下面的代码！

陷入了一个兔子洞，弄不清如何在你的网络应用程序中添加“使用谷歌登录”功能？在这个系列中，我将介绍:

*   为什么您可能想要使用网站 JS 库的登录，并开始使用它
*   通过 javascript 添加库
*   将其添加到现有项目中(角度和反应)

如果你知道你在找什么，他们的文档会给你一个很好的起点，但是有一些地方他们的文档和其他在线指南会让人困惑。关于如何在现有项目中使用它，也只有最低限度的指导。

*   我是应该按照[谷歌身份文件](https://developers.google.com/identity/protocols/OAuth2UserAgent#example)还是需要[谷歌登录网站？](https://developers.google.com/identity/sign-in/web/)？
*   等等，为什么这两个指南如此不同？他们甚至加载不同的库！
*   这个应该很简单！我只是想加载一个用户的头像！！

“我如何用谷歌添加登录”的答案归结为一个你需要能够回答的问题:你到底想做什么？

*   你只是想看到用户的名字和照片，也许是他们的电子邮件吗？使用[登录网站库](https://developers.google.com/identity/sign-in/web/)(下面的完整演示)。
*   你有自己的用户数据库，并想提供谷歌登录作为一个额外的选择吗？这是联合登录，您可能想要使用他们的 OpenID 连接协议。这是 auth0、firebase、oneidentity 等平台使用的。
*   你希望能够与用户的谷歌帐户互动，并做一些事情，如查看他们的日历，或创建一个谷歌文档？你需要使用他们的 OAuth 工作流程。

出于本系列的目的，我将探讨什么时候应该使用使用 OpenID Connect 协议(也被联合登录系统使用)的网站登录库——这个库很容易被误解，但仍然非常有用。这个认证库的目标是让你通过用户的 Google 账户来识别用户。就是这样。

## 那么什么时候该用呢？

使用它的好理由:

*   您有一些存储在后端服务上的内容，只有在用户登录后才通过 API 交付
*   你只需要支持 Google/G-Suite 用户
*   您不需要授权用户(例如，允许一些用户成为管理员)
*   你的网站是公开的
*   你有一个单页的应用程序

值得考虑联合登录的用例:

*   您已经将**预先存在的用户内容**存储在后端服务上
*   您有一个内部网站，您想将它限制给来自特定域的用户。(例如，只有来自@example.com 的用户才能看到它)
*   您希望防止他人看到您的页面，除非他们已经登录。如果用户登录，这个库所能做的最好的事情就是显示/隐藏页面上的元素，但是如果你只在用户登录后从 API 加载数据，这就足够了。

该库设计用于 HTML/JS，并且只通过“使用 Google 登录”按钮与您的页面交互。您可以将它与 Angular/React 等其他框架集成，我将在本系列的下一部分介绍这些框架。

## 逐步添加 Google 登录

### 1。Hello World HTML

首先，你需要的只是一个 index.html 文件。

```
<!DOCTYPE html>
<html>

<head>
  Google Auth Demo
</head>
<body>
  <h1>Welcome to the Demo</h1>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 2。添加 Google 登录按钮

在添加按钮之前，您首先需要创建一个客户端 ID/secret，这是一种识别您(开发者)允许用户从 Google 获取身份信息并将其发送到您的网站的方式。

### 创建登录凭证

*   去谷歌 API 控制台-[https://console.developers.google.com/apis/dashboard](https://console.developers.google.com/apis/dashboard)
*   创建一个新项目，或者使用现有项目(如果您已经设置了一个项目)。
*   然后点击凭证->创建凭证-> OAuth 客户端 ID

这是我在这个演示中加入的内容:

名称:google-auth-demo
授权 Javascript 起源:[http://localhost:8080](http://localhost:8080)T3】授权重定向 URIs:空

*关于 Javascript 起源的一个注意事项:如果您有一个普通的 HTML 文件，并使用/home/dperez/index.html 这样的文件路径加载到浏览器中，这将不起作用。你需要在你的电脑上“服务”你的网站，这样你就有了一个 URL，即使它只是本地主机。您可以使用`python -m SimpleHTTPServer 8080`(这是常见的)来提供您当前的目录，或者您可以使用 npm 包，如 [http-server](https://www.npmjs.com/package/http-server) 。

然后你会得到一个客户 ID 和客户秘密。这是 Oauth 客户端的两个标识符。此时，您已经授权该客户机 ID 接受登录并向您返回用户信息。把它们抄下来。如果你不小心点出了屏幕，你可以稍后再复制它们。

### 将库+凭证+按钮添加到您的 HTML 中

在您的 index.html 页面中，将以下内容添加到您的 HTML 页面:

```
...
<head>
  ...
  <meta name="google-signin-client_id" content="your-client-id-goes-here">
  <script src="https://apis.google.com/js/platform.js" async defer></script>
</head>
<body>
  <h1>Welcome to the Demo</h1>
  <div class="g-signin2"></div>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

script 标签从 Google 获取读取您的`<meta>`标签的库，用作客户端 ID，然后自动用 CSS 类`.g-signin2`重新设计按钮的样式。

此时，如果您刷新页面，您应该会看到一个漂亮的登录按钮。如果你点击它，你会在一个弹出窗口中浏览你的谷歌登录流程，最后你会回到你的网站，按钮会显示“已登录”。

太好了，我们差不多到了！但以目前的形式，这还是没用。

## 3。识别用户

1.  更新 g-signin2 div 以包含`data-onsuccess`属性:

```
<div class="g-signin2" data-onsuccess="onSignIn"></div> 
```

Enter fullscreen mode Exit fullscreen mode

该属性包含用户成功登录 Google 后将调用的函数的名称。

1.  创建一个名为“onSignIn”的函数。

```
<body>
  ...
  <script>
    function onSignIn(googleUser) {
      // get user profile information
      console.log(googleUser.getBasicProfile())
    }
  </script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

您的`onSignIn`函数将被调用，并带有一个包含 Google 提供的信息的参数。如果你刷新页面，你会注意到

1.  您将自动登录
2.  按钮在刷新后很快更新(1 秒延迟)

如果您打开您的 javascript 控制台，您会看到用户信息被打印出来:

```
{
  Eea: "108716981921401766503"
  Paa: "https://lh3.googleusercontent.com/-y_ba58pC4us/AAAAAAAAAAI/AAAAAAAAAYE/wMGKOxlWR90/s96-c/photo.jpg"
  U3: "perez.dp@gmail.com"
  ig: "danny perez"
  ofa: "danny"
  wea: "perez"
} 
```

Enter fullscreen mode Exit fullscreen mode

这是包含一系列值的基本配置文件，这些值将我标识为用户。在任何正常情况下，这个对象应该有类似于`name`或`email`的字段，但是由于某种未知的原因(我希望我有答案，但是[他们也没有给出答案](https://github.com/google/google-api-javascript-client/issues/440))，它是乱码——但是至少它是一致的，没有改变。

你可以通过做`googleUser.getBasicProfile()['U3']`来直接获得数据，或者通过使用像`googleUser.getBasicProfile().getName()`或`googleUser.getBasicProfile().getEmail()`这样的函数来使用更容易理解的方法。([点击这里查看 javascript 客户端 api 参考文档](https://developers.google.com/identity/sign-in/web/reference#googleusergetid))

## 4。签名登记离开

通过在您的 index.html 中添加一个按钮和在您的 javascript 中添加一个点击处理程序，在用户登录后添加一个注销按钮。

index.html

```
<body>
...
  <button onclick="signOut()">Sign out</button>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

```
function signOut() {
  console.log('user signed out')
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！在这一点上，我们已经添加了一个登录谷歌按钮，我们可以通过名称/电子邮件来识别用户。还有，就是这样。这就是本库能帮你做的一切。

...但是像将用户数据保存到后端这样的基本事情呢？或者显示管理页面？！为什么文件里没有这个？！

这将在 7 月 22 日的下一篇文章中讨论——使用 js 库在没有 HTML 的情况下将其添加到您的站点中。

如果你觉得这篇文章有用，给它一个❤️，并关注我，以获得下一篇文章的通知。