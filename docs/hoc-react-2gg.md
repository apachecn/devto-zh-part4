# 特设反应

> 原文：<https://dev.to/fosteman/hoc-react-2gg>

这是我在 dev.to
上的第一篇帖子，热情地向大家问好！

有句老话叫“不要重复自己”。如果应用于 React code base——一个涉及高阶函数(HOF)的有价值的挑战。

在深入研究代码之前，
举几个例子:

#### [`Array.map`T4】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

```
[1, 2].map(el => el = el + 1);
// → [2, 3] 
```

#### [`Array​.prototype​.for​Each()`T4】](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

```
["A", "B"].forEach(l => console.log(l));
// → A
// → B 
```

#### `Typical Math Abstraction`

```
const multiply = (x) => (y) => x * y
multiply(1)(2) 
// -> 2 
```

#### *`Wild Caught oneliner`T4】*

##### 功能组成

```
const composition = (...callbacks) => callbacks.reduce((a,b) => (...args) => a(b(...args))); 
```

解码为:

```
const compose = (...callbacks) => callbacks.reduce((a, b) => (...args) => a(b(...args)));
    const addTwo = x => x + 2;
    const double = x => x * 2;
    const square = x => x * x;
    const fn = compose(addTwo, double, square);
    console.log(fn(1)); // -> 4 addTwo(double(square(1))) 
```

### HOC 在作出反应...

##### HOC 是一种从 React 的组合性质中出现的模式。

注意，HOC 是 React 中使用的高级**模式**，而不是 API 的一部分。您可以使用它们将功能抽象出来，但将其作为多个组件的可选功能重用。高阶组件将组件和可选配置作为输入，并返回组件的增强版本。它建立在 JavaScript 中高阶函数的原则之上:返回函数的函数。

HOC 在后面很重要，因为在使用 Redux 时你会遇到它们。(在 [react-redux](https://github.com/reduxjs/react-redux) 中连接 HOC)。
这些是抽象给定 React 组件的方法，实现逻辑并返回一个新增加的组件

原型是这样的:

```
const AugmentedComponent = HOC(OriginalComponent); 
```

为了进行演示，下面的函数返回组件`<ReversedNameComponent>`，并反转最初传递的`<OriginalNameComponent>`的内部文本:

```
const reverse = ChildComponent =>
  ({ children, ...props }) =>
    <ChildComponent {...props}>
      {children.split("").reverse().join("")}
    </ChildComponent> 
const OriginalNameComponent = props => <span>{props.children}</span> 
const ReversedNameComponent = reverse(OriginalNameComponent) 
```

接收到`ChildComponent`，*在字符数组上拆分*字符串，*反转*顺序，*将*加入新字符串，将结果返回到组件的`innerText`

感谢阅读！