# 如何用 useReducer 钩子模仿 setState 方法？

> 原文：<https://dev.to/przemwo/how-to-use-usereducer-hook-to-mimics-setstate-method-5990>

当使用类组件时，你可以使用`setState`方法只更新**状态对象的一部分**。React 将更新的值与状态对象的先前值合并:

```
//Somewhere in CLASS COMPONENT

//set initial state:
state = {
  name: 'Bob',
  age: 25,
};

//update name value with setState method:
this.setState({ name: 'Alice' });

//updated state:
state = {
  name: 'Alice',
  age: 25,
}; 
```

现在让我们试着用`useState`钩子和函数组件:
做同样的事情

```
//Somewhere in FUNCTION COMPONENT

//set initial state:
const [state, setState] = useState({ name: 'Bob', age: 25 });

//update name value with setState updater function
setState({ name: 'Alice' });

//updated state:
state = {
  name: 'Alice',
}; 
```

如你所见，我们“丢失”了关于年龄的信息。`setState`不只是更新名称属性。它返回传递给它的新状态对象。

解决这个问题的一个方法是将`setState`与回调函数
一起使用

```
//Somewhere in FUNCTION COMPONENT

//set initial state:
const [state, setState] = useState({ name: 'Bob', age: 25 });

//update name value with setState and callback function
setState(prevState => ({ ...prevState, name: 'Alice' }));

//updated state:
state = {
  name: 'Alice',
  age: 25,
}; 
```

这工作非常好。但是...如果你想在函数组件中使用简洁版本的`this.setState`方法(或者只是找点乐子),你可以使用`useReducer`钩子来完成。

## useReducer 喜欢 setState 方法

首先让我们写一些伪代码:

```
//Somewhere in FUNCTION COMPONENT

const reducer = (prevState, updatedProperty) => ({
  ...prevState,
  ...updatedProperty,
});

const initState = {
  name: 'Bob',
  age: 25,
};

//initialize state with initState
const [state, setState] = useReducer(reducer, initState);

//update name value (like we do in class component!)
setState({ name: 'Alice' });

//updated state:
state = {
  name: 'Alice',
  age: 25,
}; 
```

这一次，它像预期的那样成功了。我们已经更新了名称值，并且没有丢失年龄属性。

而现在完整的工作示例:

```
import React, { useReducer, useEffect } from 'react';

const reducer = (prevState, updatedProperty) => ({
  ...prevState,
  ...updatedProperty,
});

const initState = {
  name: 'Bob',
  age: 25,
  isLoading: true,
};

function App() {
  const [state, setState] = useReducer(reducer, initState);

  const handleOnChange = (e) => setState({ [e.target.name]: e.target.value });

  useEffect(() => {
    setState({ isLoading: false });
  }, []);

  const { name, age, isLoading } = state;

  return(
    <>
      {isLoading ? 'Loading...' : (
        <>
          <input type="text" name="name" value={name} onChange={handleOnChange} />
          <input type="text" name="age" value={age} onChange={handleOnChange} />
        </>
      )}
    </>
  );
} 
```

现在最重要的问题是:

你**一定要**在这里用`useReducer`吗？

不要！

可以吗？

是啊！如果你喜欢:)

这篇文章的灵感来自于肯特·c·多兹(Kent c . Dodds)“课程”[用 React 挂钩简化 React 应用](https://egghead.io/courses/simplify-react-apps-with-react-hooks)。