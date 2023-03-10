# React 开发人员的不变量

> 原文：<https://dev.to/ganeshmani/immutablejs-for-react-developers-2g5>

在本文中，我们将了解什么是不变性，以及为什么它对 React 应用程序很重要。它为 ReactJS 开发人员解释了 immutable.js

### 什么是不变性

根据维基百科，

```
In object-oriented and functional programming, an immutable object . It is an object whose state cannot be modified after it is created. This is in contrast to a mutable object (changeable object), which can be modified after it is created. 
```

不变性是指**在被创建**后就不能改变的东西。不可变对象在创建后不能修改。

比如说，我们在 javascript 中有一个常量变量。

```
const getValue = "Some Value"
```

常量变量被赋值后，我们不能修改它。它将抛出一个错误。它是永恒的

```
getValue = "Change Value"
```

### 为什么不变性很重要？

我们用一个类比来理解这个概念。假设你在银行账户里存钱。最重要的是，你要分析你每个月存了多少钱。因此，为了分析这一点，你需要有一个你前几个月储蓄的轨迹。

如果你没有储蓄历史，就很难追踪记录。也就是说，对于一些复杂的 React 应用程序，我们需要维护应用程序的状态，比如什么是以前的状态，什么是当前的状态。这样，我们就可以渲染、处理带有变化的事件。

### Redux 的目的

为了解决这个问题，redux 就发挥了作用。但是，还有一个问题存在。不过，redux 解决了管理状态的问题。

但是，管理数据的不变性取决于我们。例如，如果我们有一个像这样的值

```
item = {
 "id" : 1,
  "name" : "Ganesh"
}
```

redux 保持以前的状态和当前状态。但是我们仍然可以修改对象中的值。它是可变对象。这就是问题所在。

在 javascript 中使数据结构不可变。Immutable.js 开始发挥作用。

### 不可变. js 简介

不可变. js 提供了许多持久不可变的数据结构，包括:`[List](https://immutable-js.github.io/immutable-js/docs/#/List)`、`[Stack](https://immutable-js.github.io/immutable-js/docs/#/Stack)`、`[Map](https://immutable-js.github.io/immutable-js/docs/#/Map)`、`[OrderedMap](https://immutable-js.github.io/immutable-js/docs/#/OrderedMap)`、`[Set](https://immutable-js.github.io/immutable-js/docs/#/Set)`、`[OrderedSet](https://immutable-js.github.io/immutable-js/docs/#/OrderedSet)`和`[Record](https://immutable-js.github.io/immutable-js/docs/#/Record)`。

让我们看看如何在 React/Redux 应用程序中实现 immutable.js。

我假设你熟悉 React 和 Redux。如果你是 React 和 Redux 新手，可以参考这篇优秀的文章。

*   [完成反应和还原](https://daveceddia.com/redux-tutorial/)
*   [Redux - FlavioCopes](https://flaviocopes.com/redux/)
*   [Redux -官方文档](https://redux.js.org/introduction/getting-started)

用于初始化不可变数据结构中的状态。在 reducer 文件中，用不可变对象初始化状态，在每一个动作中，reducer 都将创建一个新的现有数据实例。

[https://codesandbox.io/embed/reduximmutable-vrtdn](https://codesandbox.io/embed/reduximmutable-vrtdn)

在 Immutable.js 中有三种重要的数据结构

*   一个`Iterable`是一组可以迭代的键/值条目(例如，使用`.map()`、`.filter()`、`.reduce()`、…)。不可变. js 中的所有集合，像`Map`和`List`，都使用`Iterable`作为基类
*   **地图**——把`Map`想象成一个物体。它使用键存储值。让我们创建一个`Map`来表示在线商店中一本书的数据结构。为了让事情变得简单一点，我们将使用 Immutable.js 提供的`.fromJS()`方法
*   **List** - List 类似于 Javascript 数组。它们被排序和索引。我们可以做这样的手术。push()，。pop()和。unshift()就像 javascript 数组操作一样

### 读取和修改地图中的数据

主要是读取不可变的. js 中的数据。我们需要从数组(或)对象中获取数据

### 阅读日期

```
state.get('items').get('title')
```

主要是为了得到一个嵌套的对象。我们必须从最高级别到我们想要获得价值的级别。这将是获取嵌套对象的麻烦。为了简化这个结构，我们可以使用这样的东西

```
state.getIn(['items','title'])
```

两者将返回相同的值。后面的将会比前一个更简单，可读性更强。

### 修改数据

```
state.setIn(['items','title'],'Hola')
```

修改数据类似于读取数据。这里唯一的区别是我们需要使用 **setIn** 而不是 **get** ， **getIn**

### 读取和修改列表中的数据

js 中的 List 类似于标准的 javascript 数组。它们被排序和索引。我们可以使用像**这样的操作来访问列表。推()**，**。pop()** 和**。unshift()**

[https://codesandbox.io/embed/reduximmutable-vrtdn](https://codesandbox.io/embed/reduximmutable-vrtdn)

```
state.getIn(['items',1])
```

我们也可以通过索引来访问列表

```
state.getIn(['items',0,'test'])
```

### 修改列表中的数据

这类似于修改地图中的数据。除此之外，我们可以用索引来修改数据。

```
state = state.setIn(['items', 0], 'Fantasy');
```

同时，Immutable.js 还有其他方法来修改列表中的数据。他们是**。更新()**和**。updateIn()** 。与**相比，这些更加简单易读。set()** 和**。setIn()**

```
state = state.update('items', items => items.push('Wizards'));
```

例如，让我们想象一下，我们需要将亚马逊的价格更新为 6.80 美元。这可以通过使用`.setIn()`和正确的密钥路径来完成

```
state = state.setIn(['storeListings', 0, 'price'], 6.80);
```

然而，我们通常不知道我们想要更新的索引。findIndex()

```
const indexOfListingToUpdate = state.get('storeListings').findIndex(item => {
  return item.get('id') === 'amazon';
});
state = state.setIn(['storeListings', indexOfListingToUpdate, 'price'], 6.80);
```

同时，阅读更多关于 ReactJS 的 Immutable.js。

*   [Redux 文档](https://redux.js.org/recipes/using-immutablejs-with-redux#why-should-i-use-an-immutable-focused-library-such-as-immutablejs)
*   [Redux 和 Immutable.js](https://tech.affirm.com/redux-patterns-and-anti-patterns-7d80ef3d53bc)
*   [JavaScript 中不可变的数据结构](https://jlongster.com/Using-Immutable-Data-Structures-in-JavaScript)

要了解更多关于网络开发的信息-

*   [Nginx 前端开发-第 1 部分](https://cloudnweb.dev/2019/05/nginx-for-front-end-developers/)
*   [Nginx 前端开发-第二部分](https://cloudnweb.dev/2019/06/nginx-for-front-end-developers-beyond-the-basics/)