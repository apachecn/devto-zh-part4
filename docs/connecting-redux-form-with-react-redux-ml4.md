# 连接 Redux 表单和 React Redux

> 原文：<https://dev.to/itsmenatalie/connecting-redux-form-with-react-redux-ml4>

在 React 中处理表单有时会很麻烦。您需要注意适当的状态管理、流畅的用户体验、简单的表单声明、可重用的组件等等。但是我使用 Redux Form 的原因是验证。我想让表单的实现更加简单快捷，而不需要在页面组件中添加大量代码。但当我试图将 Redux Form 与 React Redux 连接起来时，我碰壁了。

这不是我第一次这么做，所以我没想到会有什么困难。我错了。有了最新的依赖关系，我在连接`reduxForm`和`connect()`函数时尝试了许多不同的方法。从将一个放入另一个中，到将导出的组件与每个功能分别连接。一切都不正常。不管我做了什么，我最终得到了这个错误消息:

```
Uncaught Invariant Violation:
You must pass a component to the function returned by connect.
Instead received {"displayName":"ReduxForm",
"defaultProps": {destroyOnUnmount":true, "form":"loginForm", etc.}} 
```

Enter fullscreen mode Exit fullscreen mode

我决定在网上寻求帮助。没有直接的答案来帮助我，我错过了什么，或者我应该如何正确地做它。显然，人们仍然在这个问题上挣扎。检查 StackOverflow 和其他论坛只能找到过时的工作版本。甚至来自 [redux-form 文档](https://redux-form.com/8.2.2/docs/faq/howtoconnect.md/)的官方例子也不起作用！我决定尝试一步一步的方法，将每个部分分别连接到不同的文件中。我以一个额外的文件`container.tsx`结束，在那里我连接了一个更高层次的组件。让我们看看发生了什么。

首先，我们用 Redux Form 连接一下我之前创建的一个表单组件:

```
import React from 'react'; 
import { Field, InjectedFormProps, reduxForm } from 'redux-form';

export interface IDispatchProps {
  //...
}

const LoginComponent = (props:IDispatchProps) => (
  <form>...</form>
);

export const LoginForm = reduxForm({
  form: 'loginForm',
})(LoginComponent); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们看看如何为这个组件实现一个容器:

```
import * as React from 'react';
import { connect } from 'react-redux';

import { LoginForm, IProps } from './LoginForm';
import actions from './actions';
import selector from './selector';

const LoginContainer = (props:IProps) =>
  <LoginForm {...props} />;

export default connect(selector, actions)(LoginContainer); 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做的是，我们将创建的`LoginComponent`函数用`reduxForm`包装起来。在`container.tsx`文件中`LoginContainer`函数会将我们的道具传递给导入的组件。最后，我们使用`connect()`来创建容器和商店道具之间的通信。如果我们想将 TypeScript 添加到表单中，并且根本不使用`any`，那么最棘手的部分就开始了！离开基本组件的道具会抛出一个错误，说我们的道具在这个组件中不存在。但我们刚刚过了，对吧？

```
TS2559: Type '{ loginAction: () => any; }' has no properties in common with type
'IntrinsicAttributes & IntrinsicClassAttributes<FormInstance<{},
Partial<ConfigProps<{}, {}, string>>, string>> & Readonly
<Partial<ConfigProps<{}, {}, string>>> & Readonly<...>'. 
```

Enter fullscreen mode Exit fullscreen mode

我花了至少一个小时才想明白，回答“为什么？”。我决定再次上网寻求帮助，但我找到的唯一答案是不清楚发生了什么，我应该把我的道具放在哪里。经过几次尝试，我终于找到了一个明智的解决方案，我很满意。首先，在`LoginForm.tsx`文件中，我们需要用表单中的字段名称创建一个接口`IFormProps`。接下来我们需要从`redux-form`库中导入`InjectedFormProps`的类型定义，并将其与我们的 props 接口相结合。InjectedFormProps 接受两个参数:`FormProps = {}`和`P={}`。第一个表示将放入表单的表单数据，第二个是组件的道具。理论上，您可以将两个对象都留空。但这不是我们使用 TypeScript 的原因，对吗？

```
import React from 'react'; 
import { Field, InjectedFormProps, reduxForm } from 'redux-form';

interface IFormProps {
  username:string;
  password:string;
}

export interface IDispatchProps {
  //...
}

const LoginComponent =
(props:IDispatchProps & InjectedFormProps<IFormProps, IDispatchProps>) => (
  <form>
    <Field
      name="username"
      component={Input}
      type="text"
      label="Enter username"
    />
    <Field
      name="password"
      component={Input}
      type="password"
      label="Enter password"
    />
  </form>
);

export const LoginForm = reduxForm<IFormProps, IDispatchProps>({
  form: 'loginForm',
})(LoginComponent); 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，如果你想把 Redux Form 和 React Redux 连接起来，那就帮你自己一个忙，创建另一个文件作为这两个库之间的桥梁。不要忘了将它与 TypeScript 结合起来。如果你想看一个例子，看看我的工作实现: [Redux Form with TypeScript](https://github.com/CodeMeNatalie/Redux-Forms-with-TypeScript) 。祝你好运，下次再见！👍🏻

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[codemenata lie](https://github.com/CodeMeNatalie)/[Redux-Forms-with-TypeScript](https://github.com/CodeMeNatalie/Redux-Forms-with-TypeScript)

### 连接 Redux 表单和 React Redux 示例

<article class="markdown-body entry-content container-lg" itemprop="text">

### 代码威胁 [![redux form](img/8c103dad64c972dc6efddc0a4fade647.png)](https://camo.githubusercontent.com/354760287c15fe73bfcd0b5d21d9c2a3b2501bc42bd5213f680c3b705c6e3d20/687474703a2f2f636f64656d656e6174616c69652e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031392f30372f72656475782d666f726d2e706e67)

### 用 TypeScript 还原表单

这个项目是 Redux Forms 和 TypeScript 的工作实现的一个例子。在我的文章[中描述了连接 Redux 表单和连接](https://raw.githubusercontent.com/CodeMeNatalie/Redux-Forms-with-TypeScript/master/codemenatalie.com/blog/connecting-redux-form-and-connect)的整个过程。

[网站](https://codemenatalie.com)♀[博客](http://codemenatalie.com/blog)

## 快速启动

*   克隆回购:`git clone https://github.com/CodeMeNatalie/Redux-Form-with-TypeScript.git`
*   安装依赖项
*   运行应用程序`npm start`
*   打开 [http://localhost:3000](http://localhost:3000) 在浏览器中查看项目。

[![coding doge](img/8ff777942927e9f7c33303a6319d0dde.png)](https://camo.githubusercontent.com/b3b5969b73c0ab5267e90f97519969c45eb99e1da020fa062f881bf5c4758780/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f6d43524a446f323455764a4d412f67697068792e676966)

### 享受<g-emoji class="g-emoji" alias="computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4bb.png">💻</g-emoji>

</article>

[View on GitHub](https://github.com/CodeMeNatalie/Redux-Forms-with-TypeScript)

重要通知:
上面的文章和示例是用 redux-form 8.2.2 和 react-redux 6.0.1 创建的。