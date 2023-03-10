# 关于使用 Javascript 的 Set 和 Map 对象的注意事项

> 原文：<https://dev.to/rgeraldporter/a-cautionary-note-about-use-of-javascript-s-set-and-map-objects-1kpc>

几周前，我在研究一个非常奇怪的 bug。正在组装一个包含一些用户数据的对象，当我们将该对象作为 JSON 发送到服务器时，它总是不完整的——它在一个属性中缺少数据。

我尝试了各种方法来找出导致这种情况的原因:是对象合并意外地发生了吗？是不是某个地方发生了我不知道的事件，删除了这些数据？

实际上比这要简单得多。问题在于被字符串化的对象中包含了`JSON.stringify()`和`Set`。

举个例子，我将用一些“幸运数字”创建一个用户，我只是把它作为一个例子，条目应该是唯一的，所以`Set`在理论上比`Array`更好。

```
const userData = {
    name: 'Haleema Greer',
    id: 7252,
    luckyNumbers: new Set([55, 45, 62, 21])
}; 
```

看起来足够好了。现在，让我们转换成 JSON，这样我们就可以将它发送到服务器。

```
JSON.stringify(userData);

// > "{\"name\":\"Haleema Greer\",\"id\":7252,\"luckyNumbers\":{}}" 
```

等等，刚才是什么吃了我们的`luckyNumbers`？

显然，数据不是一种无需额外工作就能转换成 JSON 的格式。

一个好的解决方案出现在[堆栈溢出](https://stackoverflow.com/a/46491780/325674)上。

```
function Set_toJSON(key, value) {
  if (typeof value === 'object' && value instanceof Set) {
    return [...value];
  }
  return value;
}

JSON.stringify(userData, Set_toJSON);

// > "{\"name\":\"Haleema Greer\",\"id\":7252,\"luckyNumbers\":[55,45,62,21]}" 
```

好多了！

许多指南会声明或暗示`Set`可以用作`Array`的插页，但它们的作用并不相同，如果你同样对待它们，可能会产生一些意想不到的后果。

要知道这两种类型是不同的，对一种有效的，对另一种可能无效。

`Map`也分享这个问题。

```
const userDataMap = {
    name: 'Haleema Greer',
    id: 7252,
    luckyNumbers: new Map([
        [1, 55],
        [2, 45],
        [3, 62],
        [4, 21]
    ])
};

JSON.stringify(userDataMap);

// > "{\"name\":\"Haleema Greer\",\"id\":7252,\"luckyNumbers\":{}}" 
```

解决这个问题并不简单，因为`Map`的结构不能直接转换成 JSON。这将是你喜欢如何解决这个问题的问题。

关于将`Map`转换成 JSON 的各种解决方案，请参见这个[堆栈溢出讨论](https://stackoverflow.com/questions/29085197/how-do-you-json-stringify-an-es6-map)。