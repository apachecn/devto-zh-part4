# JavaScript 复制数组在 JavaScript 中克隆对象数组

> 原文：<https://dev.to/devcer/cloning-an-array-of-objects-in-javascript-3nng>

[![](img/a65025fb781f1df98321f7947032ba4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kQ6LqUAo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8F64T9PBPSCurUB3l1zbgw.jpeg)

如果你想更好地理解对象、数组和它们的引用，并创建它们的副本，那么你就来对地方了。

Javascript 对象和数组是可变的，这意味着它们的状态在创建后可以被修改。与字符串和数字类型不同，对象和数组是通过引用而不是值进行比较的。如果没有仔细处理这些问题，并且没有正确地维护状态，那么您很有可能会在应用程序中犯下大错。

有多种方法可以复制数组和对象，使其具有与其原始对象不同的引用。复制一个数组，比如说 100000 个对象，会非常昂贵。

这里我们将讨论几种正确克隆对象/数组的方法，以及复制对象数组的最佳执行方式。

### 克隆一个对象

#### ES6 道:

```
const originalObj = { name: 'cloning' }; 
const clonedObj = Object.assign({}, obj);
originalObj === clonedObj //false 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以简单地使用扩展操作符

```
const originalObj = { name: 'cloning' }; 
const clonedObj = {...obj};
originalObj === clonedObj //false 
```

Enter fullscreen mode Exit fullscreen mode

### 克隆一个数组

#### ES5 道:

```
const originalArr = ['a', 'b', 'c'];
const clonedArr = originalArr.slice(0); // slice with index is FASTER
originalArr === clonedArr // false 
```

Enter fullscreen mode Exit fullscreen mode

#### ES6 道:

```
const originalArr = ['a', 'b', 'c'];
const clonedArr = [...originalArr];
originalArr === clonedArr // false 
```

Enter fullscreen mode Exit fullscreen mode

### 克隆使用 JSON 对象

```
const originalArr = ['a', 'b', 'c'];
const clonedArr = JSON.parse(JSON.stringify(originalArr)); 
originalArr === clonedArr // false

const originalObj = { name: 'cloning' }; 
const clonedObj = JSON.parse(JSON.stringify(originalObj));
originalObj === clonedObj //false 
```

Enter fullscreen mode Exit fullscreen mode

然而，使用 JSON 方法克隆是一个非常昂贵的操作。

克隆一个对象数组是简单而容易的，但是大多数时候我们忘记了这样一个事实，即只克隆数组(包含对象)并不能进行深度克隆，新数组中的对象仍然引用相同的旧对象。

#### 举例:

```
const arrayObj = [{name: 'clone'}, {name: 'array'}, {name: 'object'}];
const copy = [...arrayObj];
copy === arrayObj // false
copy[0] === arrayObj[0] //true 
```

Enter fullscreen mode Exit fullscreen mode

有多种方法可以做到这一点，但大多数方法都不是执行式的，可能会完全破坏应用程序。开发人员最简单和最常用的方法是使用 JSON 方法，这显然是最慢的方法。我[测试了](https://jsperf.com/copying-an-array-of-objects)几种常用的克隆和复制数组和对象的方法，下面是一个样本大小为 10000 个对象的数组的结果，每个对象有一个键值对。

[![](img/47aba24d0aa55a65214f8bb287bb5e86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x9Do8vPe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQy-ZeSwWUdn5eEuUvrwdYw.png)

可能有许多其他方法来执行精确的操作，但在我测试的所有测试案例中，我发现带有 Spread 运算符的 Map 比其他方法表现出更好的性能。

**注意:** `Object.assign()`或者`{...}`不做深度克隆。

希望这对你有帮助。如果你在这篇文章结束时有任何问题，那么让我知道，以便我可以改进这篇文章。