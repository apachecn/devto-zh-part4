# react 路由器中的重定向路由

> 原文：<https://dev.to/paramharrison/redirect-routes-in-react-router-4f0n>

重定向主要用于检查用户是否有权访问该页面，如果没有，则重定向到相关页面。

在 react 应用程序中使用 react 路由器组件很容易做到这一点。

让我们看一个重定向组件如何在 react 路由器中工作的基本示例。让我们构建一个简单的页面，根据条件重定向到不同的页面。

我们将扩展我们的路由器示例，

*   演示`/old-route`到`/new-route`的简单页面

#### 简单重定向示例

将重定向路由添加到 react 路由器示例中，

在这个例子中。我们将创建两个 URL

*   `/old-route`是旧的 URL，将使用 react 路由器的`Redirect`组件重定向到新的 URL `/new-route`。

```
// Import Redirect component from react-router
import { Route, Redirect } from 'react-router';

// Pass from URL and to URL for the Redirect component
<Redirect from="/old-route" to="/new-route" />
// Handle the to Route and load new page
<Route exact path="/new-route" component={RedirectPage} />

// Add link to the old route and see the page displays the new url onClick
<Link to="/new-route">Redirecting to New page</Link>

// Page component
const RedirectPage = () => {
  return (
    <h3>Redirect Page</h3>
  );
}; 
```

现在，在这里查看简单重定向路由的示例

[https://codesandbox.io/embed/redirect-route-simple-yewt5](https://codesandbox.io/embed/redirect-route-simple-yewt5)

您可以看到链接中的旧网址被重定向到新网址，新网址显示在浏览器中。

### 基于 props 的复杂重定向示例

为此，我们将构建一个小页面，

*   如果属性`isLoggedin`为真或
*   如果属性`isLoggedin`为假，则显示一条简单的消息“未登录”

让我们先构建页面，

```
const AuthPage = ({ isLoggedIn }) => {
  if (isLoggedIn) {
    return <Redirect to="/dashboard" />;
  } else {
    return <h3>User not loggedin</h3>;
  }
};

const DashboardPage = () => {
  return <h3>Dashboard Page</h3>;
}; 
```

让我们为`Auth`页和`Dashboard`页、
定义路线

```
// Route definition for Dashboard
<Route exact path="/dashboard" component={DashboardPage} />
// Route definition for not loggedin page by passing IsLoggedIn props as false
<Route exact path="/auth-not-loggedin" render={(props) => <AuthPage {...props} isLoggedIn={false} />} />
// Route definition for loggedin page by passing IsLoggedIn props as true. This route will automatically redirect to dashboard because of the condition
<Route exact path="/auth-loggedin" render={(props) => <AuthPage {...props} isLoggedIn={true} />} />

// Lets add the links to navigate to these pages
<Link to="/auth-not-loggedin">Not Loggedin</Link>
<Link to="/auth-loggedin">User Loggedin</Link> 
```

你好，你现在可以检查了。当我们将`isLoggedIn`属性作为`true`传递时，路由重定向并将浏览器 URL 更改为 dashboard。如果没有设置 props，则显示一条简单的未登录消息。

您可以在这里查看功能，

[https://codesandbox.io/embed/redirect-route-complex-x6o6v](https://codesandbox.io/embed/redirect-route-complex-x6o6v)

就是这样，伙计们，希望这有助于您了解 react 路由器中重定向的工作原理。react 系列的更多内容，敬请期待🤗

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/e354527b3c43917112e36101fbd4f5f66924252e)