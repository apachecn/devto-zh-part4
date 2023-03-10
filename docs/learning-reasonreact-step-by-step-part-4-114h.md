# 学习原因循序渐进第 4 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-4-114h>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

到目前为止，我们已经创建了带有`useReducer`钩子的简单表单组件[。](https://github.com/sophiabrandt/reason-form/blob/e51d7cde835ab56eec4cd98564a59d8dc77e0811/src/Form.re)

这个表单使用电子邮件和密码——它可以是一个登录表单。

但是如果我们也想使用相同的逻辑来创建一个注册表单呢？

## 使用自定义挂钩使表单组件可重用

**我们知道数据的形状**:我们有三个表单字段:电子邮件、密码和用户名。我们将只在注册页面上显示用户名字段。

**但是我们必须为我们所有的状态** :
建立一个[记录](https://reasonml.github.io/docs/en/record)

```
/* src/Form.re */
type state = {
  username: string, // *new
  email: string,
  password: string,
}; 
```

Enter fullscreen mode Exit fullscreen mode

**让我们将`useReducer`钩子提取到一个单独的函数**中，并调整动作。一、我们表单的初始状态、动作类型和减速器功能:

```
/* src/Form.re */
let initialState = {username: "", email: "", password: ""};

type action =
  | SetUsername(string)
  | SetEmail(string)
  | SetPassword(string) // *new
  | ResetState;         // *new

let reducer = (state, action) =>
  switch (action) {
  | SetUsername(username) => {...state, username}
  | SetEmail(email) => {...state, email}
  | SetPassword(password) => {...state, password} // *new
  | ResetState => initialState                   // *new
  }; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的[最后一次尝试](https://www.rockyourcode.com/learning-reason-react-step-by-step-part-3)中，我们在组件内部使用了`useReducer`，并且还在组件的 JSX 内部连接了调度函数。

```
/* src/Form.re */
[@react.component]
let make = () => {
  let initialState = {email: "", password: ""};

  let (state, dispatch) = React.useReducer(reducer,initialState);

  // ...

    <input
        className="input"
        type_="email"
        name="email"
        value={state.email}
        required=true
        onChange={evt => valueFromEvent(evt)->SetEmail |> dispatch}
      />

 // ... 
```

Enter fullscreen mode Exit fullscreen mode

相反，我想**创建一个自定义钩子来处理表单动作和处理状态**。

```
let useForm = (~callback) => { // (A)
  let valueFromEvent = evt: string => evt->ReactEvent.Form.target##value;
  let nameFromEvent = evt: string => evt->ReactEvent.Form.target##name;

  let (state, dispatch) = React.useReducer(reducer, initialState);

  let handleChange = evt => {
    ReactEvent.Form.persist(evt);
    switch (nameFromEvent(evt)) {
    | "username" => valueFromEvent(evt)->SetUsername |> dispatch
    | "email" => valueFromEvent(evt)->SetEmail |> dispatch
    | "password" => valueFromEvent(evt)->SetPassword |> dispatch
    | _ => ()   // (B)
    };
  };

  let handleSubmit = evt => {
    ReactEvent.Form.preventDefault(evt);
    callback();            // (A)
    dispatch(ResetState);  // (C)
  };

  (state, handleChange, handleSubmit); // (D)
}; 
```

Enter fullscreen mode Exit fullscreen mode

定制钩子接受一个回调函数(`A`)，我们将在提交表单时使用这个函数。现在不同的形式可以添加不同的逻辑！

`handleChange`函数反映了我们之前的情况。我们在每个动作上使用。所有操作都处理表单的状态:更新或重置表单。

这些`nameFromEvent`和`valueFromEvent`是什么东西？

我们必须以某种方式与 DOM 交互——在 JavaScript 中，应该是`evt.target.value`和`evt.target.name`。

例如，如果目标名称是“password”，那么用我们从 HTML 表单中获得的值更新密码状态。

但是等等！动作`variant`也有重置表单的选项。我们不想在`handleChange`处理这个案子。相反，当我们提交表单时，我们会发送它(见在线`C` : `ResetState`)。

**我们在`handleChange`中的模式匹配并不详尽。**我们不处理所有可能的情况。这就是为什么我们必须在 a 行中设置一个“包罗万象”的 case。下划线匹配所有内容。我们不想返回任何东西，所以我们返回`Unit`类型(一种表示“无值”的类型)——也称为空括号(参见`line B`)。

**最后，我们必须返回`state`、`handleChange`、`handleSubmit`、T5(`D`)，这样我们就可以在我们的表单组件中使用它作为自定义钩子。**

## 在表单组件中使用自定义钩子

现在，让我们利用 React 组件中的自定义钩子:

```
/* src/Form.re */
[@react.component]
let make = (~formType) => {
  let logger = () => Js.log("Form submitted");

  let (state, handleChange, handleSubmit) = useForm(~callback=logger);

  //... 
```

Enter fullscreen mode Exit fullscreen mode

`logger`函数是我们对`useForm`的回调。然后我们从`useForm`中去结构`state`、`handleChange`和`handleSubmit`。

我们的组件将采用一个名为`formType`的道具。`formType`会告诉我们这是注册页面还是登录页面。

例如，在`src/App.re`中，它看起来像这样:

```
[@react.component]
let make = () => <Form formType="login"/>; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们必须将逻辑添加到 JSX:

```
// ...

<div className="section is-fullheight">
    <div className="container">
      <div className="column is-4 is-offset-4">
        <h1 className="is-size-1 has-text-centered is-capitalized">
          {formType |> str}   // (A)
        </h1>
        <br />
        <div className="box">
          <form onSubmit=handleSubmit>      // (B)
            {
              formType === "register" ?     // (C)
                <div className="field">
                  <label className="label"> {"Username" |> str} </label>
                  <div className="control">
                    <input
                      className="input"
                      type_="text"
                      name="username"
                      value={state.username}
                      required=true
                      onChange=handleChange  // (D)
                    />
                  </div>
                </div> :
                ReasonReact.null
            }
            <div className="field">
              <label className="label"> {"Email Address" |> str} </label>
              <div className="control">
                <input
                  className="input"
                  type_="email"
                  name="email"
                  value={state.email}
                  required=true
                  onChange=handleChange   // (D)
                />
              </div>
            </div>
            <div className="field">
              <label className="label"> {"Password" |> str} </label>
              <div className="control">
                <input
                  className="input"
                  type_="password"
                  name="password"
                  value={state.password}
                  required=true
                  onChange=handleChange // (D)
                />
              </div>
            </div>
            <button
              type_="submit"
              className="button is-block is-info is-fullwidth is-uppercase">
              {formType |> str} // (A)
              <br />
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>; 
```

Enter fullscreen mode Exit fullscreen mode

在 A 行，我们可以看到表单将根据`formType`属性显示一个标题或一个按钮文本。

第 B 行显示了我们如何使用自定义函数`handleSubmit`提交表单。

C 行显示了如果我们的表单是注册表单(`formType`是我们从 main `App.re`获得的道具)，我们如何有条件地显示用户名字段。

当我们不想渲染田地的时候，就得通过`ReasonReact.null`。

在 JavaScript 中，你可以像这样做一个布尔渲染:

```
(formType === "register" && (<JSX here>) 
```

Enter fullscreen mode Exit fullscreen mode

这是不鼓励的。你必须明确如果你不满足条件会发生什么。

第 D 行显示我们必须将`handleChange`函数传递给每个`onChange`输入字段。我们的`useForm`定制钩子封装了如何在`useForm`钩子内部处理状态的逻辑。这使得理解我们的代码更加容易。

## 代码库

完整的表单模块可在 [GitHub](https://github.com/sophiabrandt/reason-form/blob/4d9e5093f43c047d4c807adff1849f5e1281cbba/src/Form.re) 上获得。

## 思想

在最初的一些小问题之后，写 ReasonReact 出人意料地简单。

ReasonReact 保持接近 React.js.

你可以“在 React.js 中思考”并将其移植到 ReasonReact/ReasonML。新的 JSX 语法(今年早些时候发布)也几乎和原生 React.js 一样

有时候，相似性几乎是一种伤害，因为它们隐藏了原因，JavaScript *毕竟是*不同的语言。

模式匹配是理性的致命特征之一。我是在学习仙丹的时候开始享受的，现在也很乐意在 ReasonReact 的前端使用。

### 进一步阅读

*   [原因反应](https://reasonml.github.io/reason-react/en/)
*   [一个原因反应教程](https://jaredforsyth.com/posts/a-reason-react-tutorial/)
*   使用定制的 React 钩子来简化表单作者:詹姆斯·金