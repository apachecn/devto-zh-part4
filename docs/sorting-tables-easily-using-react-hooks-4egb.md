# 使用 React 挂钩轻松排序表格

> 原文：<https://dev.to/johnridesabike/sorting-tables-easily-using-react-hooks-4egb>

在构建我的定制国际象棋锦标赛管理器( [Github link](https://github.com/johnridesabike/coronate) )时，我经常发现自己在处理表格数据。一个表格应该是这样的:

```
const players = [
    {id: 0, name: "Joel", rating: 1216},
    {id: 1, name: "Crow", rating: 1153},
    ... // and so on
]; 
```

我有用户表，锦标赛表，比赛表，各种各样的表。每一种都有自己的特性。一旦表格达到一定的大小，我就需要一种方法来对它们进行排序。谢天谢地，React 使这变得非常简单。

### 快速笔记

对于我的项目，在这些例子中，我使用了 [Ramda](https://ramdajs.com/) 实用程序库。如果您不习惯 Ramda 的函数式编程风格，这些代码中的一些乍一看可能会很奇怪。忍耐一下，一切都会好的😉。

## 设置状态

为了管理排序后的表的状态，我们需要三个变量:排序键、排序方向和表本身。键只是一个表示对象属性的字符串。方向是二进制的，可以是升序也可以是降序，所以可以存储为布尔值。我们的状态将会是这样:

```
{key: "name", isDescending: false, table: [...]} 
```

您可以很容易地用`isAscending`来代替 direction 属性，但是这是您必须做出的实现决定。最后，你选择哪一个并不重要。

## 编写减速器

现在我们知道了状态对象的样子，我们需要写一个 reducer 函数来更新它。下面是我想到的一个:

```
import {ascend, descend, prop, sort} from "ramda";

function sortedTableReducer(oldState, newState) {
    const {isDescending, key, table} = {...oldState, ...newState};
    const direction = isDescending ? descend : ascend;
    const sortFunc = sort(direction(prop(key)));
    return {isDescending, key, table: sortFunc(table)};
} 
```

在这四行中发生了很多事情，所以让我们来分解一下:

### 1。更新状态

```
const {isDescending, key, table} = {...oldState, ...newState}; 
```

第一行将旧状态与新状态合并，然后将结果析构为我们需要的三个变量。`newState`参数可能是`{key: "rating"}`、`{isDescending: true}`、全新的`table`，或者它们的任意组合。任何未指定的属性将保持原始状态不变。

### 2。确定排序方向

```
const direction = isDescending ? descend : ascend; 
```

Ramda 有两个函数，`ascend`和`descend`，可以创建新的函数进行排序比较。在这里，我们只是简单地确定我们想要哪个函数。

### 3。创建排序函数

```
const sortFunc = sort(direction(prop(key))); 
```

这就是 Ramda 的一大特性发挥作用的地方:部分应用。`sort`、`direction`和`prop`都是函数，但是我们只是部分地应用它们来生成新函数(也称为 currying)。

让我们来分解一下:

`prop`从指定的对象中检索指定的属性。来自 Ramda 的文档:`prop('x', {x: 100}); //=> 100 `。因为我们只给*提供了一个*参数，`prop`只返回一个新函数，它的行为和已经应用了第一个参数的`prop`一样。`prop(key)`是一种更简洁的写法`(x) => prop(key, x)`，或者`prop.bind(null, key)`。

我们已经知道，`direction`不是`ascend`就是`descend`。例如，通过调用`ascend(prop("rating"))`，我们创建了一个新函数，它将根据两个对象的`rating`属性来比较它们，并返回一个布尔值来指示哪一个应该在另一个之前。

最后，`sort`类似于 JavaScript 的内置`Array.prototype.sort`。它有两个参数:一个比较函数和一个数组。我们已经创建了比较器函数，所以它作为第一个参数被传递。

通过将数组参数留空，我们再次利用了 Ramda 的奉承。返回一个新函数，它将根据我们已经提供的函数对任何数组进行排序。

这可能看起来很难理解，但这正是 Ramda(以及类似的库)的魅力所在。你可以将大量的逻辑打包成几行代码。

### 4。返回更新后的状态

```
return {isDescending, key, table: sortFunc(table)}; 
```

最后，我们可以返回更新后的对象，其中的表根据我们的排序函数进行了排序。

## 使用减速器

在你的组件内部，你可以使用减速器，嗯，`useReducer` :

```
const initialState = {key: "name", isDescending: false, table: players}
const [sortedPlayers, sortedDispatch] = useReducer(sortedTableReducer, initialState); 
```

您可以将 JSX 内排序后的表格输出为:

```
{sortedPlayers.table.map((player) => ...)} 
```

您可以更新排序关键字:

```
sortedDispatch({key: "name"}); 
```

您可以切换排序顺序:

```
sortedDispatch({isDescending: !sortedPlayers.isDescending}); 
```

并且可以更新数据:

```
const newPlayers = players.concat([{id: 3, name: "Tom", rating: 2500}]);
sortedDispatch({table: newPlayers}); 
```

然而，我注意到了一个缺陷。我不能保证表格数据最初会被排序(事实上，通常不会)。有几种方法可以弥补这一点。一种方法是从 reducer 中提取排序函数，并在将它传递给`useReducer`之前在您的数组中调用它。该策略的一个缺点是，该函数将在每次重新渲染时对初始数据进行排序。如果排序开销很大，并且组件频繁呈现，这可能会降低性能。

如果你的直觉是用`useMemo`来记忆初始数据，有一个更简单的解决方案。React 的`useEffect`钩子优雅地为我们解决了这个问题，我们甚至不需要提取排序函数:

```
useEffect(
    function callDispatchOnceToTriggerInitialSort() {
        sortedDispatch({});
    },
    [] // <-- This ensures the effect only fires once
); 
```

传递给分派的空对象不会改变状态，因此分派将只对初始数据进行排序。

## 提取逻辑到一个可重用的钩子

因为我们想在多个组件中使用我们的代码，所以让我们提取它。下面是最终结果:

```
import {useEffect, useReducer} from "react";
import {ascend, descend, prop, sort} from "ramda";

function sortedTableReducer(oldState, newState) {
    const {isDescending, key, table} = {...oldState, ...newState};
    const direction = isDescending ? descend : ascend;
    const sortFunc = sort(direction(prop(key)));
    return {isDescending, key, table: sortFunc(table)};
}

function useSortedTable(table, key, isDescending = true) {
    const initialState = {isDescending, key, table};
    const [state, dispatch] = useReducer(sortedTableReducer, initialState);
    useEffect(
        function callDispatchOnceToTriggerInitialSort() {
            dispatch({});
        },
        []
    );
    return [state, dispatch];
} 
```

这个钩子可以处理 JavaScript 能够与`<`或`>`进行比较的任何类型的数据:字符串、数字，甚至日期。在您自己的项目中，您可以根据需要提供更多的定制。例如，您可以编写自己的排序函数来比较复杂的对象，或者使用类似 Ramda 的 [`sortWith`](https://ramdajs.com/docs/#sortWith) 这样的函数来一次使用多个键进行排序。

既然钩子已经准备好了，将它集成到组件中就很容易了。这里有一个简单的例子来说明这是如何工作的:

[https://codesandbox.io/embed/sortable-table-hook-example-ghmq5](https://codesandbox.io/embed/sortable-table-hook-example-ghmq5)

很不错，对吧？它完全符合您对可排序表格的预期。

<small><sup>*</sup> (用户对某些数据类型的“升序”或“降序”的定义可能与 JavaScript 的解释不同。在我的代码中，为了简单起见，我保持原样。)</small>

## 额外积分:不区分大小写排序

我不喜欢 Javascript 将大写和小写字符串分开排序的方式。为了补救这一点，我做了一些调整。

首先，我创建了一个函数，将排序后的字符串转换成小写，而非字符串保持原样:

```
const toLowerCaseIfPossible = (x) => x.toLowerCase ? x.toLowerCase() : x; 
```

在减速器内部，我利用了 [Ramda 的`pipe`功能](https://ramdajs.com/docs/#pipe)。`pipe`创建一个函数，将它的参数传递给第一个函数，然后将该函数的输出作为参数传递给下一个函数。

```
const caseInsensitiveProp = pipe(prop(key), toLowerCaseIfPossible);
const sortFunc = sort(direction(caseInsensitiveProp)); 
```

哒哒！现在，字符串被视为不区分大小写的排序。

你可以在我自己的应用中看到这个钩子的源代码。