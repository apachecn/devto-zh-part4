# 将 google sign-in 添加到您的 web app——react 示例

> 原文：<https://dev.to/intricatecloud/adding-google-sign-in-to-your-webapp-a-react-example-152k>

在本系列的下一部分中，我将用一个简单的 react 应用程序和一个额外的 react-router 示例带您完成 google 登录的实现。

到目前为止，我们已经看到了两个不同的 hello world 示例，展示了如何在前端添加 google sign-in 使用普通的 HTML 和普通的 JS。对于 hello world 来说，这一切都很好，但是在我弄清楚 google sign-in 时，有一件事一直在丢失，那就是一个工作的实现是什么样子的——尤其是在 React 中。

*有一个 [react-google-login 组件](https://github.com/anthonyjgrove/react-google-login)，它在一个`<GoogleLogin>`标签后面配置所有的 google 登录。它非常有用，我已经在几个实例中使用过——我的一个抱怨是你不能得到`gapi.auth2.init()`方法的返回值。如果你不喜欢使用图书馆，这篇文章将会告诉你幕后发生了什么。

## 使用 google 登录创建新的 react 应用程序

首先-创建应用程序`create-react-app google-auth-demo`。我们将主要使用的文件是 App.js 和 index.html。

将 google 登录脚本标签添加到您的`public/index.html`

```
<head>
  ...
  <script src="https://apis.google.com/js/api.js" async defer></script>
  ...
</head> 
```

Enter fullscreen mode Exit fullscreen mode

## 添加登录按钮

在 App.js 中——添加一些状态来跟踪用户何时登录

```
contructor(props) {
    super(props)
    this.state = {
        isSignedIn: false,
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将按钮添加到组件

```
render() {
  return (
    <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />

          <p>You are not signed in. Click here to sign in.</p>
          <button id="loginButton">Login with Google</button>
        </header>
      </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

等等，如果用户已经登录，如何避免显示此信息？我们可以使用状态来有条件地显示它。

```
getContent() {
  if (this.state.isSignedIn) {
    return <p>hello user, you're signed in </p>
  } else {
    return (
      <div>
        <p>You are not signed in. Click here to sign in.</p>
        <button id="loginButton">Login with Google</button>
      </div>
    )
  }

}

render() {
  return (      
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <h2>Sample App.</h2>

        {this.getContent()}           
      </header>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

*   因为使用内联 JSX 编写条件语句有点困难，所以我将条件块移至另一个方法，以提供我们想要的组件。

此时，您将有一个什么也不做的按钮(最好的按钮类型),您将看到“您没有登录”消息

## 添加签到

要完成设置 google 登录，您需要使用`gapi.auth2.init()`来初始化这个库。这样做的一个好地方是在`componentDidMount()`回调内部。

```
componentDidMount() {
  window.gapi.load('auth2', () => {
    this.auth2 = gapi.auth2.init({
      client_id: '260896681708-o8bddcaipuisksuvb5u805vokq0fg2hc.apps.googleusercontent.com',
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用默认样式，请在初始化组件时使用`gapi.signin2.render`方法。

```
onSuccess() {
  this.setState({
    isSignedIn: true
  })
}

componentDidMount() {
  window.gapi.load('auth2', () => {
    this.auth2 = gapi.auth2.init({
      client_id: 'YOUR_CLIENT_ID.apps.googleusercontent.com',
    })

    window.gapi.load('signin2', function() {
      // render a sign in button
      // using this method will show Signed In if the user is already signed in
      var opts = {
        width: 200,
        height: 50,
        onSuccess: this.onSuccess.bind(this),
      }
      gapi.signin2.render('loginButton', opts)
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

当使用这种方法时，按钮会自动显示你是否登录，但是`onSuccess`回调实际上不会运行，除非当它显示“登录”时用户点击它。否则，您会自动登录。一种挂钩到自动登录过程末尾的方法是给由`gapi.auth2.init` :
返回的承诺添加一个回调

```
window.gapi.load('auth2', () => {
  this.auth2 = gapi.auth2.init({
    client_id: 'YOUR_CLIENT_ID.apps.googleusercontent.com',
  })

  this.auth2.then(() => {
    this.setState({
      isSignedIn: this.auth2.isSignedIn.get(),
    });
  });
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 制定“受保护”的路线

如果你正在使用 react-router，并且你想给你的 react 应用添加一个“受保护的”路由，你可以劫持一个`<Route>`的`render`道具。你可以这样做:

```
authCheck(props, Component) {
  return this.auth2.isSignedIn.get() ? <Component {...props} /> : <UnauthorizedPage/>

}

render() {
  ...
  <Route path="/home" render={this.authCheck.bind(this, HomePage)}/>
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过挂钩到`<Route>`上的 render 属性，您可以动态定义当您尝试访问该路由时将加载什么组件。

这是由 [react-private-route 项目](https://www.npmjs.com/package/react-private-route)库采用的策略，以使它更容易编写，绝对值得一试。

# 结论

如果你正在 React 应用程序中实现谷歌登录，请查看[my github repo intri cate cloud/Google-sign-in-demo](https://github.com/intricatecloud/google-sign-in-demo/tree/master/react/google-auth-demo)以查看上面工作设置中的所有代码。

在这个由 3 部分组成的系列文章中，我们讲述了从 google 登录的 hello-world 示例到使用 javascript 库做一些不太好的事情的[。现在，我们已经审查了您需要与 Google 登录按钮集成的所有代码。](https://www.intricatecloud.io/2019/07/adding-google-sign-in-to-your-webapp-using-the-js-library/)

有时，像这样的教程可能很难跟上，除非你看到它，否则它不会点击。我把这个系列作为一个现场编码演练，在这里你可以看到我在教程中犯的所有错误。[在这里注册我的邮件列表](https://www.intricatecloud.io/intricate-cloud-newsletter-signup/)以便在它上线时得到通知。