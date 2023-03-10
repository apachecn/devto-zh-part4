# 如何处理 react 和 axios 中常见的错误页面？

> 原文：<https://dev.to/nimahkh/how-to-handle-common-error-pages-in-react-and-axios-2fe8>

在我们的项目中，我们遇到了一个问题，即如何使用 axios 控制常见的错误页面，并显示适当的错误页面，如 404 或维护模式页面。

在我们的用户流设计中，当响应状态代码为 503 等时，react 必须向用户显示维护页面或其他一些错误页面。和我的朋友搜索了一下，我们发现 axios 有一个拦截器。

> 您可以在请求或响应被 then 或 catch 处理之前拦截它们。[轴](https://github.com/axios/axios#interceptors)

但是在我们的项目中，我们有一个问题，我们有 40 个请求，编辑所有的服务来处理错误和改变路由器不是很合理。

所以我用 react HOC 技术来处理这个问题，所以我的解决方案是这样的:

```
import React, {useEffect} from "react";
import axios from "axios";

const checkRequests= Wrapped => {
    function CheckRequests(props) {
        useEffect(()=>{
            axios.interceptors.response.use(function (response) {
                // Do something with response data
                return response;
            }, function (error) {
                switch (error.response.status) {
                    case 503 :
                        props.history.push('/503') //we will redirect user into 503 page 
                        break
                    default :
                        break
                }
                // Do something with response error
                return Promise.reject(error);
            });
        })

        return (
            <Wrapped {...props} />
        )
    }
    return CheckRequests
}

export default checkRequests 
```

Enter fullscreen mode Exit fullscreen mode

然后我在我的 app 里用 js:

```
 import React from "react"
import CheckRequests from "./HOC/CheckRequests"

function App(props){ ... }

export default checkRequests(App) 
```

Enter fullscreen mode Exit fullscreen mode

因此，在 axios 调用的每个请求中，错误处理都会起作用。

这是我的解决方案。我喜欢用你的。