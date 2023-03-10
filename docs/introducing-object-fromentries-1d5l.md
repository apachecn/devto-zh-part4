# 引入 Object.fromEntries

> 原文：<https://dev.to/laurieontech/introducing-object-fromentries-1d5l>

我一直很喜欢这个系列，因为它给了我真正深入了解这些新特性的机会。

今天，我们来看看`Object.fromEntries`！

# 让我们从 Object.entries 开始

首先要知道的是，在 ECMAScript 之前的版本中，我们被介绍到了`Object.entries`。这是一个漂亮的函数，它允许我们通过将对象转换成数组来遍历对象中的键和值。

最基本的是，它把一个物体变成了这样。

```
const obj = {a: 1, b: 2, c: 3}
const entries = Object.entries(obj)
// [['a', 1], ['b', 2], ['c', 3]] 
```

Enter fullscreen mode Exit fullscreen mode

但是更常见的用例是迭代结果。

```
const obj = {a: 1, b: 2, c: 3}
const entries = Object.entries(obj)

for(const [key, value] of entries) {
    // do something with key and value here
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，当您使用`Object.entries`时，您将被数据困在一个数组中。然后是`Object.fromEntries`。

# 输入 Object.fromEntries

原来，`Object.fromEntries`正好是`Object.entries`的逆。举这个例子。

```
const obj = {a: 1, b: 2, c: 3}
const entries = Object.entries(obj)
const newObj = Object.fromEntries(entries)
// {a: 1, b: 2, c: 3} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子除了来回改变数据结构之外什么也不做。但是有了这么多可用于数组的助手函数，很容易看到能够这样做的好处。

# 我们为什么要这样

我们有这么多奇妙的函数，允许我们转换数组。像`map, reduce, filter,`[`flat`](https://dev.to/laurieontech/demystifying-array-prototype-flat-gi9)[`flatMap`](https://dev.to/laurieontech/understanding-array-prototype-flatmap-56bm)之类的东西。`Object.entries`让我们也能把它们用在物品上。如果我们把我们的`Object`改造成`Array`，它们就可以使用了。

```
const obj = {a: 1, b: 2, c: 3}
const result = Object.entries(obj).map(([key, value]) => [key, value * 2]) 
// [['a', 2], ['b', 4], ['c', 6]] 
```

Enter fullscreen mode Exit fullscreen mode

但是没有了`Object.fromEntries`，我们就被困在了`Array`结构中。有了它的加入，我们反而可以这样做了！

```
const obj = {a: 1, b: 2, c: 3}
const result = Object.fromEntries(
         Object.entries(obj).map(
            ([key, value]) => [key, value * 2]
         ))
// {a: 2, b: 4, c: 6} 
```

Enter fullscreen mode Exit fullscreen mode

# 不只是物体

这个函数最大的优点之一是它适用于所有的可迭代对象。这意味着你可以把一个`Array`变成一个`Object`，但是你也可以把一个`Map`变成一个`Object`。

```
const map = new Map([ ['a', 1], ['b', 2], ['c', 3] ]);
const obj = Object.fromEntries(map);
// {a: 1, b: 2, c: 3} 
```

Enter fullscreen mode Exit fullscreen mode

# 要注意的一件事

`Object`和`Array`的区别在于后者不需要唯一的键。这意味着`Object.fromEntries`会导致你丢失信息。

```
const arr = [['a', 1], ['a', 2], ['c', 3]]
const entries = Object.fromEntries(arr)
// {a: 2, c: 3} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们丢失了值`1`。

# 如此而已

我们做到了！能够对对象使用所有的数组操作函数非常有用！让`Object.fromEntries`关闭`Object.entries`创建的循环。

希望您喜欢我们的 ES2019 乐趣！