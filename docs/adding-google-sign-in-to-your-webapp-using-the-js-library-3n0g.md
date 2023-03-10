# 将 google 登录添加到您的 webapp 中——使用 js 库

> 原文：<https://dev.to/intricatecloud/adding-google-sign-in-to-your-webapp-using-the-js-library-3n0g>

在本系列的第一部分中，我们决定使用 [Google 登录网站库](https://developers.google.com/identity/sign-in/web/)来显示一些使用 javascript 的用户的信息。我们使用了默认的 Google 登录工作流和他们的默认按钮。

在这一部分，我们将介绍如何使用`gapi`库来配置登录，然后实际登录用户，以及处理一些常见用户场景的一些片段。

## 创建自己的签到按钮

1.  加载 api.js 库，而不是 platform.js🤷(不确定它们为什么不同)

将`https://apis.google.com/js/platform.js`改为`https://apis.google.com/js/api.js?onload=onLibraryLoaded`

在这里，一旦使用您可以通过 URL 提供的`?onload=onLibraryLoaded`回调加载了库，我们就配置我们的登录客户端。`api.js`将添加一个名为`gapi`的全局变量。

1.  用按钮单击处理程序向 index.html 添加一个按钮

`<button onclick="onSignInClicked()">Sign in with button onClick</button>`

1.  将以下代码添加到 index.html 的脚本标记中，以处理按钮单击

```
function onLibraryLoaded() {
    gapi.load('auth2', function() {
        gapi.auth2.init({
            client_id: 'YOUR_CLIENT_ID.apps.googleusercontent.com',
            scope: 'profile'
        })
    })
}

function onSignInClicked() {
    gapi.load('auth2', function() {
        gapi.auth2.signIn().then(function(googleUser) {
          console.log('user signed in')
        }, function(error) {
            console.log('user failed to sign in')
        })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以从开发人员控制台访问`gapi.auth2`库，并使用我们的`client_id`初始化它。

## 如何处理一些常见的用户场景

*   当用户登录时监听
*   检查用户是否登录
*   获取用户信息
*   仅允许特定域的用户登录
*   仅允许特定用户登录
*   在用户登录之前隐藏内容
*   将您的 ID 令牌发送到后端(如果您有)

### 用户登录时监听

在上面的例子中，我们只能在初始化并登录用户后运行一些代码。但是，如果页面的不同部分在不同的文件中，每个部分根据用户显示不同的内容，该怎么办呢？(如果您使用组件，可能就是这种情况)

```
class UserAvatarComponent extends React.Component {
    ...
    componentDidMount() {
        gapi.load('auth2', function() {
            gapi.auth2.isSignedIn.listen(function(isSignedIn) {
                console.log('user signed in ', isSignedIn)
                this.setState({status: isSignedIn})
            })
        })
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

### 检查用户是否登录

```
function isUserSignedIn() {
  gapi.load('auth2', function() {
      var isSignedIn = auth2.isSignedIn.get();
      console.log('is signed in? ', isSigned In)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

使用该函数需要注意一些事项:

*   默认情况下，如果您已经登录，登录库会自动为您登录。
*   如果你刷新页面，甚至在用户登录之后，那么在第一次加载时，你会得到`auth2.isSignedIn.get() === false`
*   用户自动登录后(通常需要一秒钟)，然后`auth2.isSignedIn.get() === true`
*   取决于您如何处理您的登录用户界面，您的用户可能会看到他们没有登录一个热点秒。如果您想知道发生这种情况的准确时间，使用`isSignedIn.listen()`回调会很有帮助。

### 获取用户信息

```
function showCurrentUserInfo() {
  gapi.load('auth2', function() {
      var googleUser = auth2.currentUser.get()
      console.log('users info ', googleUser)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

### 只允许特定域的用户登录

这是一个小技巧，可能很容易绕过，但是您可以使用`getHostedDomain()`方法获得用户来自的 G-Suite 域。如果用户没有 G-Suite 域，那么它将是空白的。

```
function onSignIn(googleUser) {
    if(googleUser.getHostedDomain() !== 'mysite.com') {
        // show a Not Authorized message
    } else {
        // show the users dashboard
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 只允许某些用户登录

这更像是一种黑客行为，但似乎是唯一可以从 javascript 内部完成的方法。你真的不应该。不知道为什么我要把它包括进来。野蛮的方法。

```
function onSignIn(googleUser) {
    var profile = googleUser.getBasicProfile()
    if(profile.getEmail() === 'admin@example.com' ||
       profile.getEmail() === 'client@example.com') {
           // show the user dashboard
    } else {
        // show a Not Authorized message
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 隐藏内容直到用户登录后

这也是黑。如果您在浏览器中摆弄 CSS，这很容易解决，但是如果它适合您的用例，也可以工作。这是一个坏主意的原因是，在一个静态网站中，用户可以获得 HTML 中的所有信息。如果您要隐藏实际的敏感信息，请不要使用此选项。这是一个很好的候选人展示你最喜欢的猫图片。

```
<body>
...
  <div id="greeting">
    You are not authorized to view this content
  </div>
  <div id="dashboard">
    ...
  </div>
  <script>
    // hide initially
    $('#dashboard').hide()

    function onSignIn(googleUser) {
      setTimeout(function() {
        // show the content
        $('#greeting').hide()
        $('#dashboard').show()
      }, 1000);
    }
  </script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 发送令牌来识别用户(不是他们的 ID)

如果您正在进行后端请求，您将希望将用户 ID 令牌作为授权头发送到您的后端。在您的后端，您将能够验证和解码 ID 令牌([参见这里的示例](https://developers.google.com/identity/sign-in/web/backend-auth))。

这种方法的好处是这个 ID 令牌(一种 JWT 令牌)是由 Google 签名的，所以您知道信息没有被篡改。

```
$.ajax({
  url: 'myapi/example',
  headers: {'Authorization': googleUser.getAuthResponse().id_token)},
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这篇文章中，我们看到了如何通过 javascript 配置 Google 登录库，并使用它来做一些事情，如获取用户信息并检查他们是否登录(登录流程中需要注意一些细微差别)。

演示代码可在[Github intricatecloud/Google-sign-in-demo](https://github.com/intricatecloud/google-sign-in-demo/blob/master/gapi-demo.html)上获得。将`YOUR_CLIENT_ID`替换为您的客户端 ID，您将能够看到运行中的登录按钮。

在本系列的最后一部分，我们将看看如何在 React 和 Angular 应用程序中使用 google sign-in 的一些例子。[点击这里查看](https://www.intricatecloud.io/2019/08/adding-google-sign-in-to-your-webapp-a-react-example/)。

* * *

最初发布于 [www.intricatecloud.io](https://www.intricatecloud.io/2019/07/adding-google-sign-in-to-your-webapp-using-the-js-library/)