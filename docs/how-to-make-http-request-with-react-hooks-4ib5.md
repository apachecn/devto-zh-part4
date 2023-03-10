# 如何用 React 钩子做 http 请求？

> 原文：<https://dev.to/h2rmone/how-to-make-http-request-with-react-hooks-4ib5>

这是我在 [dev.to](//dev.to) 的第一个帖子。我将介绍一个定制的 React 钩子来帮助生成 http 请求- [`use-request`](https://github.com/react-rekindle/use-request)

我见过很多用 React Hooks 编写的请求库，但没有一个是我喜欢的。他们中的大多数将客户端放在其中，这导致灵活性降低。切换到 [`use-request`](https://github.com/react-rekindle/use-request) 时，用`axios`还是`fetch`由你决定。将封装客户端和 api 请求的函数传递给`useRequest`函数，就可以得到请求和回调函数的状态。有了 typescript 支持，请求回调函数的输入参数也可以根据 api 函数的类型定义准确显示。让我们来看看它是如何工作的！

## 特征

*   👕Typescript 支持。
*   没有 gzip 缩小后的🗜️ 1.3kb。
*   📤易于使用不同的请求客户端。

## 安装

```
yarn add @rekindle/use-request 
```

## 用法

```
import useRequest from '@rekindle/use-request'
import getFooApi from '@/utils/axios'

function App() {
  const [{ loading, error, data }, fetch] = useRequest(getFooApi)

  function handleClick() {
    fetch()
  }

  useEffect(() => {
    fetch()
  }, [fetch])

  if (loading) return <div>loading...</div>
  if (error) return <div>error</div>

  return (
    <div>
      <p>{data && data.text}</p>
      <button onClick={handleClick}>refetch</button>
    </div>
  )
} 
```

## Api

```
type useRequest = (api, initialState) => [state, memoizedRequestCallback] 
```

注意:为什么要移动请求回调？

参考:[从依赖列表中省略函数安全吗？](https://reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)

如果你想深入了解使用效果和依赖性，这里:[https://overreacted.io/a-complete-guide-to-useeffect/](https://overreacted.io/a-complete-guide-to-useeffect/)