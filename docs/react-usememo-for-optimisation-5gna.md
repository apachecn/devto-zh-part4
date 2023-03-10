# React.useMemo()进行优化

> 原文：<https://dev.to/spukas/react-usememo-for-optimisation-5gna>

React.useMemo()是 Hook API 的另一个方法，有助于优化性能。如果依赖关系保持不变，它会为昂贵的计算记忆并返回相同的值。例如，如果您在组件中有一个计算函数，并且该函数接收 props 作为参数来计算值，那么如果您的组件确实更新了，但传递给函数的 props 保持不变，通常您会希望不进行计算。

## 如何使用

该方法具有以下结构`React.useMemo(() => fn, deps)`。它有两个参数:一个内联函数和一个依赖数组。
内联函数，第一个参数，从昂贵的计算中返回值- `() => doHeavyComputation(a, b)`。
第二个参数是一个依赖关系的数组`[a, b]`，当其中一个依赖关系改变时，`useMemo()`会重新计算值，如果没有改变，则返回上次存储的值。

```
const memoisedValue = React.useMemo(() => doHeavyComputation(a, b), [a, b]) 
```

如果您忘记传递依赖项数组，则每次组件呈现时都会计算新值。

同样重要的是要注意，计算功能应该是纯粹的，没有副作用。如果你想改变状态，例如，`doHeavyComputation()`，你应该使用`React.useEffect()`钩子。

## 何时使用

React 文档指出，您应该将它用于昂贵的计算。这里有几个你可能会用到它的例子:

#### 过滤大数组

```
function MyList(list, query) {
  // On every component render it will be refiltered
  const filteredList = filterListByQyery(list, query);

  // Will recalculate only when the list or the query changes
  const memoizedFilteredList = React.useMemo(
    () => filterListByQyery(list, query),
    [list, query],
  );
} 
```

#### 递归函数，像斐波那契数列、阶乘等。

```
function MySequance(number) {
  // Will recalculate only when the number changes
  const memoizedSequance = React.useMemo(() => getFibonacci(number), [number]);
} 
```

#### 避免不必要的子组件渲染

```
function Parent(a, b) {
  // Will skip rerendering if the argument `a` will not change
  const childA = React.useMemo(() => <ChildA item={a} />, [a]); 
  // Will skip rerendering if `b` remains the same. Similar to `React.memo()` method
  const childB = React.useMemo(() => <ChildB item={b} />, [b]); 
  return (
    <>
      {childA}
      {childB}
    </>
  )
} 
```

## 总结起来

每次性能优化都是有代价的。例如，`useMemo`需要更长的时间来挂载，在提供依赖关系时容易出错，并且可能很难为你的同事阅读代码。因此，在将该方法应用于您的代码之前，进行一次性能分析，并确保这是值得的。