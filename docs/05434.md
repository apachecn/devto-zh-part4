# 计算值的反应状态挂钩

> 原文：<https://dev.to/elad/a-react-state-hook-for-computed-values-9p>

钩子很棒！

但是我发现自己用`useMemo`写了很多`useState`来计算每个州的字段。

让我们看一个例子。

说我有这个分量:

```
const Component = () => {
  const [num, setNum] = useState(0);

  return (
    <div>
      <button onClick={() => setNum(num + 1)}>Inc</button>
      <button onClick={() => setNum(num - 1)}>Dec</button>
      <span>{num}</span>
    </div>
  );
} 
```

我想显示这个数是不是偶数，以及这个数乘以 10。

所以我来做:

```
const Component = () => {
  const [num, setNum] = useState(0);
  const isEven = useMemo(() => num % 2 === 0, [num]);
  const multiplied = useMemo(() => num * 10, [num]);

  return (
    <div>
      <button onClick={() => setNum(num + 1)}>Inc</button>
      <button onClick={() => setNum(num - 1)}>Dec</button>
      <span>{num}</span>
      <span>isEven: {isEven}</span>
      <span>by 10: {multiplied}</span>
    </div>
  );
} 
```

这还不错，但显然这种情况很少。通常，这些映射函数相加，你的状态会比这更复杂。
你最终会得到很多地图功能，这一切变得非常混乱！

所以我们可以用一个简单的定制钩子来包装它，这个钩子可以得到一个初始值和一个无限制的映射函数列表-

```
function useMappedState(initialState, ...mapFns) {
  const [state, setState] = useState(initialState);

  const memo = useMemo(() => mapFns.map(mapFn => mapFn(state)), [state]);

  return [state, setState, memo];
} 
```

### 打字稿

不过，使用 Typescript 会变得非常复杂，如何获得无限数量的 map 函数，并为每个析构值应用它们的返回类型呢？

当然，您可以为每个添加的 map 函数使用函数重载，但是您最终只能为您的类型工作。(我讨厌为我喜欢的类型工作)

[Typescript Mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)to rescue——让我们迭代每个 map 函数并获得返回类型，我们可以深入研究它，但这是另一篇文章。

这是它看起来的样子

```
// types:  number       Dispatch...      boolean     string
const [    number   ,    setNumber   , [ isEven , numberName ]] = useMappedState(
  initialValue,
  value => value % 2,
  value => value + 'name'
) 
```

我刚刚发表了 [use-mapped-state](https://github.com/EladBezalel/use-mapped-state) ，试试吧，让我听听你的想法！:D

编辑:根据要求，我将创建另一个帖子，更多地讨论 typescript 遇到的问题，我是如何解决它的，以及我对它的了解

又及:原型和名字归功于 [@yoav](https://dev.to/yoav)