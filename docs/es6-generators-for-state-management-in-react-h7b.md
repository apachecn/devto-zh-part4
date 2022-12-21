# React 中用于状态管理的 ES6 生成器

> 原文：<https://dev.to/beccaliz/es6-generators-for-state-management-in-react-h7b>

当我们谈论 JavaScript 中的状态管理时，我们通常谈论的是使用特定于框架的库或工具，如 React 中的 redux 或`setState`。但是当我们在寻找状态管理策略时，我认为 ES6 中有强大的工具可供我们使用。如果我们正在实现顺序状态更新(多步表单、传送带、动画等)，一个这样的工具就是生成器。

## 什么是发电机？

生成器是实现[迭代器协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Iterators)的特殊 JavaScript 函数。如果您以前从未使用过，请耐心等待，因为第一次使用它们需要一定的学习过程。

如果你已经熟悉迭代器，请随意跳过，如果不熟悉，下面是我的十秒钟介绍。

### 迭代器简介

对于实现迭代器协议的函数，它必须返回一个带有`next`函数的对象。这个`next`函数返回一个具有属性`value`和`done`的对象。

```
const myIterator = createIterator();
const { value, done } = myIterator.next(); 
```

我们可以像这样创建自己的迭代器函数。

```
function createColorIterator() {
  let i = 0;
  const colors = ["red", "yellow", "blue"];
  return {
    next: () => {
      if (i < colors.length) {
        let value = colors[i];
        i++;
        return {
          value: value,
          done: false
        };
      } else {
        return {
          value: undefined,
          done: true
        };
      }
    }
  };
}

let iterator = createColorIterator();

console.log(iterator.next());
// { value: "red", done: false }
console.log(iterator.next());
// { value: "yellow", done: false }
console.log(iterator.next());
// { value: "blue", done: false }
console.log(iterator.next());
// { value: undefined, done: true } 
```

这个迭代器使用`createColorIterator`函数的局部范围来跟踪自己的状态。在这种情况下，我们可以改变`i`的值，它将在迭代器中保持不变。更多信息，你可以在这里阅读关于闭包和词法范围的。

另外，我应该注意 JS 中的任何 iterables(数组、字符串、映射、集合等。)有一个名为`Symbol.iterator`的属性返回一个迭代器。

```
const colors = ["red", "yellow", "blue"];
const iterator = colors[Symbol.iterator]();

console.log(iterator.next());
// { value: "red", done: false }
// ...same as above 
```

### 回到发电机…

所以，迭代器很棒！但是从头开始构建一个可能意味着要写很多样板文件。这就是发电机的用武之地！生成器是特殊的函数，为你创建迭代器做一些 ES6 魔术。生成器对于异步编程非常有帮助，尽管我在这里并不打算深入探讨。

例如，我现在可以使用`function*`语法用更少的代码重写我的迭代器。

```
function* createColorIterator() {
  let i = 0;
  const colors = ["red", "yellow", "blue"];
  while (i < colors.length) {
    const color = colors[i];
    i++;
    yield color;
  }
}

console.log(iterator.next());
// { value: "red", done: false }
// ...same as above 
```

注意这个生成器函数是如何使用关键字`yield`的。当生成器遇到这个关键字时，它会立即退出函数，并返回`yield`之后的值。当再次调用`next`时，功能执行可以恢复。

## 如何在我的 React 应用程序中使用生成器来存储状态？

发电机为我们打开了许多可能性！现在，让我们继续这个简单的颜色例子。

在我的 React 组件中，我需要创建一个状态变量来存储生成器的当前状态。这主要是为了在每次状态更新时触发重新呈现，因为我是基于当前迭代器状态来呈现视图组件的。

```
let colors = createColorIterator();
let initialState = colors.next();

function App() {
  const [colorState, setColorState] = React.useState(initialState);

  function updateBackgroundColor() {
    setColorState(colors.next());
  }

  function reset() {
    colors = createColorIterator();
    setColorState(initialState);
  }

  const { value, done } = colorState;

  return (
    <Container backgroundColor={value}>
      <h1>Hello!</h1>
      <Button disabled={done} onClick={updateBackgroundColor}>
        Change background color
      </Button>
      <Button onClick={reset}>Reset</Button>
    </Container>
  );
} 
```

请注意我是如何在组件范围之外定义迭代器和初始状态值的，以避免每次重新呈现时重置该状态。

[颜色生成器示例- CodeSandbox](https://codesandbox.io/s/colors-generator-example-kpp9s)

## 为什么我可以使用发电机进行状态管理？

对于大多数用例，我实际上不建议使用生成器而不是更传统的状态管理策略。因为生成器不是一个纯粹的函数(它的值在每次被调用时都会改变，即使是相同的参数)，所以我们不能将它与 redux 或`useReducer`等功能性更强的状态管理策略结合使用。

然而，我认为在创建增量状态更新、独立于组件呈现测试状态(但也集成测试您的组件)以及在框架间共享代码方面有很大的潜力。我这样做的目的不是为了这篇文章，但是在 Angular 或 Vue 中实现相同的逻辑而不改变核心逻辑是非常琐碎的。