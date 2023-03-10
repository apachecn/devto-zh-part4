# 将 React useState 挂钩与 TypeScript 一起使用

> 原文：<https://dev.to/miloszpp/using-react-usestate-hook-with-typescript-3d3h>

React 挂钩是 React 最近增加的一项功能，它使函数组件具有与类组件几乎相同的功能。大多数情况下，在 TypeScript 中使用 React 挂钩很简单。

然而，在某些情况下，深入理解钩子的类型可能会非常有用。在本文中，我们将关注于`useState`钩子。

我假设你对这个钩子有一个基本的了解。如果不是这样，请先阅读[这个](https://reactjs.org/docs/hooks-state.html)。

## 阅读类型

首先我们来看看`useState`的类型签名。您将看到，不看文档(或实现)，只从类型中可以提取多少信息。

如果你只对实际例子感兴趣，跳到[下一节](#Usage-examples)。

### 超载

```
function useState<S = undefined>(): [S | undefined, Dispatch<SetStateAction<S | undefined>>];
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>]; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`useState`函数有两个*版本*。TypeScript 允许您为一个函数定义多个类型签名，因为在 JavaScript 中，一个函数通常支持不同类型的参数。一个函数的多个类型签名被称为**重载**。

这两种重载都是泛型函数。类型参数`S`表示钩子存储的状态的类型。第二个重载中的类型参数可以从`initialState`中推断出来。然而，在第一个重载中，它默认为`undefined`，除非显式提供类型参数。**如果不将初始状态传递给`useState`，应该显式提供类型参数。**

### `useState`参数

第一个重载不带任何参数——它在您调用`useState`而不提供任何初始状态时使用。

第二个重载接受`initialState`作为参数。它的类型是`S`和`() => S`的联合。为什么要传递一个返回初始状态的函数而不是直接传递初始状态？计算初始状态可能很昂贵。只有在安装组件时才需要它。但是，在函数组件中，它将在每次渲染时计算。**因此，你可以选择传递一个计算初始状态的函数——昂贵的计算将只执行一次，而不是每次渲染。**

### `useState`返回类型

让我们转到返回类型。两种情况下都是一个**元组**。Tuple 就像一个具有特定长度的数组，包含特定类型的元素。

对于第二次重载，返回类型为`[S, Dispatch<SetStateAction<S>>]`。元组的第一个元素具有类型`S`——状态块的类型。它将包含从组件状态中检索的值。

第二个元素的类型是`Dispatch<SetStateAction<S>>`。`Dispatch<A>`被简单地定义为`(value: A) => void`——一个取值不返回值的函数。`SetStateAction<S>`定义为`S | ((prevState: S) => S)`。所以`Dispatch<SetStateAction<S>>`的类型其实就是`(value: S | ((prevState: S) => S)) => void`。它是一个接受状态的更新版本的函数，或者是一个基于先前版本产生更新版本的函数。在这两种情况下，我们可以推断出由`setState`返回的元组的第二个元素是一个函数，我们可以调用它来更新组件的状态。

第一个重载的返回类型是一样的，但是这里用`S | undefined`代替了`S`。如果我们不提供初始状态，它将最初存储`undefined`。这意味着`undefined`必须包含在钩子存储的状态类型中。

## 用法举例

大多数时候你不需要费心为`useState`提供类型参数——编译器会为你推断出正确的类型。然而，在某些情况下，类型推断可能是不够的。

### 空初始状态

第一种情况是你不想给`useState`提供初始状态。

正如我们在类型定义中看到的，无参数的类型实参`S`默认为`undefined`。因此，`pill`的类型应该推断为`undefined`。然而，由于打字稿中的[设计限制，实际上推断为`any`。](https://github.com/microsoft/TypeScript/issues/32003)

同样，`setPill`的类型推断到`React.Dispatch<any>`。这真的很糟糕，因为没有什么能阻止我们用无效的论点来称呼它:`setPill({ hello: 5 })`。

```
export const PillSelector: React.FunctionComponent = () => {
    const [pill, setPill] = useState();
    return (
        <div>
            <button onClick={() => setPill('red')}>Red pill</button>
            <button onClick={() => setPill('blue')}>Blue pill</button>
            <span>You chose {pill} pill!</span>
        </div>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们需要向`setState`传递一个类型参数。在 JSX，我们将`pill`视为文本，因此我们的首选可能是`string`。然而，让我们更精确地将类型限制为只允许我们期望的值。

```
const [pill, setPill] = useState<'red' | 'blue'>(); 
```

Enter fullscreen mode Exit fullscreen mode

注意`pill`的推断类型现在是`"red" | "blue" | undefined`(因为这一段状态最初是空的)。启用了`strictNullChecks`后，TypeScript 不允许我们在`pill` :
上调用任何东西

```
// 🛑 Object is possibly 'undefined'.ts(2532)
<span>You chose {pill.toUpperCase()} pill!</span> 
```

Enter fullscreen mode Exit fullscreen mode

...除非我们先检查值:

```
// ✅ No errors!
{pill && <span>You chose {pill.toUpperCase()} pill!</span>} 
```

Enter fullscreen mode Exit fullscreen mode

### 可清除状态

另一种情况是，当你给`useState`提供一个类型参数的时候，初始状态被定义，但是你想让**稍后清除**这个状态。

```
export const PillSelector: React.FunctionComponent = () => {
    const [pill, setPill] = useState('blue');
    return (<div>
        <button onClick={() => setPill('red')}>Red pill</button>
        <button onClick={() => setPill('blue')}>Blue pill</button>
        // 🛑 Argument of type 'undefined' is not assignable 
        // to parameter of type 'SetStateAction<string>'.
        <button onClick={() => setPill(undefined)}>Reset</button>
        {pill && <span>You chose {pill.toUpperCase()} pill!</span>}
    </div>); } 
```

Enter fullscreen mode Exit fullscreen mode

由于初始状态被传递给`useState`，因此`pill`的类型被推断为`string`。因此，当您试图将`undefined`传递给它时，TypeScript 将出错。

您可以通过提供类型参数来解决这个问题。

```
const [pill, setPill] = useState<'blue' | 'red' | undefined>('blue'); 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

总结一下，我们已经彻底分析了`useState`函数的类型定义。基于这些信息，我们看到了什么时候向`useState`提供类型参数是必要的，什么时候推断的类型是足够的。

我喜欢钩子是从类型定义中读取多少信息的好例子。他们真的展示了静态打字的威力！

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的书！

[高级类型脚本](https://typescriptmasterclass.com)