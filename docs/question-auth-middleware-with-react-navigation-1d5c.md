# 具有反应式导航的认证中间件

> 原文：<https://dev.to/themisir/question-auth-middleware-with-react-navigation-1d5c>

我想创建一个认证中间件来检查用户是否被授权，如果没有，继续页面，然后显示登录屏幕。

我有一个底部标签导航器。我想将这个中间件应用到导航器上我的 Profile 选项卡中。

所以我试着用钩子来做，但是没用。

```
export const authMiddleware = (PageComponent, pageName, backTo) => (props) => {
    var auth = useContext(AuthContext);
    if (auth.token) {
        return <PageComponent {...props} />
    } else {
        useEffect(() => {
            props.navigation.navigate('SignIn', { redirect: { to: pageName }, back: { to: backTo } })
        })
        return <View />
    }
} 
```

页面中的登录只出现过一次(在第一次渲染之后)。

你有什么办法让它发挥作用？