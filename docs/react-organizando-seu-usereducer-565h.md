# React: Organizando seu useReducer

> 原文：<https://dev.to/oieduardorabelo/react-organizando-seu-usereducer-565h>

## 小标准造成了很大的差异！

[随着反动胡克斯](https://reactjs.org/docs/hooks-intro.html)的引进，地方和全球的建国变得稍微简单了一点(视乎观点而定，不是吗？)并且所有的状态创建都倾向于“T2”，因为挂接的引用会随每次渲染而更改。

试剂的两种原生选择是美国状态和**用户教育**。

如果你已经走过这片树林一段时间了，你可能听说过“在简单的情况下使用 useState，在复杂的情况下使用 use edu”，或者“啊，但是 useState 在引擎盖下面使用 use edu”，最后，“user edu 是反应堆中的 Redux，我更喜欢 useState”()

意见部分，**美国真的利用了**帽下的用户教育，你可以查阅[github](https://github.com/facebook/react/blob/master/packages/react-reconciler/src/ReactFiberHooks.js#L803:L834)中的试剂协调器代码片段(链接今后可能/应该改变！😆).

我喜欢这两个，但今天，**我们来谈谈 user eduer**。

## 从文献开始

请看[试剂挂钩参考文献](https://reactjs.org/docs/hooks-reference.html#usereducer)，我们有以下例子与**用户教育** :

```
let initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  let [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
} 
```

像这样的小国家，这种结构运行了很长一段时间。

那下一步怎么办？

## 提取股票

像 Redux 一样，**行动创造者**的想法对于**用户教育**是相当有效的。因为我喜欢循序渐进，所以通常我会先隔离动作，然后建立具有关键字(动作名称)和值(传回新状态的函数)的物件。

此函数以参数形式接收当前/上一状态和操作本身。总是返回到一个新的状态。

我们去掉了`if..else`而赞成`if..else`，使阅读更加简单。在这种情况下，我个人的喜好，与其说是失误，不如说是登录那些没有相应减压器的股票。使浏览器中的应用程序和代码之间的迭代更加简单。

到达以下代码:

```
let initialState = {count: 0};
let reducerActions = {
    increment: (state, action) => {
      return {count: state.count + 1};
    }
    decrement: (state, action) => {
      return {count: state.count - 1};
    }
};

function reducer(state, action) {
    let fn = reducerActions[action.type];

    if (fn) {
      return fn(state, action);
    }

    console.log('[WARNING] Action without reducer:', action);
    return state;
}

function Counter() {
  let [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
} 
```

感觉好多了。但是，`reducerActions`中的这些函数需要返回一个新状态，并且手动更新它们的值容易出错！我相信你还记得像`{ ...state, chave: { ...state.chave } }`这样的情景，这已经给我带来了很多噩梦。😣

那么，我们怎样才能改进这一部分呢？

# 具有可变操作的不可变状态

一个我喜欢的图书馆，也荣获 2019 年年[反应公开源码奖](https://osawards.com/react/)和**的奖项**的影响贡献 [JavaScript 公开源码奖](https://osawards.com/javascript/)

有了它，我们可以保证我们减负功能内的所有变化都会返回一个新的状态，而不会使您创建的每一个‘t1’都变得更加复杂。

在将状态作为我们还原功能的参数传递之前，我们先调用“`immer`”并返回为还原功能创建的临时状态。

保留以下代码:

```
import immer from 'immer';

let initialState = {count: 0};
let reducerActions = {
    increment: (state, action) => {
      state.count += 1;
    }
    decrement: (state, action) => {
      state.count -= 1;
    }
};

function reducer(state, action) {
    let fn = reducerActions[action.type];

    if (fn) {
      return immer(state, draftState => fn(draftState, action));
    }

    console.log('[WARNING] Action without reducer:', action);
    return state;
}

function Counter() {
  let [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
} 
```

如您所见，我们现在可以完全安全地使用减速器内的可变操作。确保返回新的不可变/纯状态。

在此文档示例中，所有这些都很酷，但它如何变成更动态的东西，如 API 调用？

# API 调用和有效负载对象

到目前为止，我们还没有采用(状态，动作)还原函数的第二个参数，对象**动作**被遗忘了。但是，在下面的示例中，我们将使用名为“**有效负载**”的额外密钥。

**有效负载**金钥与 Redux 一样，负责传送目前动作所需的资料。我们还将更新我们的还原性功能，使其仅接收有效负载的对象，而不接收**动作**。隔离对任何其他不必要数据类型的访问。

让我们从[【里克】&【莫特】](https://rickandmortyapi.com)的 API 中获取数据，整理出角色名称的列表。

按照上述例子，我们保留如下代码:

```
import immer from "immer";

let initialState = {
  characters: {
    data: null,
    error: null,
    loading: false
  }
};
let reducerActions = {
  fetch_rick_and_morty_pending: (state, payload) => {
    state.characters.loading = true;
    state.characters.error = null;
    state.characters.data = null;
  },
  fetch_rick_and_morty_resolved: (state, payload) => {
    state.characters.loading = false;
    state.characters.error = null;
    state.characters.data = payload.value;
  },
  fetch_rick_and_morty_rejected: (state, payload) => {
    state.characters.loading = false;
    state.characters.error = payload.error;
    state.characters.data = null;
  }
};
let reducer = (state, action) => {
  let fn = reducerActions[action.type];

  if (fn) {
    return immer(state, draftState => fn(draftState, action.payload));
  }

    console.log('[WARNING] Action without reducer:', action);
    return state;
};

function App() {
  let [state, dispatch] = React.useReducer(reducer, initialState);

  React.useEffect(() => {
    let didRun = true;

    async function fetchRickAndMorty() {
      let req = await fetch("https://rickandmortyapi.com/api/character");
      let json = await req.json();
      return json;
    }

    if (state.characters.loading) {
      fetchRickAndMorty()
        .then(data => {
          if (didRun) {
            dispatch({
              type: "fetch_rick_and_morty_resolved",
              payload: { value: data.results }
            });
          }
        })
        .catch(err => {
          if (didRun) {
            dispatch({
              type: "fetch_rick_and_morty_rejected",
              payload: { error: err }
            });
          }
        });
    }

    return () => {
      didRun = false;
    };
  }, [state.characters]);

  let { loading, data, error } = state.characters;

  return (
    <div className="App">
      <button
        type="button"
        onClick={() => dispatch({ type: "fetch_rick_and_morty_pending" })}
      >
        Let's Rick & Morty!
      </button>
      {loading && data === null && <p>Loading characters...</p>}
      {!loading && error !== null && <p>Ooops, something wrong happened!</p>}
      {!loading && data !== null && data.length === 0 && (
        <p>No characters to display.</p>
      )}
      {!loading && data !== null && data.length > 0 && (
        <ul>
          {state.characters.data.map(char => (
            <li key={char.id}>{char.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
} 
```

如您所见，使用切换操作使一切变得更简单，尤其是访问处于该状态的嵌套对象。

状态管理是一个单独的主题，值得您讨论，但我们可以在此看到一些域、命名和操作模式。

您可以在以下位置查看实时示例:

[https://codesandbox.io/s/live-demo-article-usereducer-fyehh](https://codesandbox.io/s/live-demo-article-usereducer-fyehh)

# 完成

反应胡克斯带来了一些便利，但我们还是要小心很多东西，毕竟是 JavaScript！照顾**价值观和参考**如果不习惯我们喜爱的语言，可能会让人头疼。

你有反应的建议吗？或者反应堆，你们用得着吗？在评论中分享！

下次见！👋🎉