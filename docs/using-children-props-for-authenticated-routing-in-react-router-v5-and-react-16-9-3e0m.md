# 在 React 路由器 v5 和 React 16.9 中使用子 Props 进行认证路由

> 原文：<https://dev.to/gloriamaris/using-children-props-for-authenticated-routing-in-react-router-v5-and-react-16-9-3e0m>

所有 SPA 开发人员都面临的一个普遍问题是，只允许经过身份验证的用户访问某些页面。

比如登陆页面= *好的*。仪表板里面的页面=**。**

 **我的解决方案是利用 React 的特殊`children`道具，围绕我想要控制访问的路径构建一个包装器组件。

#### 什么是`children`道具？

本质上，组件的开始和结束标签中的所有内容都是该组件的*子元素*。这通常有助于构建布局组件，(如果您使用该设计模式，[，您应该](https://reactpatterns.com/#layout-component))。

#### Routes.js

以一种非常 v3 的方式(呵呵)，我有一个`Routes`文件，其中包含应用程序中所有可用路线的列表。

```
import App from './components/App'
import Login from './components/Login'
import Messages from './components/Messages'
import Settings from './components/Settings'
import { routes } from './routes-list' // just a list of paths

export default Routes = () => {
    return (
        <Router>
            <Switch>
                <Route exact path="/" component={App}/>
                <Route exact path={routes.login} component={Login}/> 
               // Private routes
                <Route exact path={routes.messages} component={Messages}/>
                <Route exact path={routes.settings} component={Settings}/>
            </Switch>
        </Router>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 新组件:`PrivateRoute`

`PrivateRoute`充当所有需要认证的路由的包装组件。身份验证方法高度特定于应用程序，因此您可能希望使用或创建自己的方法，但本质上，它应该检查用户是否被允许访问这些私有路由。否则，app 重定向到`/login`页面。

```
import React, { Fragment } from 'react'
import { Redirect } from 'react-router-dom'
import { isUserAuthenticated } from './utils/auth'

export default PrivateRoute = (props) => (
    <Fragment>
        { isUserAuthenticated() ? props.children : <Redirect to={routes.login} /> }
    </Fragment> ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 实现

在`Routes.js`里面，包装好你需要私有的路线。

```
// ...all the imports
import PrivateRoute from './components/PrivateRoute'

export default Routes = () => {
    return (
        <Router>
            <Switch>
                <Route exact path="/" component={App}/>
                <Route exact path={routes.login} component={Login}/> 
               // Private routes
                <PrivateRoute>
                    <Route exact path={routes.messages} component={Messages}/>
                    <Route exact path={routes.settings} component={Settings}/>
                </PrivateRoute>
            </Switch>
        </Router>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样！

如果您有兴趣了解 React 中认证路由的其他实现(相信我，有很多)，我在这里整理了一个列表作为指南:

*   [私有路由、公共路由和带 React 路由器的受限路由](https://medium.com/@thanhbinh.tran93/private-route-public-route-and-restricted-route-with-react-router-d50b27c15f5e)

*   [使用高阶组件进行认证路由](https://www.codementor.io/sahilmittal/using-higher-order-components-for-authenticated-routing-i1hcp6pc6)

*   [检查认证的高阶组件](https://riptutorial.com/reactjs/example/30238/higher-order-component-that-checks-for-authentication)

*   [受保护的路由和 React 路由器 v4 的认证](https://tylermcginnis.com/react-router-protected-routes-authentication/)

一如既往，编码快乐！**