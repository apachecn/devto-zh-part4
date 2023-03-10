# MobX 和 React 钩子。开始使用。

> 原文：<https://dev.to/davidyushkov/mobx-and-react-hooks-getting-started-4ff2>

[![](img/0735722c6a21b56997eebb29a8380c1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zz-7FZ-T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4egd8rfjb6mp5usda761.jpg)

我很喜欢使用 [`MobX`](https://mobx.js.org/) ，就像我们用类描述商店一样，用装饰器或 [`decorate`](https://mobx.js.org/refguide/modifiers.html) 函数装饰可观察值、动作和计算值，然后在我需要的任何地方使用组件中的商店，只需将商店注入组件并在我需要的任何地方观察状态。而且用它也有不错的表现。

简单的例子:

```
//Mobx Store
class CounterStore {
  @observable count = 1;

  @action increment() {
    this.count++;
  }

  @action decrement() {
    this.count++;
  }
} 
```

```
//index.js
const stores = {
  // all mobx stores here
}

ReactDOM.render(
  <Provider {...stores} >
    <App/>
  </Provider> ) 
```

```
// RandomComponent.js
class RandomComponent extends React.Component {
  // some stuff
}

export default inject('anyStore')(observer(RandomComponent)); 
```

但是随着 react hooks 的出现，我们有了一个新的库来使用 mobx 和 react - [mobx-react-lite](https://mobx-react.js.org) ，乍一看示例和文档似乎有点[怪异](https://news.ycombinator.com/item?id=19206276)，但是让我们继续讨论文档。我们可以用 [useLocalStore](https://mobx-react.js.org/state-local) 定义可观察的本地商店，用 [useObserver](https://mobx-react.js.org/observer-hook) 观察，那么我们来试试吧。

```
import React from 'react';
import { useLocalStore, useObserver } from 'mobx-react-lite';

const Counter = () => {
  const counter = useLocalStore(() => ({
    count: 0,
    increment() { counter.count++; }
    decrement() { counter.count--; }
  }));

  return useObserver(() => (
    <>
      <h2>Count: {counter.count}<h2>
      <button onClick={counter.increment}>Increment</button>
      <button onClick={counter.decrement}>Decrement</button>
    </>
  ));
} 
```

来自关于`useLocalStore`的文档:

> 返回对象的所有属性将自动变为可观察的，getters 将被转换为计算属性，方法将被绑定到存储并自动应用 mobx[transactions(actions)](https://mobx.js.org/refguide/action.html)。

听起来很酷，没有必要再装饰了，但全球商店，注入和供应商呢？我们应该再一次看一下文件

> 选择命名`useLocalStore`是为了表明 store 是在组件中本地创建的。然而，这并不意味着您不能在组件树中传递这样的存储。事实上，尽管有名字，用`useLocalStore`处理全局状态管理是完全可能的。例如，你可以设置一堆本地商店，将它们组装在一个根对象中，并在 React 上下文的帮助下在应用程序中传递。

它回答了这些问题。

1.  我们可以使用`useLocalStore`来创建全球商店。
2.  我们不需要注入和提供者，而只需要使用[上下文 API](https://reactjs.org/docs/context.html)

#### 实现。

有几个步骤:

1.  创建新的上下文。
2.  使用`useLocalStore`创建 store，并将其作为上下文提供者的值。
3.  用上下文提供者包装目标组件。
4.  使用`useContext`获取上下文。
5.  在渲染中使用`useObserver`。

结果:
[https://codesandbox.io/embed/mobx-react-lite-example-m1cxl](https://codesandbox.io/embed/mobx-react-lite-example-m1cxl)

你可以注意到 React 函数中根据钩子的规则定义的商店

用同样的方法简单列出待办事项:
[https://codesandbox.io/embed/mobx-react-lite-example2-o05cv](https://codesandbox.io/embed/mobx-react-lite-example2-o05cv)

这种方法工作得很好，但在这里你可以看到一个问题——商店不像类 analog 那样描述自己，也没有可选的严格模式，其中只有`mobx actions`可以改变可观察值。

```
configure({ enforceActions: 'observed' }); 
```

我个人认为这种模式很有用，你可以在`Vue`中注意到同样的情况，对于异步操作和其他繁重的事情，只有突变会改变状态，而[动作](https://vuex.vuejs.org/guide/actions.html)会在操作结束时提交突变。

可以用`mobx-react-lite`做类存储和严格模式吗？
当然，`useLocalStore`与类存储的实例几乎相同，
我们可以将类的实例放入提供者的值中。

带类商店的柜台:
[https://codesandbox.io/embed/mobx-react-lite-example3-yjzj1](https://codesandbox.io/embed/mobx-react-lite-example3-yjzj1)

所以它几乎和 [`mobx-react`](https://github.com/mobxjs/mobx-react) 中的方法一样，但是现在我们用`Context Provider`和`useContext`代替了`inject`。

事实上，我们甚至根本不需要使用`Context Provider`，我们可以用`createContext`代替它，用类的新实例存储和导出它，并用`useContext`将上下文注入任何组件。

```
class CounterStore {
  count = 0;
  increment = () => {
    this.count++;
  };
  decrement = () => {
    this.count--;
  };
}

decorate(CounterStore, {
  count: observable,
  increment: action,
  decrement: action
});

export const counterContext = createContext(new CounterStore()); 
```

```
const CountComponent = () => {
  const counterStore = useContext(counterContext);

  return useObserver(() => (
    <>
      <h2>Count {counterStore.count}</h2>
      <button onClick={counterStore.increment}>+</button>
      <button onClick={counterStore.decrement}>-</button>
    </>
  ));
};
//and no need Provider on the upper level 
```

#### 结论

我不认为这是最终的方法，在`react`和`mobx-react-lite`中有很多东西，但主要的仍然是理解它们背后的概念，不仅挂钩新的 api，而且它们还解释了什么是`React`。