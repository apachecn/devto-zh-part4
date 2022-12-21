# React hooks &封闭地狱

> 原文：<https://dev.to/anpos231/react-hooks-the-closure-hell-71m>

# 反应过来的钩子&关闭地狱

自从脸书引入了功能组件和钩子，事件处理程序就变成了简单的闭包。不要误解我，我喜欢功能组件，但是有许多问题困扰着我，当我在社区中询问它们时，最常见的回答是:“*不要担心过早的优化*”。

但这对我来说是个问题，我是用 C 语言编程长大的，我经常担心我的应用程序的性能，即使其他人认为它不那么重要。

## 问题？

由于事件处理程序是闭包，我们需要在每次渲染时或者当它的一个依赖关系改变时重新创建它们。这意味着只依赖于事件处理程序(可能不依赖于处理程序的依赖项)的组件也必须重新呈现。

考虑这个示例代码([试试这里](https://codesandbox.io/s/react-hooks-usestate-m59ve) ):

```
import React, { useState, useCallback, memo } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

let times = 0

const ExpensiveComponent = memo(({ onClick }) => (
  <p onClick={onClick}>I am expensive form component: {times++}</p> ))

const App = () => {
  const [value, setValue] = useState(1);

  const handleClick = useCallback(
    () => {
      setValue(value + 1)
    },
    [value],
  );

  return (
    <div className="app">
      <ExpensiveComponent onClick={handleClick} />
      <button onClick={handleClick}>
        I will trigger expensive re-render
      </button>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

在前面的例子中，点击按钮将导致`ExpensiveComponent`重新渲染。在基于类的组件的情况下，这是不必要的。

## 解？

我是一个实验性的修补者，我试图找到这个问题的解决方案，在这个解决方案中，我们可以使用功能组件，但不必在每次创建新值时都创建新的回调。

所以我创造了`useBetterCallback(fn, deps)`。该函数/钩子的签名与`useCallback(fn, deps)`相同。不同之处在于，无论发生什么，它总是返回相同的处理程序。

你们中的一些人可能会想:'【T1]那么我如何访问新的状态值呢？’。会用一个额外的参数调用你的处理程序，这个参数是一个数组，包含你的回调所依赖的所有依赖项。因此，我们不是重新创建回调，而是将新值传递给现有值。

这里是`useBetterCallback`钩子的源代码。

```
const useBetterCallback = (callback, values) => {
  const self = useRef({
    values: values,
    handler: (...args) => {
      return callback(...args, self.current.values)
    }
  });
  self.current.values = values
  return self.current.handler
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个`useBetterCallback`在起作用的例子([试试这里](https://codesandbox.io/s/react-hooks-usestate-e1207) ):

```
import React, { useState, useRef, memo } from "react";
import ReactDOM from "react-dom";

import "./styles.css";

const useBetterCallback = (callback, values) => {
  const self = useRef({
    values: values,
    handler: (...args) => {
      return callback(...args, self.current.values)
    }
  });
  self.current.values = values
  return self.current.handler
}

let times = 0

const ExpensiveComponent = memo(({ onClick }) => (
  <p onClick={onClick}>I am expensive form component: {times++}</p> ))

const App = () => {
  const [value, setValue] = useState(1);

  const handleClick = useBetterCallback((event, [ value, setValue ]) => {
    setValue( value + 1 )
  }, [value, setValue])

  console.log("Value: " + value)

  return (
    <div className="app">
      <ExpensiveComponent onClick={handleClick} />
      <button onClick={handleClick}>
        I will not trigger expensive re-render
      </button>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

## 复习？

你怎么想呢?