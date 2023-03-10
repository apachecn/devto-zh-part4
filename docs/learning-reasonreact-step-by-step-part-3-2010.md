# 学习原因循序渐进第 3 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-3-2010>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

在我的上一篇文章中，我试图为 React 表单创建一个定制的钩子函数。

那没有像我预期的那样起作用。一些好心人帮助了我，并给了我一些建议。

让我们换个角度，尝试一些不同的东西。我将后退一步，将逻辑添加到表单组件中，而不是创建一个自定义挂钩。也许以后我可以解耦。

使用一个`Js.Dict`来存储数据(电子邮件和密码)被证明是困难的，似乎是一种反模式。

我们目前拥有的代码非常简单，可以在 [GitHub](https://github.com/sophiabrandt/reason-form/commit/632547ae4a9c353beb885516d345cac02d89ccbe) 上看到。

## useReducer 钩子与 React 的原因

作为替代，我将编写一个`useReduce`钩子并添加状态作为 [ReasonML 记录](https://reasonml.github.io/docs/en/record)。

好消息是记录都是打出来的。坏消息是字段名(键)是固定的。因此，我必须对我想要存储的数据进行硬编码。

```
/* src/Form.re */
type state = {
  email: string,
  password: string,
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们设置了“存储容器”类型，其中电子邮件和密码是字符串。

`useReducer`几乎与[反应](https://reactjs.org/docs/hooks-reference.html#usereducer)中的一样。

我们来写动作:

```
/* src/Form.re */
type action =
  | SetEmail(string)
  | SetPassword(string)
  | SubmitForm; 
```

Enter fullscreen mode Exit fullscreen mode

当有人在电子邮件字段中键入内容时，我们必须存储输入内容。`SetEmail`动作/函数采用字符串类型的参数。

密码也是如此。

之后，我们要处理如何提交表单值。`SubmitForm`动作没有任何参数。

现在，对于`useReducer` :

```
/* src/Form.re */
//...

let reducer = (state, action) => {                   // (A)
  switch (action) {
    | SetEmail(email) => {...state, email}           // (B)
    | SetPassword(password) => {...state, password}
    | SubmitForm => {                                // (B)
      Js.log({j|Form submitted with values: $state|j});
      {email: "", password: ""};
    };
  }
};

[@react.component]
let make = () => {
  let initialState = {email: "", password: ""};    // (D)

  let (state, dispatch) = React.useReducer(reducer,initialState); // (E) 
```

Enter fullscreen mode Exit fullscreen mode

在第 A 行，我们创建了 reducer 函数，每个动作都有一个 switch 语句。

我们的状态是一个记录，所以我们可以使用 spread 语法来更新它(那看起来像 JavaScript！)(参见`line B`)。

`SetEmail`和`SetPassword`几乎一模一样。

`SubmitForm` ( `line C`)用一个 JavaScript `console.log`注销我们的状态。然后它将状态重置为空字符串。

我们不得不对[字符串插值](https://bucklescript.github.io/docs/en/interop-cheatsheet.html#string-unicode-interpolation)使用奇怪的语法。

在表单组件中，我用一个空的电子邮件和密码字符串(`line D`)创建了一个初始状态。

在 React 中，我们使用一个非结构化数组来初始化`useReducer`，即:

```
const [state, dispatch] = React.useReducer(reducerFunction, initialState) 
```

Enter fullscreen mode Exit fullscreen mode

Reason 使用了一个[元组](https://reasonml.github.io/docs/en/tuple)，但除此之外，看起来和 React ( `line E`)差不多。

现在，我们只需要将调度功能连接到我们的 JSX:

```
/* src/Form.re */
//... 
  let valueFromEvent = evt: string => evt->ReactEvent.Form.target##value; // (A)

  <div className="section is-fullheight">
    <div className="container">
      <div className="column is-4 is-offset-4">
        <div className="box">
          <form
            onSubmit={
              evt => {
                ReactEvent.Form.preventDefault(evt);
                dispatch(SubmitForm);
              }
            }>
            <div className="field">
              <label className="label"> {"Email Address" |> str} </label>
              <div className="control">
                <input
                  className="input"
                  type_="email"
                  name="email"
                  value={state.email}
                  required=true
                  onChange={evt => valueFromEvent(evt)->SetEmail |> dispatch} // (B)
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
                  onChange={
                    evt => valueFromEvent(evt)->SetPassword |> dispatch // (B)
                  }
                />
              </div>
            </div>
            <button
              type_="submit" className="button is-block is-info is-fullwidth">
              {"Login" |> str}
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？

我从 Jared Forsythe 的教程中偷了 A 行:

> 在 JavaScript 中，我们会执行 evt.target.value 来获取输入的当前文本，这就是 React 的等价原因。ReasonReact 的绑定还没有一种类型良好的方法来获取输入元素的值，所以我们使用 ReactEvent。Form.target 获取“事件的目标元素”作为“无所不包的 javascript 对象”，用“JavaScript 访问器语法”##value 获取值。
> 
> 这牺牲了一些类型安全性，对于 ReasonReact 来说，最好是提供一种直接获取输入文本的安全方法，但这就是我们目前所拥有的。注意，我们已经将 valueFromEvent 的返回值注释为 string。如果没有这个，OCaml 将使返回值为‘a ’(因为我们使用了无所不包的 JavaScript 对象),这意味着它可以与任何东西统一，类似于 Flow 中的 any 类型。

我们将使用这个函数将它与密码和电子邮件字段的`onChange`函数连接起来(参见`line B`)。

首先，我们获取事件并提取其值，然后我们将函数传送给我们的`SetEmail`或`SetPassword`动作，最后传送给我们的分派。

为什么是`->`和`|>`？

第一个是[管先](https://reasonml.github.io/docs/en/pipe-first):

> `->`是一个方便的操作符，允许你从里到外“翻转”你的代码。`a(b)`变成了`b->a`。这是一段没有任何运行时成本的语法。

另一个是[钻杆向前/钻杆最后/反向应用操作员](http://reasonmlhub.com/exploring-reasonml/ch_functions.html#the-reverse-application-operator)。基本上也是这样。但是有些函数要求您将管道中的东西作为第一个参数添加，有些作为最后一个参数添加。

有点丑。大多数 JavaScript 和 BuckleScript 互操作要求管道优先。Ocaml 和 Reason 本机代码主要使用 pipe-last。

## 代码库

完整的代码在 [GitHub](https://github.com/sophiabrandt/reason-form/blob/e51d7cde835ab56eec4cd98564a59d8dc77e0811/src/Form.re) 上。

## 思想

与 ReasonReact 配合良好，React 开发人员会非常熟悉。

我喜欢 ReasonML 的[模式匹配](https://reasonml.github.io/docs/en/pattern-matching)，很适合`useReducer`。

### 进一步阅读

*   [一个原因反应教程](https://jaredforsyth.com/posts/a-reason-react-tutorial/)
*   - >和| >在道理上有什么区别？