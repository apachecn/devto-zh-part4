# 用钩子测试 React 组件的简要指南

> 原文：<https://dev.to/rahmanfadhil/a-brief-guide-to-test-react-components-with-hooks-5hg2>

*阅读原文[此处](http://rahmanfadhil.com/testing-react-hooks)T3】*

测试是每个 web 开发人员构建高质量和稳定的 web 应用程序的基本技能。但是，这也令人生畏，尤其是对于那些刚刚开始 web 开发的人来说。但是事实证明:有了现代的框架和库，测试并不困难。

许多开发人员避免测试他们的应用程序，你也不应该这样做！

## 入门

要开始学习本教程，你需要很好地了解[反应](https://reactjs.org)和[反应钩](https://reactjs.org/docs/hooks-intro.html)(例如`useState`和`useEffect`)的基础知识。

我也准备了这个项目的代码。你可以在这里查看。

## 是基本的

Jest 是一个专注于简单性的 JavaScript 测试框架。它可以与其他 JavaScript 框架一起工作，如 [Angular](https://angular.io) 和 [Vue](https://vuejs.org) ，最重要的是，它可以与 React 一起使用 [`create-react-app`](https://facebook.github.io/create-react-app) 开箱即用！

想知道 jest 的目的是什么，看看下面这个函数。

```
function sum(a, b) {
  return a + b
} 
```

在这个例子中，我们有一个`sum`函数，它只返回两个参数之和。为了测试我们的功能，用 jest 提供的`test`方法定义一个测试套件。然后，我们期望我们的函数返回 2 + 2 的和。

```
test("function should add numbers correctly", () => {
  expect(sum(2, 2)).toBe(4)
}) 
```

当您编写测试时，您经常需要检查一些值是否满足某些条件。简而言之，你使用“匹配器”,你可以通过`expect`函数访问它们来验证不同的东西。

这里有一些测试中经常用到的基本匹配器。

*   [T2`toBe`](#toBe)
*   [T2`toBeTruthy`](#toBeTruthy)
*   [T2`toBeFalsy`](#toBeFalsy)
*   [T2`not`](#not)
*   [T2`toHaveLength`](#toHaveLength)

### 成为

使用`toBe`比较原始值。它调用`Object.is`来比较值，这在某些情况下比`===`严格相等运算符更适合测试。

例如，这个测试将验证`user`对象中的一些属性:

```
const user = {
  username: "johndoe",
  age: 24,
}

test("is 24 years old", () => {
  expect(can.age).toBe(24)
})

test("has a username", () => {
  expect(can.name).toBe("johndoe")
}) 
```

### 去伪存真

无论值是什么，只要检查值是否为真，就使用`toBeTruthy`。

```
const user = {
  username: "johndoe",
  age: 24,
  job: null,
  isOld: false,
}

test("property is truthy", () => {
  expect(can.username).toBeTruthy()
  expect(can.age).toBeTruthy()

  // This will throw error
  expect(can.job).toBeTruthy()
  expect(can.isOldEnough).toBeTruthy()
}) 
```

### toBeFalsy

与`toBeTruthy`正好相反。

```
const user = {
  username: "johndoe",
  age: 24,
  job: null,
  isOld: false,
}

test("the property is falsy", () => {
  expect(can.job).toBeFalsy()
  expect(can.isOldEnough).toBeFalsy()

  // This will throw error
  expect(can.username).toBeFalsy()
  expect(can.age).toBeFalsy()
}) 
```

### 不

使用`not`来期待给定匹配器的相反结果。

```
const user = {
  username: "johndoe",
  age: 24,
  job: null,
  isOld: false,
}

test("property is truthy", () => {
  expect(can.username).toBeTruthy()
  expect(can.age).toBeTruthy()

  expect(can.job).not.toBeTruthy()
  expect(can.isOldEnough).not.toBeTruthy()
}) 
```

### 到 HaveLength

使用`toHaveLength`检查具有`length`属性(如数组、对象和字符串)并被设置为某个数字的对象。

```
const numbers = [1, 2, 3]
const greeting = "Hello world!"
const user = { firstName: "John", lastName: "Doe" }

expect(numbers).toHaveLength(3)
expect(greeting).toHaveLength(12)
expect(user).toHaveLength(2) 
```

## React 测试库

我们在这个项目中使用的另一个工具是 [React 测试库](https://testing-library.com/docs/react-testing-library/intro)。这是一个简单而完整的 React 测试库，它鼓励我们用良好的测试实践来编写测试。如果你以前已经熟悉反应测试，你应该听说过[酶](https://airbnb.io/enzyme)。这和我们现在使用的非常相似。

我发现 React 测试库提供了一些非常有用的方法，我们将在本教程中介绍这些方法。

*   [T2`render`](#render)
*   [T2`getByTestId`](#getByTestId)
*   [T2`fireEvent`](#fireEvent)
*   [T2`cleanup`](#cleanup)

这里有一些使用 React 测试库和 Jest 的快速指南。

### 渲染

将组件渲染到附加到`document.body`的容器中。

```
import { render } from "react-testing-library"

const { container } = render(<div />) 
```

### getByTestId

在容器中查找要测试的元素/组件。

`MyButton`组件:

```
<button data-testid="my-button">Click Me!</button> 
```

我们的测试将是:

```
const { getByTestId } = render(<MyButton />)

const myButton = getByTestId("my-button")
expect(myButton.text).toBe("Click Me!") 
```

### 火灾事件

一种触发 DOM 事件的方法。

```
// Fire a click event
fireEvent.click(getByTestId("my-button"))

// Fire a keyboard event
fireEvent.keyDown(getByTestId("my-input"), { key: "Enter", code: 13 }) 
```

### 清理

卸载使用[渲染](#render)挂载的 React 树。

```
test("renders into document", () => {
  render(<MyComponent />)
  // ...
  cleanup()
}) 
```

每次测试后自动运行`cleanup`。

```
afterEach(cleanup)

test("renders into document", () => {
  render(<MyComponent />)
  // ...
}) 
```

## 测试组件

下面是一个使用钩子的组件的例子。

```
// App.js

import React, { useState } from "react"

function App() {
  const [counter, setCounter] = useState(0)

  return (
    <div>
      <h1>Counter: {counter}</h1>
      <button onClick={() => setCounter(counter + 1)}>Increase</button>
    </div>
  )
} 
```

这里没有什么特别的，只是一个计数器组件，带有一个`h1`标签和一个`button`来增加计数器。那么，如何测试呢？

我们希望我们的“增加”按钮会在我们点击它的时候改变`h1`标签中的值。所以，我们需要通过使用 React 测试库提供的`getByTestId`方法来获取`button`和`h1`元素。为了让它发生，我们需要在两个组件中添加`data-testid`属性。

```
<div>
  <h1 data-testid="counter">Counter: {counter}</h1>
  <button data-testid="increase-btn" onClick={() => setCounter(counter + 1)}>
    Increase
  </button>
</div> 
```

是时候写一些测试了。首先，我们需要导入组件并呈现它。此外，不要忘记在每次测试后进行*清理*。

```
import { render, cleanup, fireEvent } from "react-testing-library"
import App from "./App.js"

afterEach(cleanup)

test("should increase counter when the button clicked", () => {
  const component = render(<App />)
}) 
```

其次，使用`getByTestId`方法获取组件中的元素。

```
import { render, cleanup, fireEvent } from "react-testing-library"
import App from "./App.js"

afterEach(cleanup)

test("should increase counter when the button clicked", () => {
  const { getByTestId } = render(<App />)

  const counter = getByTestId("counter")
  const increaseBtn = getByTestId("increase-btn")
}) 
```

最后，我们期望我们的`counter`元素文本默认为“Counter: 0”(因为我们的初始状态是 0)。然后，向`increaseBtn`元素触发一个 click 事件，并期望`counter`文本增加到“计数器:1”。

```
import { render, cleanup, fireEvent } from "react-testing-library"
import App from "./App.js"

afterEach(cleanup)

test("should increase counter when the button clicked", () => {
  const { getByTestId } = render(<App />)

  const counter = getByTestId("counter")
  const increaseBtn = getByTestId("increase-btn")

  expect(counter.textContent).toBe("Counter: 0")
  fireEvent.click(increaseBtn)
  expect(counter.textContent).toBe("Counter: 1")
}) 
```

通过这三个简单的步骤，您现在可以测试使用 React 钩子的组件了。