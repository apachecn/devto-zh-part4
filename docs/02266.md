# 学习原因逐步反应第 2 部分

> 原文：<https://dev.to/sophiabrandt/learning-reasonreact-step-by-step-part-2-3h20>

**更新**:

reason ml+BuckleScript 现为 [Rescript](https://rescript-lang.org/blog/bucklescript-is-rebranding) 。随着围绕这些工具的生态系统发生了变化，这篇博文不再准确。

* * *

现在我们已经有了输入表单的 HTML/JSX 框架:一个简单的登录表单，样式为[布尔玛](https://bulma.io/)。

(代码在 [Github](https://github.com/sophiabrandt/reason-form/commit/632547ae4a9c353beb885516d345cac02d89ccbe) 上有)。)

## 理性的绊脚石

这个博客系列的想法是创建一个带有挂钩的 ReasonReact 表单，以了解 ReasonML 和 ReasonReact 是如何工作的。

我从詹姆斯·金关于 **[的教程中得到灵感，使用定制的 React 钩子来简化表单](https://upmostly.com/tutorials/using-custom-react-hooks-simplify-forms)** 。当我在年初阅读它时，它帮助我理解了新的 React Hooks API 是如何工作的。

在文章中，James 创建了一个定制的`useForm`钩子，所以这也是我想在 ReasonReact 中创建的。

当您有 HTML 表单时，您将需要获取 HTML 元素(目标)的[值，以便您可以将它存储在某个地方。

在 React 中，你可以使用`useState`钩子或者一个类组件，并将值存储为 state。](https://devdocs.io/html/attributes#target-attribute)

例如，您可以将每个值存储为一个字符串，或者将所有值存储为一个 JavaScript 对象。

前面提到的博文使用了一个带有计算键的 JavaScript 对象:

```
const handleChange = event => {
  event.persist()
  setValues(values => ({ ...values, [event.target.name]: event.target.value }))
} 
```

Enter fullscreen mode Exit fullscreen mode

ReasonML 使用对象的方式与 Javascript 不同。

但是我们确实需要一个能够处理带有键和值的复合数据的数据结构(“哈希映射”)。当然，理智提供了这样的东西: **[记录](https://reasonml.github.io/docs/en/record#docsNav)** 。

记录默认为*不可变*并被录入！但是它们不支持计算密钥，你必须事先知道这些密钥。

因此，上述方法不适用于开箱即用的 ReasonML。

BuckleScript 来救援了！ BuckleScript 很好地解释了我们使用 JavaScript 对象的目的。文档提供了关于如何使用和使用什么的建议。

所以，记录不行，我们用一个 [JS。法官声明](https://bucklescript.github.io/docs/en/object#hash-map-mode) :

```
let myMap = Js.Dict.empty();
Js.Dict.set(myMap, "Allison", 10); 
```

Enter fullscreen mode Exit fullscreen mode

让我们**尝试**在 ReasonReact 中创建`useForm`钩子(下面的代码不起作用):

```
/* inside src/Form.re */

module UseForm = {
  [@react.component]
  let make = (~callback) => {
    let valuesMap = Js.Dict.empty();
    let (values, setValues) = React.useState(() => valuesMap);  // (A)

    let handleChange = (evt) => {
      let targetName = evt:string => evt->ReactEvent.Form.target##name;    // (B)
      let targetValue = evt:string => evt->ReactEvent.Form.target##value;  // (B)
      let payload = Js.Dict.set(valuesMap,{j|$targetName|j},targetValue);  // (C)

      ReactEvent.Form.persist(evt);

      setValues(payload); // (D)
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们设置一个空的`Js.Dict`作为`useState`钩子(`line (A)`)的初始值。

在`handleChange`函数中我们要告诉 ReasonReact，HTML 目标名和 HTML 目标值是字符串(`line (B)`)。

然后我们使用`Js.Dict.set`函数将新值添加到字典中(`line (C)`)，最后尝试使用`useState`函数(`(D)`)设置这些值。

我不得不使用 BuckleScript 的[字符串插值语法](https://bucklescript.github.io/docs/en/interop-cheatsheet#string-unicode-interpolation)来创建`Js.Dict`键(`line (C)`)。

不幸的是，这不起作用。编译器在第`line (D)` :
行报错

```
Error: This expression has type unit but an expression was expected of type Js.Dict.t(ReactEvent.Form.t => string) => Js.Dict.t(ReactEvent.Form.t => string) 
```

Enter fullscreen mode Exit fullscreen mode

你总是可以在 Reason 中嵌入原始的 JavaScript 来解决这些问题，但是这是非常不鼓励的。

作为一个新手，我现在不知道如何继续下去。

如何合并`JS.Dict`对象？这个界面看起来像一个 [JavaScript Map](https://devdocs.io/javascript/global_objects/map) ，但是使用“对象传播语法”也不行。ReasonReact 使用这种语法来更新他们的不可变记录，但它不适用于 BuckleScript 的`Js.Dict`。

此外，我怎样才能将`useState`钩与`Js.Dict`一起使用呢？

也许我在这里使用了一个**反模式**，这就是为什么在 ReasonReact 中实现 JavaScript 解决方案如此困难。

我也不确定**文件结构**。Reason 鼓励更少的文件和[嵌套模块](https://reasonml.github.io/docs/en/module)，但是这如何与自定义(React)挂钩一起工作呢？