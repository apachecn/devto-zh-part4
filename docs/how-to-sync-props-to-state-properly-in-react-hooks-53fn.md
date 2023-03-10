# 如何在 React 钩子中将道具正确同步到状态

> 原文：<https://dev.to/paramharrison/how-to-sync-props-to-state-properly-in-react-hooks-53fn>

首先，你至少需要知道`useState`和`useEffect`的基础。以便你能更好地理解这个例子。

我们将看到两个案例

1.  如何在 React 钩子中将道具传递给状态
2.  如何在 React 钩子中将道具同步到状态

### 使用 useState 钩子将道具传递给状态

```
import React, { useState } from 'react';

const Profile = props => {
  const [profileState, setProfileState] = useState(props);
  return (
    <div>
      <p>
        <strong>Name:</strong>
        {profileState.name}
      </p>
      <p>
        <strong>Email:</strong>
        {profileState.email}
      </p>
    </div>
  );
};

export default Profile; 
```

它是一个接受`props`的简单组件。我们将这个道具作为`initialState`传递给 useState。

现在，如果道具被改变，状态被改变并重新渲染，会发生什么？

不，不会的。状态会保持不变，`useState`不会在道具改变时初始化。让我们看看例子。

```
const App = () => {
  const [state, setState] = useState({
    name: 'Param',
    email: 'param@gmail.com',
  });

  const handleChange = () => {
    setState({
      name: 'Vennila',
      email: 'vennila@gmail.com',
    });
  };

  return (
    <div className="App">
      <Profile {...state} />
      <button onClick={handleChange}>Change Profile</button>
    </div>
  );
}; 
```

点击按钮，它不会改变配置文件。你可以控制更新的道具，但不能控制 UI。

```
const Profile = props => {
  const [profileState, setProfileState] = useState(props);
  console.log(profileState, props);

  return (
    <div>
      <p>
        <strong>Name: </strong>
        {profileState.name}
      </p>
      <p>
        <strong>Email: </strong>
        {profileState.email}
      </p>
    </div>
  );
}; 
```

点击
按钮后，控制台将显示此信息

```
// profileState - { name: "Param", email: "param@gmail.com" }
// props - { name: "Vennila", email: "vennila@gmail.com" } 
```

即使 props 得到了更新，useState 也没有重置状态。

### 如何在 React 钩子中同步道具到状态

这是`useEffect`的完美用例。每当`props`改变时，我们将在 useEffect 中设置状态。

让我们改变例子，看看，

```
...

  useEffect(() => {
    setProfileState(props);
  }, [props]);

... 
```

这样，每当道具被改变时，它将被设置为 useEffect 中的状态。useEffect 主要用于处理副作用，这是使用它一个例子。

请点击这里查看示例

[https://codesandbox.io/embed/props-to-state-react-hooks-o2wd2](https://codesandbox.io/embed/props-to-state-react-hooks-o2wd2)

这就是全部，我们很快会看到更多 react 钩子的例子和用例😋