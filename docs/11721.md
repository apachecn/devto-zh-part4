# 带 Lodash FP 的整洁 Redux 减速器

> 原文：<https://dev.to/robbyronk/tidy-redux-reducers-with-lodash-fp-3fke>

Lodash 是我写 JavaScript 时接触最多的库。在这篇文章中，我将向你展示 Lodash 的函数式编程(FP)构建如何真正整理你的 reducers。

首先，导入我们将使用的函数:`import {set, update, flow} from 'lodash/fp';`。注意这些是从`'lodash/fp'`而不是`'lodash'`导入的。

然后看看这个例子:

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'SET':
      return set('some.deep.key', action.value, state);
    case 'INCREMENT':
      return update('some.deep.key', i => i + 1, state);
    case 'FOO':
      return flow(
        set('some.deep.key', action.value),
        update('another.deep.key', i => i * 2),
      )(state);
  }
}; 
```

Lodash 的热心用户将会看到关于`set`和`update`的争论是杂乱无章的！在`FOO`的分支中，我们只传入了两个参数，这很奇怪，因为我们之前传入了三个参数！这是怎么回事？

由于 Lodash 的 FP 构建，参数的顺序不同。这个版本没有标准版本的文档记录的好，这很令人失望，但是这里有一些可用的文档。

> lodash/fp 模块通过导出 lodash 的一个实例来促进一种更加函数式编程(fp)友好的风格，该实例的方法被包装以产生不可变的自动定制的迭代优先数据最后方法。

坚持住！没有听起来那么可怕。

*   不可变的:reducer 不能修改状态，相反，它们必须返回一个新的状态。Lodash FP 和 Redux 走在一起就像 PB & J！
*   自动定制的:定制的函数接受严格数量的参数。如果少给一点，它将返回另一个函数，该函数在返回值之前接受其余的参数。
*   *迭代优先*:“迭代者”是洛达什的超能力之一。在我们的例子中，它是对象内部的虚线路径(我们的状态片段)。
*   *数据-最后的*:我们例子中的“数据”是`state`。在普通的 Lodash 中，数据通常是第一个参数。

如果给了`set`一个路径和一个值而不是状态，它将返回一个函数。返回的函数接受一个参数，即状态，然后返回新的状态。那是奉承！

```
const setNameToRobby = set('name', 'Robby');
setNameToRobby({ name: 'Robert', location: 'Wellington' });
// => { name: 'Robby', location: 'Wellington' } 
```

我经常发现一个给定的动作可以在一个缩减器中完成几件事情。这就是`flow`和 curried 函数派上用场的地方。`flow`接受多个函数并返回一个将它们组合在一起的函数。大量的`set`和`update`(以及其他你可能在 Lodash FP 中发现有用的)可以被赋予`flow`，因为参数和流程将产生一个单一的函数。这个 single 函数接收`state`并将其传递给第一个原始函数，第一个函数的值传递给第二个函数，依此类推。

```
flow(
  set('some.deep.key', action.value),
  update('another.deep.key', i => i * 2),
)(state); 
// is equal to:
update(
  'another.deep.key', 
  i => i * 2, 
  set(
   'some.deep.key', 
   action.value, 
   state
  ),
)
// and quite a bit more readable! 
```