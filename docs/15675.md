# 让我们重新实现 JavaScript 的 Array.map 方法！

> 原文：<https://dev.to/jchiatt/let-s-re-implement-javascript-s-array-map-method-2cnd>

我最近一直在 Twitter 上发布[每日 JavaScript 挑战，今天的挑战涉及](https://twitter.com/hashtag/JCsJSTips)[重新实现 Array.map](https://twitter.com/jchiatt/status/1146034617646047233) 。

这当然不是唯一的解决方案，但我尝试了一下。

## array . map 是干什么的？

完整的细节可以在 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)中找到，但这里是事情的要点:

1.  。地图生活在阵列原型上
2.  在调用数组的每个元素上的回调函数后，它从返回一个新数组。
3.  回调函数有 3 个参数，其中最后两个是可选的:当前项、当前索引和您调用的原始数组。打开地图。
4.  您可以选择在回调之后传入第二个参数，这将是用来调用回调的`this`上下文。如果没有传入一个`this`上下文，回调将使用`undefined`作为`this`上下文来调用。

## 举例说明

```
const exercises = [
  "Bench Press", 
  "Svend Press", 
  "Chest Flyes",
  "Kettlebell Swing",
  "40 Yard Sprint",
];

const arr = exercises.map(item => {
  return "mapped " + item";
})

// returns:
[ 'mapped Bench Press',
  'mapped Svend Press',
  'mapped Chest Flyes',
  'mapped Kettlebell Swing',
  'mapped 40 Yard Sprint' ] 
```

## 重新实施方法

### 1。用我们知道会用到的参数来设置我们的函数。

我们知道 map 接受一个`callback`和一个`this`上下文。我们还知道，如果没有传入上下文，`this`上下文将被设置为`undefined`，因此我们将为该参数设置一个默认值。

```
function newMap(callback, thisArg = undefined) {
  // more code will go here
} 
```

### 2。如果有问题，尽早退出并给出错误信息。

第一项检查是确保在数组上调用了`newMap`。

```
function newMap(callback, thisArg = undefined) {
  // Ensure it called on an array.
  if (!Array.isArray(this)) {
    throw new TypeError('Must be called on an array');
  }
} 
```

接下来，确保提供了回调，因为这是必需的。

```
function newMap(callback, thisArg = undefined) {
  // Ensure it called on an array.
  if (!Array.isArray(this)) {
    throw new TypeError('Must be called on an array');
  }

  // Make sure a callback was supplied.
  if ( !callback ) {
    throw new TypeError('undefined is not a function');
  }
} 
```

最后，确保*如果*一个回调*被*提供，它是一个函数。

```
function newMap(callback, thisArg = undefined) {
  // Ensure it called on an array.
  if (!Array.isArray(this)) {
    throw new TypeError('Must be called on an array');
  }

  // Ensure a callback was supplied.
  if ( !callback ) {
    throw new TypeError('undefined is not a function');
  }

  // Ensure the supplied callback is a function.
  if ( typeof callback !== 'function' ) {
    throw new TypeError(callback + " is not a function");
  }
} 
```

### 3。对数组中的每一项执行回调。

```
function newMap(callback, thisArg = undefined) {
  // Ensure it called on an array.
  if (!Array.isArray(this)) {
    throw new TypeError('Must be called on an array');
  }

  // Ensure a callback was supplied.
  if ( !callback ) {
    throw new TypeError('undefined is not a function');
  }

  // Ensure the supplied callback is a function.
  if ( typeof callback !== 'function' ) {
    throw new TypeError(callback + " is not a function");
  }

  /**
  * Make a copy of the original array, just in case the callback 
  * does any mutations on the current value.
  */
  const self = Array.from(this);

  // Initialize a new array to build and return.
  let newArray = [];

  // Loop through the length of the original array.
  for (i = 0; i < this.length; i++) {
    /**
    * Execute the callback with `thisArg` as the `this` context.
    * Callback is executed with 3 arguments:
    * self[i] is the current value
    * i is the current index
    * this is the original array
    */
    let result = callback.call(thisArg, self[i], i, this);

    // Add the result of the callback to the new array.
    newArray[i] = result;
  }

  // Return the new array.
  return newArray;
} 
```

## 测试我们的新方法。

我们可以将我们的新方法附加到`Array`原型上，并像这样进行测试:

```
Array.prototype.newMap = newMap;

const arr = exercises.newMap(item => {
  return {% raw %}`new ${item}`{% endraw %};
})

// Returns the same result as Array.map
[ 'new Bench Press',
  'new Svend Press',
  'new Chest Flyes',
  'new Kettlebell Swing',
  'new 40 Yard Sprint' ] 
```

* * *

对更多提示和挑战感兴趣吗？[在 Twitter 上关注我](https://twitter.com/jchiatt)，也可以查看我的[收藏的以前的技巧和挑战](https://repl.it/@jchiatt/JCs-JS-Tips)。