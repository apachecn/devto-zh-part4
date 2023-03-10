# 学习原因逐步反应第 5 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-5-m6l>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

我们的[可重用表单组件](https://github.com/sophiabrandt/reason-form/blob/4d9e5093f43c047d4c807adff1849f5e1281cbba/src/Form.re)可以工作，但是我们没有任何路由。我们如何导航到我们的两个表单(注册和登录)？

## 输入原因反应路由器

设置内置**[react 路由器](https://reasonml.github.io/reason-react/docs/en/router-2#docsNav)** 轻而易举。我对 [React 路由器](https://reacttraining.com/react-router/)世界的探索要痛苦得多。

```
/* src/App.re */

[@react.component]
let make = () => {
  let url = ReasonReactRouter.useUrl();
  switch (url.path) {
  | ["login"] => <Form formType="login"/>
  | ["register"] => <Form formType="register"/>
  | [] => <Main />
  | _ => <NotFoundPage />
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对，就这些了(为了发展)。

为路由器 API 添加一个`let`绑定，然后在不同的路径上进行模式匹配。

默认路由匹配一个空数组。所有我们没有明确处理的东西，我们会在下划线上匹配，然后发送到 404 页面。

我们的表格怎么办？

在`/login`我们路由到表单组件，并将`formType`道具作为`"login"`转发。对于`/register`，我们添加了`"register" formType`。

(我还在想怎么设置一个基本 url。比如发布到 GitHub 页面，就要告诉路由器公共 url。)

## 添加一些页面

让我们快速添加根路由和 404 页的占位符页面。

```
/* src/Main.re */

let str = ReasonReact.string;

[@react.component]
let make = () =>
  <div className="section is-fullheight">
    <div className="container">
      <div className="column is-4 is-offset-4">
        <h1 className="is-size-1"> {"Main Page" |> str} </h1>
        <hr />
        <a href="/login" className="is-size-4"> {"Login" |> str} </a>
        <p className="is-size-5"> {"or" |> str} </p>
        <a href="/register" className="is-size-4"> {"Register" |> str} </a>
      </div>
    </div>
  </div>; 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么新鲜的。

```
/* src/NotFound.re */

let str = ReasonReact.string;

[@react.component]
let make = () =>
  <div className="section is-fullheight">
    <div className="container">
      <div className="column is-4 is-offset-4">
        <h1 className="is-size-1"> {"404" |> str} </h1>
        <h2 className="is-size-2"> {"Not Found" |> str} </h2>
        <hr />
        <a href="/login" className="is-size-4"> {"Login" |> str} </a>
        <p className="is-size-5"> {"or" |> str} </p>
        <a href="/register" className="is-size-4"> {"Register" |> str} </a>
        <hr />
        <a href="/" className="is-size-4">
          {{j|⬅️ Back to Homepage|j} |> str} // (A)
        </a>
      </div>
    </div>
  </div>; 
```

Enter fullscreen mode Exit fullscreen mode

同样，将每个 HTML 标记内容转换成 ReasonReact 字符串有点麻烦。

我们还必须小心 [Unicode](https://bucklescript.github.io/docs/en/interop-cheatsheet.html#string-unicode-interpolation) (参见`line A`)。

## 思想

添加基于浏览器的路由非常简单。API 非常直观。设置路由器不需要任何仪式。

[路由器也应该非常快，因为它是用 C++编译成跳转表的。](https://reasonml.github.io/reason-react/docs/en/router-2#docsNav)

我还不知道如何使用基本 URL。看起来您可以向`ReasonRouter.useUrl`函数传递一个 url。

我想为开发设置一个公共 url，并在 [ReasonML Chat](https://reasonml.chat/t/reading-env-variables-with-reason-and-or-reason-scripts/) 上找到了一个关于环境变量的讨论。这是我以后要调查的事情。

### 进一步阅读

*   [原因反应路由器](https://reasonml.github.io/reason-react/docs/en/router-2#docsNav)