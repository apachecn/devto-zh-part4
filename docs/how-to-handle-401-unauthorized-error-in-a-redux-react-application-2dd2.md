# 如何处理 Redux React 应用程序中的 401 未授权错误

> 原文：<https://dev.to/jetrockets/how-to-handle-401-unauthorized-error-in-a-redux-react-application-2dd2>

为了响应客户端请求，服务器可能会返回 401 未授权错误。您必须正确地捕捉它，例如，清除状态并重定向到授权页面。为了解决这个问题，我们编写了一个定制的中间件，在我们看来，这是最好的解决方案。

```
import * as actions from 'actions';

const authInterceptor = ({ dispatch }) => (next) => (action) => {
  if (action.status === 401) {
    dispatch(actions.removeJwt());
  } else {
    next(action);
  }
}; 
```