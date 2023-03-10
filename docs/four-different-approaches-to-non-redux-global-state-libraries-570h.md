# 非冗余全局状态库的四种不同方法

> 原文：<https://dev.to/dai_shi/four-different-approaches-to-non-redux-global-state-libraries-570h>

##### 从消费角度

### 简介

自从 React hooks 登陆以来，已经有很多针对全局状态的库被提出。其中一些是围绕上下文的简单包装器。然而，其中一些是功能齐全的状态管理系统。

从技术上讲，有几种存储状态和通知更改的实现方式。本帖我们不去细说，只注意两个轴。

1.  无论是基于上下文还是外部存储
2.  无论是基于订阅还是上下文传播

在这篇文章中，我们关注的是消费者端钩子的 API 设计。根据我的观察，API 设计有四种方法。让我们通过伪代码的例子来看看每种方法。举个简单的例子，我们假设一个应用程序有以下内容。

*   两个全局计数器，
*   两个计数器组件，以及
*   增加两个计数器的动作。

注意，在提供者端，它是与实现无关的。因此，`<Provider>`不一定意味着对上下文做出反应。

### 方法一:多重语境

```
const App = () => (
  <Counter1Provider initialState={0}>
    <Counter2Provider initialState={0}>
      <Counter1 />
      <Counter2 />
    </Counter2Provider>
  </Counter1Provider>
);

const Counter1 = () => {
  const [count1, dispatch1] = useCounter1();
  const [, dispatch2] = useCounter2();
  const incrementBoth = () => {
    dispatch1({ type: 'increment' });
    dispatch2({ type: 'increment' });
  };
  return (
    <div>
      <div>Count1: {count1}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
};

const Counter2 = () => {
  const [, dispatch1] = useCounter1();
  const [count2, dispatch2] = useCounter2();
  const incrementBoth = () => {
    dispatch1({ type: 'increment' });
    dispatch2({ type: 'increment' });
  };
  return (
    <div>
      <div>Count2: {count2}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

这种方法可能是最惯用的。用 React context 和 useContext 可以很容易地实现这种方法。

使用这种方法的库: [constate](https://github.com/diegohaz/constate) 和[stated-next](https://github.com/jamiebuilds/unstated-next)

### 方法二:按属性名(或路径)选择

```
const App = () => (
  <Provider initialState={{ count1: 0, count2: 0 }}>
    <Counter1 />
    <Counter2 />
  </Provider>
);

const Counter1 = () => {
  const count1 = useGlobalState('count1');
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count1: {count1}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
};

const Counter2 = () => {
  const count2 = useGlobalState('count2');
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count2: {count2}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

这种方法是将更多的价值放在一个商店中。单个存储允许分派一个动作来改变多个值。您可以指定一个属性名来获取相应的值。通过名称来指定很简单，但是在复杂的情况下有点受限。

采用这种方法的库: [react-hooks-global-state](https://github.com/dai-shi/react-hooks-global-state) 和 [shareon](https://github.com/storeon/storeon)

### 方式 3:通过选择器功能选择

```
const App = () => (
  <Provider initialState={{ count1: 0, count2: 0 }}>
    <Counter1 />
    <Counter2 />
  </Provider>
);

const Counter1 = () => {
  const count1 = useSelector(state => state.count1); // changed
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count1: {count1}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
};

const Counter2 = () => {
  const count2 = useSelector(state => state.count2); // changed
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count2: {count2}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

与前面的代码相比，只有两行发生了变化。选择器函数比属性名更灵活。如此灵活以至于它可能会像进行昂贵的计算一样被滥用。最重要的是，性能优化通常需要保持对象引用相等。

采用这种方法的库: [zustand](https://github.com/react-spring/zustand) 和 [react-sweet-state](https://github.com/atlassian/react-sweet-state)

### 方法 4:状态使用追踪

```
const App = () => (
  <Provider initialState={{ count1: 0, count2: 0 }}>
    <Counter1 />
    <Counter2 />
  </Provider>
);

const Counter1 = () => {
  const state = useTrackedState();
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count1: {state.count1}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
};

const Counter2 = () => {
  const state = useTrackedState();
  const dispatch = useDispatch();
  const incrementBoth = () => {
    dispatch({ type: 'incrementBoth' });
  };
  return (
    <div>
      <div>Count2: {state.count2}</div>
      <button onClick={incrementBoth}>Increment both</button>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意`state`部分与之前的代码有所不同。`dispatch`部分不变。这种方法消除了选择器功能，而且很难被误用。一个大问题是性能优化。这超出了本文的范围，但根据一些基准测试，它实际上已经相当不错了。如果你有兴趣，请查看[的另一个帖子](https://blog.axlight.com/posts/benchmark-react-tracked/)。

采用这种方法的库: [react-tracked](https://github.com/dai-shi/react-tracked)

### 期末备注

这个例子可能太假了，但我希望它解释了不同之处。就个人而言，我会根据案例及其需求使用任何方法。

最后，这篇文章的第二个目的是让读者了解最后一种方法，“状态使用跟踪”我希望你得到它。

* * *

最初发表于 2019 年 7 月 6 日[https://blog.axlight.com](https://blog.axlight.com/posts/four-different-approaches-to-non-redux-global-state-libraries/)。