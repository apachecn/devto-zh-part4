# 使用 React 上下文防止额外重新呈现的 4 个选项

> 原文：<https://dev.to/dai_shi/4-options-to-prevent-extra-rerenders-with-react-context-4e5c>

##### 你怎么喜欢被追踪的反应

### 简介

React context 和 useContext 非常方便。在开发一个小应用程序时，使用它不会有任何问题。如果您的应用程序变得非常大，您可能会遇到一些与 useContext 相关的性能问题。这是因为只要上下文值发生更改，useContext 就会触发 rerender。即使渲染中没有使用该值的一部分，也会发生这种情况。这是故意的。如果 useContext 有条件地触发重新呈现器，那么挂钩将变得不可组合。

有过几次讨论，特别是在[这一期](https://github.com/facebook/react/issues/14110)。目前，React core 还没有直接的解决方案。在[本期](https://github.com/facebook/react/issues/15156#issuecomment-474590693)中描述了三种选择。

这篇文章展示了这三个选项的一个例子，以及另一个名为 [react-tracked](https://github.com/dai-shi/react-tracked) 的库选项。

### 基数举例

让我们举一个最小的例子:一个带有`firstName`和`familyName`的 person 对象。

```
const initialState = {
  firstName: 'Harry',
  familyName: 'Potter',
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们定义一个归约器来馈入 useReducer。

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'setFirstName':
      return { ...state, firstName: action.firstName };
    case 'setFamilyName':
      return { ...state, familyName: action.familyName };
    default:
      throw new Error('unexpected action type');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们的上下文提供者如下所示。

```
const NaiveContext = () => {
  const value = useReducer(reducer, initialState);
  return (
    <PersonContext.Provider value={value}>
      <PersonFirstName />
      <PersonFamilyName />
    </PersonContext.Provider>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

`PersonFirstName`是这样实现的。

```
const PersonFirstName = () => {
  const [state, dispatch] = useContext(PersonContext);
  return (
    <div>
      First Name:
      <input
        value={state.firstName}
        onChange={(event) => {
          dispatch({ type: 'setFirstName', firstName: event.target.value });
        }}
      />
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

类似于此，`PersonFamilyName`实现。

因此，如果`familyName`发生变化，`PersonFirstName`将重新呈现，产生与之前相同的输出。因为用户不会注意到变化，这不会是一个大问题。但是，当要重新渲染的组件数量很大时，速度可能会变慢。

现在，如何解决这个问题？这里有 4 个选项。

### 选项 1:拆分上下文

最可取的选择是拆分上下文。在我们的例子中，它将是这样的。

```
const initialState1 = {
  firstName: 'Harry',
};

const initialState2 = {
  familyName: 'Potter',
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了两个归约器并使用了两个上下文。如果这在你的应用中有意义，在习惯用法 React 中它总是被推荐。但如果需要让它们保持单一状态，就不能采取这个选项。我们的例子可能是这样，因为它意味着是一个人的对象。

### 选项二:React.memo

第二种选择是使用 React.memo，我觉得这也是惯用的。

我们不改变基本示例中的上下文。`PersonFirstName`由两个组件重新实现。

```
const InnerPersonFirstName = React.memo(({ firstName, dispatch }) => (
  <div>
    First Name:
    <input
      value={firstName}
      onChange={(event) => {
        dispatch({ type: 'setFirstName', firstName: event.target.value });
      }}
    />
  </div>
);

const PersonFirstName = () => {
  const [state, dispatch] = useContext(PersonContext);
  return <InnerPersonFirstName firstName={state.firstName} dispatch={dispatch} />;
}; 
```

Enter fullscreen mode Exit fullscreen mode

当 person 对象中的`familyName`改变时，`PersonFirstName`重新渲染。但是，`InnerPersonFirstName`没有重新招标，因为`firstName`没有改变。

所有复杂的逻辑都被移到了`InnerPersonFirstName`中，而`PersonFirstName`通常是轻量级的。因此，对于这种模式，性能不是问题。

### 选项 3:使用备忘录

如果 React.memo 没有像你预期的那样工作，你可以使用 memo 作为第三个选项。我个人不会推荐这个。可能会有一些限制。比如不能用钩子。

用 useMemo 看起来是这样的。

```
const PersonFirstName = () => {
  const [state, dispatch] = useContext(PersonContext);
  const { firstName } = state;
  return useMemo(() => {
    return (
      <div>
        First Name:
        <input
          value={firstName}
          onChange={(event) => {
            dispatch({ type: 'setFirstName', firstName: event.target.value });
          }}
        />
      </div>
    );
  }, [firstName, dispatch]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 选项 4:反应-跟踪

第四个选择是使用图书馆。

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

有了这个库，我们的提供者看起来会有点不同，如下所示。

```
const { Provider, useTracked } = createContainer(() => useReducer(reducer, initialState));

const ReactTracked = () => {
  return (
    <Provider>
      <PersonFirstName />
      <PersonFamilyName />
    </Provider>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

`PersonFirstName`是这样实现的。

```
const PersonFirstName = () => {
  const [state, dispatch] = useTracked();
  return (
    <div>
      First Name:
      <input
        value={state.firstName}
        onChange={(event) => {
          dispatch({ type: 'setFirstName', firstName: event.target.value });
        }}
      />
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

请注意基础示例的变化。只是换了一句台词。

```
-  const [state, dispatch] = useContext(PersonContext); +  const [state, dispatch] = useTracked(); 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的？由`useTracked()`返回的状态被代理包装，并且它的使用被跟踪。这意味着钩子知道在 render 中只使用了`firstName`属性。这允许仅在使用的属性更改时触发 rerender。这种毫不费力的优化就是我所说的“状态使用跟踪”

### 什么是状态使用追踪

更多信息，请访问我的其他博客帖子。例如:

什么是状态使用跟踪？用 React 钩子和代理实现直观和高性能全局状态的新方法

还有一个博客帖子列表。

### 完整示例演示

[codesandbox](https://codesandbox.io/embed/github/dai-shi/react-tracked/tree/master/examples/08_comparison)

[回购中的源代码](https://github.com/dai-shi/react-tracked/tree/master/examples/08_comparison)

### 期末备注

如果你已经看过我以前的一些博客文章，那么这篇文章不会有新的发现。

我想从别人那里学习更多的编码模式。请让我知道它在你的用例中会是什么样子。

* * *

最初发表于 2019 年 8 月 21 日[https://blog.axlight.com](https://blog.axlight.com/posts/4-options-to-prevent-extra-rerenders-with-react-context/)。