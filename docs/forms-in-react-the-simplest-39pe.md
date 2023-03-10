# 反应中的形式🚧最简单的

> 原文：<https://dev.to/droidmakk/forms-in-react-the-simplest-39pe>

> 表单验证有时可能是一场噩梦。在尝试了一些代码模式后，我得出了一个结论。这可能是一个没有任何插件的最佳解决方案。
> **注意**:更好的方法总是受欢迎的&我在寻找它⌛

[![Preview](img/4e926754d8206abadd5b638200212837.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dPHCD2dT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/sbdfwZD.gif)

[代码沙盒链接](%5Bhttps://codesandbox.io/s/simple-form-oio3w%5D(https://codesandbox.io/s/simple-form-oio3w))

## InputField

> 让我们开始吧。重要的事情先来。让我们为输入字段创建一个组件。
> 
> *   接受用户输入并传递其他公共属性的输入字段
> *   *handle 无效的*方法在检测到错误时施展魔法
> *   *onChange* 方法重置错误并将控制权传递给父组件

```
import React from "react";

/** We'll be using this only for username and password **/
export const InputField = props => {
  const { message, ...rest } = props; // Filter what you need and transfer rest to input
  let [error, setError] = React.useState(false); //display error message

  const handleInvalid = e => {
    e.preventDefault();
    setError(true);
    props.handleInvalid(e);
  }; // Handle error
  const onChange = e => {
    setError(false);
    props.handleChange(e);
  }; // Reset error

  // The Component
  return (
    <div className="input-container">
      <input {...rest} onInvalid={handleInvalid} onChange={onChange} />
      {error ? <span className="error-message">{message}</span> : ""}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 表单组件

> 现在让我们创建一个表单组件，它将把我们的输入字段包装在一个表单周围，并提供一些方法来处理它。
> 
> *   我们有一个状态变量来保存表单对象
> *   *handleChange* 方法根据名称处理输入字段的特定更新。
> *   *handleSubmit* 方法，一旦所有字段都被正确填充，该方法将帮助我们点击 API
> *   **action = " JavaScript:void(0)"**当我试图找出处理表单的最佳方式时，这是我特别感兴趣的。这将阻止表单元素执行默认工作。

```
import React from "react";
import { InputField } from "./InputField";

export const Form = props => {

  const [state, setState] = React.useState({ mail: "", password: "" });

  const handleChange = e =>
    setState({ ...state, [e.target.name]: e.target.value });

  const handleSubmit = e => console.info("FORMDATA", state);

  return (
    <form
      action="javascript:void(0)"
      onChange={handleChange}
      onSubmit={handleSubmit}
    >
      <InputField name="mail" type="email" message="We need a proper mail id" />
      <InputField name="password" minLength="9" type="password" message="Not a valid password"
      />
      <input type="submit" value="Login" />
    </form>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

## App.css

现在让我们添加一些 css，让它看起来至少有一点点漂亮

```
.error-message {
  color: darkred;
  text-align: right;
  font-size: 12px;
}

.input-container {
  display: flex;
  flex-direction: column;
  width: 40%;
  margin: auto;
}

.input-container > input {
  border-top: none;
  border-right: none;
  border-left: none;
  margin-bottom: 5px;
  margin-top: 20px;
}

input:invalid {
  border-color: darkred;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我希望在 React 中处理表单有多简单。都是模式。批评是最受欢迎的，因为我正在寻找进一步的最佳实践形式。如果有比这更好的地方，请告诉我