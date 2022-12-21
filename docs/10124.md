# 避免在 React 中对函数组件进行不必要的渲染

> 原文：<https://dev.to/spukas/avoid-unnecessary-rendering-for-function-components-in-react-m63>

随着 React 钩子的引入，我们更多地使用功能组件，你可能已经注意到它们一直在渲染，而不关心道具是否保持不变。例如，如果您有一个很大的待办事项列表，并且您正在向列表中添加一个新项目，所有项目组件都将重新呈现，即使它们返回相同的结果。
类组件有额外的方法来防止不必要的渲染:`PureComponent`和`shouldComponentUpdate`。前一种比较好用；用户不需要编写任何额外的逻辑；如果组件检测到新的属性，它会简单地检查属性和重新呈现器。后一种方法是生命周期的，用户需要通过手动比较属性或状态来编写逻辑，并告诉组件它是否必须呈现。
函数组件也有避免不必要渲染的方法，只是语法有点不同。React.memo() -做组件记忆，兑现结果，如果属性没有改变就重用它。

## React.memo()

一个高阶组件，它将记忆你的组件，非常类似于`PureComponent`。它将简单地比较组件的当前和新道具，如果没有任何变化，React 将跳过该组件的渲染。为了帮助方法尽可能有效，使 prop 结构成为原始值或简单对象。如果您有一个复杂的对象，比较可能会失败，组件将不会被记忆，并会再次渲染。
使用非常简单:

```
function List() {
  return ['one', 'two'].map((item) => (
    <MemoizedListItem item={item} />
  );
}

// Memoize ListItem component
const MemoizedListItem = React.memo(function ListItem({ item }) {
  return <li>{item}</li> }) 
```

在这种情况下，如果向列表中添加一个新项，那么只有包含新项的组件会呈现到 DOM 中，前两个组件将使用上次呈现的结果。

如果 prop 结构更复杂，需要手动比较，可以将一个附加函数传递给`React.memo()`作为第二个参数。该函数将接收两个参数`prevProps`、`nextProps`，如果属性相同，它必须返回一个布尔值。与`shouldComponentUpdate`相反的返回语句。

```
const ListItem = React.memo(
  function ListItem({ item }) { ... }, // first argument
  function arePropsEqual(prevProps, nextProps) { // second argument
    return prevProps.item === nextProps.item;
  }
); 
```

## 结论

`React.memo()`是一种性能优化的方法，先确定自己是否需要。对你的应用程序进行剖析，测量性能，并在必要时应用该方法。错误的应用程序可能会导致错误。