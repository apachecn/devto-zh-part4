# 用 React 挂钩简化受控组件

> 原文：<https://dev.to/stanleyjovel/simplify-controlled-components-with-react-hooks-23nn>

使用 react 时，几乎不可避免地会遇到受控组件。受控组件是一个 react 组件，它使用`setState()`控制表单中输入元素的值。

在引入新的 hooks API 之前，您只能将类组件用于此目的，因为它们是唯一可以存储状态和访问 setState API 的组件。但是现在随着钩子的引入，我们终于可以处理任何组件(函数或类)中的状态变化，这大大简化了编写受控组件。

下面是一个使用传统方法的受控组件与类组件的示例:

*稳压控制部件. js*T2

```
import React, { Component } from 'react'

export class RegularControlledComponent extends Component {
  state = {
    username: '',
    password: '',
  }

  handleUsernameChange = (e) => this.setState({
    username: e.currentTarget.value,
  })

  handlePasswordChange = (e) => this.setState({
    password: e.currentTarget.value,
  })

  render() {
    return (
      <form>
        <div>
          <label>Username:</label>
          <input type="text" onChange={this.handleUsernameChange} />
        </div>
        <div>
          <label>Password:</label>
          <input type="text" onChange={this.handlePasswordChange} />
        </div>
        <input type="submit" />
      </form>
    )
  }
} 
```

乍一看，这似乎没什么问题，但是如果不是两个输入字段，而是 5 个或 10 个，会发生什么呢？我们将需要 10 个`handleSomeInputFieldChange`函数处理器。
这种方法不可扩展

让我们重写组件，使用钩子来控制输入字段:

*控制元件开关. js*T2

```
import React, { useState } from 'react'

export const ControlledComponentWithHooks = () => {
  const [input, setInput] = useState({})

  const handleInputChange = (e) => setInput({
    ...input,
    [e.currentTarget.name]: e.currentTarget.value
  })

  return (
    <form>
      <div>
        <label>Username:</label>
        <input type="text" name="username" onChange={handleInputChange} />
      </div>
      <div>
        <label>Password:</label>
        <input type="text" name="password" onChange={handleInputChange} />
      </div>
      <input type="submit" />
    </form>
  )
} 
```

要注意的第一个变化是，我们的组件现在是一个函数，随着`useState`钩子的引入，当我们想要使用局部状态时，我们不再需要将函数组件转换成类组件。

其次，我们现在以编程的方式为我们的状态变量设置值，我们完成的方式是通过在第`17`和`25`行的输入字段中添加一个新的`name`属性。神奇的事情发生在第`8` : `[e.currentTarget.name]: e.currentTarget.value`
行，这里我们使用这个名字作为状态对象的属性值，并给它分配输入值。

这种方法是可扩展的，因为在这个表单中输入字段的数量并不重要，它们都将使用相同的`handleInputChange`，并且本地状态将相应地更新。**美丽的**

现在！让我们把钩子抽象到它自己的文件中，使它可以重用，这样会更好。

*useInputChange.js*

```
import { useState } from 'react'

export const useInputChange = () => {
  const [input, setInput] = useState({})

  const handleInputChange = (e) => setInput({
    ...input,
    [e.currentTarget.name]: e.currentTarget.value
  })

  return [input, handleInputChange]
} 
```

现在我们的功能组件`ControlledComponentWithHooks.js`只需要导入并使用新的钩子。

```
import React from 'react'
import { useInputChange } from './useInputChange'

export const ControlledComponentWithHooks = () => {
  const [input, handleInputChange] = useInputChange()

  return (
    <form>
      <div>
        <label>Username:</label>
        <input type="text" name="username" onChange={handleInputChange} />
      </div>
      <div>
        <label>Password:</label>
        <input type="text" name="password" onChange={handleInputChange} />
      </div>
      <input type="submit" />
    </form>
  )
} 
```

是不是很酷？所有的 setState 和输入处理程序样板文件已经从我们的组件中完全删除。有了我们的新钩子，创建受控组件变得简单了，使我们的组件可读性更好，并专注于它所针对的特定业务逻辑。

# 结论

就像`HOCs`和`render prop`一样，钩子允许我们在组件中重用逻辑。我们可以利用这个做各种各样的抽象。

本文的所有源代码都可以在下面的 Repl.it 中找到:
[https://repl . it/@ Stanley jovel/Controlled-Components-with-Hooks](https://repl.it/@StanleyJovel/Controlled-Components-with-Hooks)