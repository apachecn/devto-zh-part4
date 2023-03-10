# 您应该使用状态还是使用证书🤷？真的没关系。3 分钟内解释完毕。

> 原文：<https://dev.to/getd/should-you-usestate-or-usereducer-doesn-t-matter-really-explained-in-3-mins-9dk>

React [文档](https://reactjs.org/docs/hooks-reference.html#usereducer)推荐使用`useReducer`来处理复杂的状态值。但对我来说，它们同样强大。让我告诉你怎么做。

# useReducer 可以代替 useState

首先，更简单的情况:任何`useState`都可以使用`useReducer`来实现。事实上，`useState`吊钩本身就是由一个减速器实现的。

让我们用`useState`创建一个简单的反应状态。该状态包含一个`count`号。

```
type State = { count: number };

const [state, setState] = React.useState<State>({ count: 0 }); 
```

我们可以用`useReducer`重新实现同样的功能。

```
type Action = {
  type: 'COUNT_CHANGED',
  count: number,
};

const reducer: React.Reducer<State, Action> = (
  prevState: State,
  action: Action
): State => {
  switch (action.type) {
    case "COUNT_CHANGED":
      return { ...prevState, count: action.count };
  }
};

const [state, dispatch] = React.useReducer(reducer, { count: 0 }); 
```

除了多行代码之外，它们的功能完全相同。

这里`useReducer`接受两个参数。

*   第一个是`reducer`函数:`(prevState, action) => newState`。当`dispatch`变为`action`时，它更新(还原)先前状态为新状态。
*   第二个是初始状态，与传入`useState`的状态相同。

我们只有一个动作叫做`COUNT_CHANGED`。所以下面两行会触发相同的状态更新:

```
// with useState
setState({ count: 1 });

// with useReducer
dispatch({ type: 'COUNT_CHANGED', count: 1 }); 
```

# useState 也可以代替 useReducer

`useReducer`的一个优势是它处理复杂状态值的能力。让我们在这里创建一个例子。假设我们有一个包含三个输入组件的根级表单组件，我们希望每个输入都处理自己的值。用户界面如下:

```
 <UserForm>
      <FirstNameInput />
      <LastNameInput />
      <AgeInput />
    </UserForm> 
```

我们在下面创建一个缩减器来处理 3 个输入值:

```
// A complex state with user name and age
type UserState = {
  name: {
    first: string,
    last: string,
  },
  age: number,
};

// Three action types to update each state value
type Action =
  | {
      type: "FIRST_NAME_CHANGED";
      first: string;
    }
  | {
      type: "LAST_NAME_CHANGED";
      last: string;
    }
  | {
      type: "AGE_CHANGED";
      age: number;
    };

const reducer: React.Reducer<UserState, Action> = (
  prevState: UserState,
  action: Action
): UserState => {
  switch (action.type) {
    case "FIRST_NAME_CHANGED":
      return { ...prevState, name: { ...prevState.name, first: action.first } };
    case "LAST_NAME_CHANGED":
      return { ...prevState, name: { ...prevState.name, last: action.last } };
    case "AGE_CHANGED":
      return { ...prevState, age: action.age };
  }
}; 
```

现在在我们的`UserForm`组件中使用它。注意`dispatch`被传递到每个`Input`中，因此它们可以触发动作来更新它们自己的字段。

```
const UserForm = () => {
  const [state, dispatch] = React.useReducer(reducer, {
    name: { first: "first", last: "last" },
    age: 40
  });
  return (
    <React.Fragment>
      <FirstNameInput value={state.name.first} dispatch={dispatch} />
      <LastNameInput value={state.name.last} dispatch={dispatch} />
      <AgeInput value={state.age} dispatch={dispatch} />
    </React.Fragment>
  )
} 
```

完成了。这就是`useReducer`如何适用于复杂状态。现在怎么换算成`useState`？

一种简单的方法是向每个`Input`传递一个大的状态对象。我们必须传递整个状态，因为每个`Input`需要知道当前状态的“全貌”,以便正确地构造新的状态。大概如下:

```
// This is a bad example.
const UserForm = () => {
  const [state, setState] = React.useState({
    name: { first: "first", last: "last" },
    age: 40
  });
  return (
    <React.Fragment>
      <FirstNameInput state={state} setState={setState} />
      <LastNameInput state={state} setState={setState} />
      <AgeInput state={state} setState={setState} />
    </React.Fragment>
  )
} 
```

这很糟糕，原因有几个:

1.  没有职责分离:每个`Input`现在需要完全状态作为其工作的道具。使得重构和单元测试更加困难。
2.  性能差:任何状态改变都会触发所有的`Input`重新渲染。

事实上，这些正是 React 团队建议对这种复杂状态使用`useReducer`的原因。

但这并不意味着我们不能使用`useState`来达到同样的结果。它只是需要更多的手工制作。

```
function Counter() {
  const { state, setFirstName, setLastName, setAge } = useComplexState({
    name: { first: "first", last: "last" },
    age: 40
  });
  return (
    <React.Fragment>
      <FirstNameInput value={state.name.first} setFirstName={setFirstName} />
      <LastNameInput value={state.name.last} setLastName={setLastName} />
      <AgeInput value={state.age} setAge={setAge} />
    </React.Fragment>
  )
}

// A custom hook that returns setter functions for each field.
// This is similar to what the reducer above is doing,
// we simply convert each action into its own setter function.
function useComplexState(initialState: UserState): any {
  const [state, setState] = React.useState<UserState>(initialState);
  const setFirstName = first =>
    setState(prevState => ({
      ...prevState,
      name: { ...prevState.name, first }
    }));
  const setLastName = last =>
    setState(prevState => ({
      ...prevState,
      name: { ...prevState.name, last }
    }));
  const setAge = age => setState(prevState => ({ ...prevState, age }));
  return { state, setFirstName, setLastName, setAge };
} 
```

其实我们完全可以只用`useState` :
来改写`useReducer`

```
const useReducerImplementedByUseState = (reducer, initialState) => {
  const [state, setState] = React.useState<State>(initialState);
  const dispatch = (action: Action) => setState(prevState => reducer(prevState, action));
  return [state, dispatch];
};

// above implementation
const [state, dispatch] = useReducerImplementedByUseState(reducer, initialState);
// is same with
const [state, dispatch] = useReducer(reducer, initialState); 
```

总之，

*   对于简单的值状态，做`useState`，因为它使用的行数较少。
*   对于复杂状态，使用你此刻喜欢的任何一个🤪

**在你的项目中，你更喜欢`useState`还是`useReducer`？在❤️** 下面的评论中分享你的想法