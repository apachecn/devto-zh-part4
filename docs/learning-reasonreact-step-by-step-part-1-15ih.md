# 学习原因循序渐进第 1 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-1-15ih>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

让我们从[昨天的帖子](https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-0-48la)继续，为我们的应用程序构建框架:一个带有 ReasonReact 和钩子的简单表单。

转到`src`文件夹。

主`App.re`文件应该只是呈现一个`Form`组件:

```
[@react.component]
let make = () => <Form />; 
```

Enter fullscreen mode Exit fullscreen mode

语法看起来很陌生。什么是`[@react.component]`？

它是一个装饰属性，告诉 ReasonReact 您正在编写一个 React 组件。咄！

最新的 React 版本在引擎盖下使用 React 挂钩。

你的应用会抛出一个错误，因为我们还没有一个`Form`组件(`src/Form.re` ):

```
// create an alias for ReasonReact. String,
// so that we don't have to type out the full function every time
let str = ReasonReact.string;

[@react.component]
let make = () =>
  <div className="section is-fullheight">
    <div className="container">
      <div className="column is-4 is-offset-4">
        <div className="box">
          <form>
            <div className="field">
              <label className="label"> {"Email Address" |> str} </label>
              <div className="control">
                <input
                  className="input"
                  type_="email"
                  name="email"
                  required=true
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
                  required=true
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
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，在 Reason(和 Ocaml)中，文件名充当模块/组件的名称空间。

在这里，我们设置了一个带有[布尔玛](https://bulma.io/)类的 HTML 表单用于样式化。就像在 React 中，我们必须用`className`替换`class`。

`type`是保留关键字，所以你得用`type_`。

您还必须通过书写`required=true`来拼写出输入字段是必需的。使用 React，您可以省略`=true`赋值。在 ReasonReact 中，你必须明确。

### 进一步阅读

*   [原因反应](https://reasonml.github.io/reason-react/en/)
*   [原因反应无效的属性名](https://reasonml.github.io/reason-react/docs/en/invalid-prop-name)
*   使用定制的 React 钩子来简化表单作者:詹姆斯·金