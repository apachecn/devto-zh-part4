# Javascript 中 Elm 的远程数据类型

> 原文：<https://dev.to/rametta/elm-s-remote-data-type-in-javascript-1h24>

在 web 开发中，经常会出现这样一种循环模式:必须通过 rest api 从某个服务器获取一些数据，然后以某种方式在 UI 中显示出来。

这通常包括将这些数据存储在客户端的某个地方，要么存储在一个存储区中，要么存储在一个您可以引用的变量中，这就是远程数据类型可以提供帮助的地方。

通常在 JS:
中保存数据会像这样

```
// Javascript

const state = {
  data: null,
  error: null
}

fetch('/api/data')
  .then(res => res.json())
  .then(data => state.data = data)
  .catch(err => state.error = err) 
```

Enter fullscreen mode Exit fullscreen mode

并在屏幕上显示:

```
// React

const MyComp = ({ error, data }) => {
  if (error) {
    return <div className="error">{error}</div>
  } else {
    return <div>{data}</div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这种方法有几个问题:

1.  当数据从服务器加载时，我们在屏幕上显示什么？
2.  在向服务器请求数据之前，我们在屏幕上显示了什么？
3.  如果数据是成功的，我们是否正确地清除了先前的错误？
4.  如果我们得到一个错误，我们适当地清除以前的数据吗？

那么我们该如何解决这个问题呢？

有些人可能建议我们可以在状态中添加更多的字段来帮助表示所有的情况，就像这样:

```
// Javascript

const state = {
  data: null,
  error: null,
  loading: false,
  isAsked: false
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在 UI 中会类似于这样:

```
// React

const MyComp = ({ error, data, loading, isAsked }) => {
  if (!isAsked) {
    return <div>Nothing asked yet</div>
  }

  if (loading) {
    return <div>Loading...</div>
  }

  if (error) {
    return <div className="error">{error}</div>
  }

  if (data) {
    return <div>{data}</div>
  }

  return <div>Some default fallback to be safe</div>
} 
```

Enter fullscreen mode Exit fullscreen mode

但这样做的问题是，我们的 UI 很容易意外地显示错误的情况，因为在数据作为错误返回后忘记将 loading 设置为 false，或者在重试返回成功时忘记清除错误。

事实上，乐观地说，上面的结构可以有一个基数`2 x 2 x 2 x 2`,这是我们在任何给定时间可能处于的 16 种不同状态的组合。这代表了很多案例。

让我们看看 Elm 如何简化这个过程，只需要 4 个案例。

## 榆树中的远程数据

远程数据类型可以通过编写如下自定义类型来手动创建，或者通过使用库的预制版本来创建，如[krisajenkins/Remote Data](https://package.elm-lang.org/packages/krisajenkins/remotedata/latest/RemoteData):

```
-- Elm

type RemoteData e a
  = NotAsked
  | Loading
  | Error e
  | Success a 
```

Enter fullscreen mode Exit fullscreen mode

然后用这个类型作为你的模型:

```
-- Elm

type alias Model = RemoteData String MyData

-- and set the initial model value as NotAsked

init = NotAsked 
```

Enter fullscreen mode Exit fullscreen mode

我们需要明白的是，我们的状态在任何时候只能是这 4 种类型中的一种。我们不能同时处于加载状态和成功状态，否则我们的 UI 会显示两种不同的东西。这就是我们的基数现在只有 **4** 而不是 **16** 的原因，因为除此之外没有别的方法来表示我们的状态。

使用这个模型，我们现在可以为每种情况创建一个 UI，如下所示:

```
-- Elm

view model =
  case model of
    NotAsked -> div [] [ text "Not asked yet" ]

    Loading -> div [] [ text "Loading..." ]

    Error err -> div [] [ text err ]

    Success data -> div [] [ text <| "Here is my data: " ++ data ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们更新状态时，我们再也不需要担心清除以前的状态或忘记将一个字段翻转为空——因为在我们的模型中只有一个字段。

这是在 Elm 中处理远程数据获取的一个很好的模式，但是我们如何在 Javascript 中利用这个模式呢？达吉。

## Daggy

Javascript 中有一些不同的库可以帮助你用代数数据类型建模你的应用程序，就像我们在 Elm 中用`type`关键字一样。一个受欢迎的图书馆是 T2 图书馆。

为了用 Daggy 表示远程数据，我们将创建一个这样的类型:

```
// Javascript

import { taggedSum } from 'daggy'

const RemoteData = taggedSum('RemoteData', {
  NotAsked: [],
  Loading: [],
  Error: ['e'],
  Success: ['a']
}) 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了自己的类型，实现就和我们在 Elm 中的工作方式几乎一样了。

我们的州只有一个字段而不是 4 个，基数是 4 而不是 16。

```
// Javascript

const state = {
  data: RemoteData.NotAsked
}

// Fetch some data

state.data = RemoteData.Loading

fetch('/api/data')
  .then(res => res.json())
  .then(data => state.data = RemoteData.Success(data))
  .catch(err => state.data = RemoteData.Error(err)) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的用户界面中，比如 React，我们会有:

```
// React

const MyComp = ({ data}) => data.cata({
  NotAsked: () => <div>Not asked yet</div>,
  Loading: () => <div>Loading...</div>,
  Error: err => <div>{err}</div>,
  Success: d => <div>Here is my data: {d}</div>
}) 
```

Enter fullscreen mode Exit fullscreen mode

使用这种模式实际上有助于将许多呈现逻辑从 React 中的组件中移出，比如用`if (field) {...}`检查字段，而不是将这种责任转移到 reducer 之类的东西上，这将使运行单元测试更加容易。

如果您想了解 Javascript 中代数数据类型的更多信息，请查看以下链接:

*   [具有总和类型的更好的 JS](https://medium.com/fullstack-academy/better-js-cases-with-sum-types-92876e48fd9f)
*   [Elm 如何消除 UI 反模式](http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html)
*   [Daggy](https://www.npmjs.com/package/daggy)
*   练习

如果你喜欢这篇文章，一定要[关注我](https://dev.to/rametta)！