# Next.js 中的重定向，最好的方法

> 原文：<https://dev.to/sergiodxa/redirects-in-next-js-the-best-way-jm>

当使用 Next.js 时，通常会需要将用户重定向到另一个页面，这可能是因为用户试图访问一个私人页面或者用户试图访问一个旧页面。

这可以通过多种方式实现，最流行的是使用 HTTP 重定向来实现，或者如果页面是从客户端访问的，则使用`Router.replace`。

虽然这样做并不理想，但是对于第一次呈现，用户需要向服务器发出一个额外的请求来获得正确的页面，但是结果是您已经知道了用户将要请求的页面，因为您设置了 HTTP 重定向，为什么不直接呈现正确的页面呢？

## 服务器端代码

这种设置重定向的方式涉及服务器端和客户端代码，让我们从服务器端代码开始。假设我们有两个页面，一个`/login`和一个`/private`页面，都在我们的 pages 目录中，如下所示。

```
[
  {
    "type": "folder",
    "name": "pages",
    "children": [
      { "type": "file", "name": "login.js" },
      { "type": "file", "name": "private.js" }
    ]
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

在我们的私人页面中，如果用户没有登录，我们希望呈现我们的登录页面，假设我们知道用户是否登录，因为它有一个特定的 cookie。我们可以在`getInitialProps`中验证登录状态。

```
PrivatePage.getInitialProps = async context => {
  const { sessions } = readCookies(context.req);
  if (!session) return { loggedIn: false };
  // the code required for your private page
}; 
```

Enter fullscreen mode Exit fullscreen mode

> *注意* : `readCookies`是一个伪函数，它应该从请求头中读取 cookies 并返回一个对象。

现在在我们的`PrivatePage`组件中，我们可以直接呈现登录。

```
import dynamic from "next/dynamic";
const LoginPage = dynamic(() => import("./login"));
// more imports here

function PrivatePage({ loggedIn, ...props }) {
  // some hooks here that need to be before the condition
  if (!loggedIn) return <LoginPage />;
  // the JSX the private page will render
}

// define getInitialProps here

export default PrivatePage; 
```

Enter fullscreen mode Exit fullscreen mode

这样，当用户访问`/private`并且没有`session` cookie 时，它将接收登录页面的 HTML。

> *注意*:我们使用动态导入来避免加载不需要使用的 LoginPage 代码。这将为用户节省几 kB。

## 客户端代码

让我们来看看重定向的客户端部分，用户访问了`/private`并收到了登录页面 HTML，这很好，但是用户仍然可以在浏览器中看到`/private`。让我们解决这个问题。

接下来是一个名为`next/router`的模块，它让我们以编程方式改变路线，我们可以使用它导航到另一个页面，而不需要用户点击链接。

让我们在`PrivatePage`中添加一个效果来改变 URL。

```
// more code here

function PrivatePage({ loggedIn, ...props }) {
  // some hooks here that need to be before the condition
  React.useEffect(() => {
    if (loggedIn) return; // do nothing if the user is logged in
    Router.replace("/private", "/login", { shallow: true });
  }, [loggedIn]);

  if (!loggedIn) return <LoginPage />;
  // the JSX the private page will render
}

// more code here 
```

Enter fullscreen mode Exit fullscreen mode

这个效果将完成这个任务，首先，验证用户是否登录不做任何事情，但是如果用户没有登录，它将使用`/`替换当前的 URL。

函数`Router.replace`接收`href`和`as`，前者是 Next(也就是页面)内部的实际路径，后者是我们想要显示给用户的路径。这两个让 use 告诉 Next.js 使用我们已经加载的`PrivatePage`，但是在浏览器中伪装成`/login`。

`shallow: true`告诉 Next 不要调用`getInitialProps`，结合其他两个将使 Next 只改变 URL 而不做任何其他事情，这意味着技术上用户仍然在呈现`LoginPage`的`PrivatePage`上。

当用户登录应用程序时，它将被重定向回`/private`，但这次没有`shallow: true`，这将导致`getInitialProps`再次被执行，因此它将看到这次它有`session` cookie，并将继续我们的`PrivatePage`的正常流程。

## 最终字

这是使用 Next 实现重定向的最佳方法，我是在该框架的创建者 [ZEIT](https://zeit.co) 工作时学到的，这也是我在那里实现一些重定向的方法。