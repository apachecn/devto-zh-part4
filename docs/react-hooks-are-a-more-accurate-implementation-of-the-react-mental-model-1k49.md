# React 挂钩是 React 心理模型的更准确的实现

> 原文：<https://dev.to/craigmichaelmartin/react-hooks-are-a-more-accurate-implementation-of-the-react-mental-model-1k49>

与 React 类相比，使用钩子的 React 功能组件是 React 心理模型对于状态和效果的更精确的实现

> TL；React 博士使更新 DOM 成为声明性的。钩子使得组件本身具有声明性。

React 的关键是允许将声明性代码映射到命令性 DOM。

对于功能组件来说尤其如此，功能组件只是将数据映射到描述 UI 的对象。React 将接受这个对象并(强制性地)更新 DOM。

然而，对于类组件，虽然 render 函数仍然是声明性的，但类实例本身(状态所在的地方)是可变的——这使得推理变得更加困难。

状态和副作用的实现在这些类组件中——与变异实例相关联。

React 钩子是 React 中状态和副作用的重新构思和重新实现——一个实现不是在类组件中，而是在功能组件中。作为一个基本定义，它们是让您“挂钩”反应状态和生命周期特性的功能。但关键是它们在声明性 api 中用功能组件实现。

"但是为什么这是“反应心智模型的更精确的实现”？"

React 挂钩允许组件具有真正的声明性，即使它们包含状态和副作用。

状态现在是以声明的方式检索的，而不改变组件的结构(就像类实例那样)。

副作用现在声明性地与状态一致，而不是与组件的突变一致。

正如 react 的第一个关键是到 DOM 的声明性映射，钩子是第二个关键:在组件中为状态和副作用提供声明性 api。

“嗯，好的，当然..来点代码怎么样？”

让我们看看做同一件事的两个版本。第一个版本使用初始的基于类的状态和效果实现，第二个版本使用新的基于钩子的实现。

这个例子是一个(非常做作的)用户组件。输入将搜索用户并显示他们的名字，可以编辑和保存。

### 使用 React 的初始基于类实现状态和效果

[https://code sandbox . io/s/react-classes-is-the-error-mental-model-n9zbs](https://codesandbox.io/s/react-classes-are-the-wrong-mental-model-n9zbs)

```
/*
 * A code sample to show how React class components are
 * not the best implementation of the react mental model.
 *
 * Limitations:
 * - 1\. With react classes, `this` is mutable and harder
 *      to reason about
 * - 2\. With react classes, the lifecyle hooks are aligned
 *      with the component instead of the data.
 *
 * To see 1: save a user's name, and then immediately
 * change it again. You'll see the confirmation alert has
 * the wrong name (the new one, not the one which was saved).
 * Because "this" is mutated before the save finishes,
 * the wrong data is surfaced to the user.
 *
 * To see 2: Notice how the code for componentDidUpdate
 * and componentDidMount is doing the same thing? What we
 * care about is changes to "username" data but instead
 * the model here is built around changes to the component.
 */

import React from "react";

class User extends React.Component {
  state = {
    username: this.props.username
  };

  handleUsernameChange = e => {
    this.setState({ username: e.target.value });
  };

  handleNameChange = e => {
    const name = e.target.value;
    this.setState(state => ({
      ...state,
      user: {
        ...state.user,
        name
      }
    }));
  };

  save = () => {
    // Pretend save that takes two seconds
    setTimeout(
      () => alert(`User's name has been saved to "${this.state.user.name}`),
      2000
    );
  };

  async fetchUser() {
    const response = await fetch(
      `https://api.github.com/users/${this.state.username}`
    );
    if (!response.ok) {
      return {};
    }
    return await response.json();
  }

  async componentDidMount() {
    if (this.props.username) {
      if (this.state.username) {
        const user = await this.fetchUser();
        this.setState({ user });
      }
    }
  }

  async componentDidUpdate(prevProps, prevState) {
    if (this.state.username !== prevState.username) {
      if (this.state.username) {
        const user = await this.fetchUser();
        this.setState({ user });
      }
    }
  }

  componentWillUnmount() {
    // clean up any lingering promises
  }

  render() {
    return (
      <>
        Search
        <input
          value={this.state.username || ""}
          placeholder="Github Username"
          onChange={this.handleUsernameChange}
        />
        <hr />
        {this.state.user && (
          <>
            <h2>Name</h2>
            <input
              value={this.state.user.name}
              onChange={this.handleNameChange}
            />
            <button onClick={this.save}>Save</button>
          </>
        )}
      </>
    );
  }
}

export default User; 
```

下面是正在运行的代码。你可以看到上面代码注释中描述的第 1 点:保存一个用户名，然后立即再次更改它。您将看到确认警报的名称错误(新名称，而不是保存的名称)。

[https://codesandbox.io/embed/n9zbs](https://codesandbox.io/embed/n9zbs)

现在让我们看看...

### 使用 React 新的基于钩子的状态和效果实现

[https://code sandbox . io/s/react-hooks-are-a-better-mental-model-f9kql](https://codesandbox.io/s/react-hooks-are-a-better-mental-model-f9kql)

```
/*
 * A code sample to show how React functional components useing "hooks" are a
 * better implementation of the react mental model.
 */
import React, { useState, useEffect } from "react";

const fetchUser = async username => {
  if (!username) return await {};
  const response = await fetch(`https://api.github.com/users/${username}`);
  if (!response.ok) return {};
  return await response.json();
};

const saveUser = user => {
  // Pretend save that takes two seconds
  setTimeout(() => alert(`User's name has been saved to "${user.name}`), 2000);
};

export default ({ username: initialUsername = "" }) => {
  const [user, setUser] = useState({});
  const [username, setUsername] = useState(initialUsername);

  useEffect(() => {
    const doFetchAndSet = async () => {
      const u = await fetchUser(username);
      setUser(u);
    };
    doFetchAndSet();
  }, [username]);

  return (
    <>
      Search
      <input
        value={username || ""}
        placeholder="Github Username"
        onChange={e => setUsername(e.target.value)}
      />
      <hr />
      {user.name && (
        <>
          <h2>Name</h2>
          <input
            value={user.name}
            onChange={e => setUser({ ...user, name: e.target.value })}
          />
          <button onClick={() => saveUser(user)}>Save</button>
        </>
      )}
    </>
  );
}; 
```

这是正在运行的代码。如果您试图重现第一个示例中的错误，您将无法做到。

[https://codesandbox.io/embed/f9kql](https://codesandbox.io/embed/f9kql)

* * *

我遗漏了什么见解？我忽略或夸大了什么？让我知道！