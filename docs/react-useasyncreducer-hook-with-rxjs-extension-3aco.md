# React 使用带 RxJs 扩展的 syncReducer 挂钩

> 原文：<https://dev.to/alekniukas/react-useasyncreducer-hook-with-rxjs-extension-3aco>

最近，我们用 hooks 扩展对 React 应用程序进行了一次很棒的增强。自从它们发行以来，我一直很喜欢它们。它们带来了可重用状态的灵活性，我的应用程序看起来更实用，也更容易阅读。

我想向观众展示一个新的钩子，叫做[usesyncreducer](https://github.com/davidalekna/react-components/tree/master/packages/alekna-store)。它使用起来非常简单，就像 Reacts useReducer 一样，可以从 [npm](https://www.npmjs.com/package/@alekna/react-store) 获得。

`npm install @alekna/react-store`

如果你熟悉 Redux 原则，这应该感觉非常直观和直接。[请点击此处直接进入 codesandbox，例如](https://codesandbox.io/s/determined-borg-l7egr)。

使用示例

```
import { useAsyncReducer, ofType } from '@alekna/react-store';

function reducer(state = { count: 0 }, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function App() {
  const [state, dispatch] = useAsyncReducer(reducer)
  ...
} 
```

调度员事件

```
// sync event
dispatch({ type: "decrement" })
// async event
dispatch(async () => {
  await sleep(500); // fake api request helper fn
  return { type: "increment" };
})
// RxJs event with actions$ stream observable
dispatch(actions$ =>
  interval(1000).pipe(
    startWith(0),
    take(5),
    map(() => ({ type: "increment" })),  
    takeUntil(actions$.pipe(ofType("cancel")))
  )
) 
```

希望你喜欢这篇教程。请让我知道你的想法，如果你喜欢它，请分享它！感谢阅读。