# 用于映射的对象

> 原文：<https://dev.to/atila/objects-for-mapping-2g2h>

基本上在 JavaScript 的整个存在过程中,`key`到`value`属性的映射都是以同样的方式完成的。很有可能你已经写了或者读了无数遍:

```
let dictionary = {
  id: 'foo',
  url: 'bar',
  last_update: new Date()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 对物体的告诫

此外，你不得不不止一次地将它转换成一个`array`来绕过。你看，对象并不真正适合映射变量，因为(1)它们不是可迭代的。除了遍历一个对象(`Object.entries()`、`Object.keys()`、`Object.values()`)的明显困难之外，还有一个它们不可迭代的副作用，你不能检查一个对象的**大小**或**长度**。

一些用例中的另一个要点是，开发人员不能够信任**顺序**，在这个顺序中`object`将存储它的键(至少对于数字键来说是这样，在这个顺序中，行为可以根据引擎而改变)。

## 插曲:Weakmap

Weakmap 保存一个键/值引用，但在它们之间保存一个弱引用。这样做的目的是让它更容易被垃圾收集，所以如果没有其他对`key`的引用，它就可以被收集。这意味着没有方法来检索大小，也没有方法来遍历 **Weakmap** 。

而且，它带有有趣的要求:键不能是原始类型，事实上，它**需要**成为`object`。尽管相对于使用一个对象作为地图来说，它有一个可能的优势:它保持了键的顺序。

⚠️:如果你打算使用一个`Weakmap`，请注意`length`的值将永远是`0`。

[浏览器兼容性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap#Browser_compatibility)

## 地图

与`object`不同的是，**地图对象**保持其关键点的插入顺序。作为只接受`object`作为`key`的`Weakmap`和只接受`string`或`number`的`object`的计数器，任何原语类型都可以是**映射**中的`key`。

**地图**不仅是可列举的，而且是**可列举的**。

```
let myMap = new Map([['foo', 'bar'], [0, 22]])

let iterator = myMap.entries()

console.log(iterator.next().value) // ['foo', 'bar']
console.log(iterator.next().value) // [0, 22]

console.log(myMap.size()) // 2 
```

Enter fullscreen mode Exit fullscreen mode

[浏览器兼容性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map/get#Browser_compatibility)

## 引用

*   [MDN 上的 weak map](https://mdn.io/weakmap)
*   [MDN 上的地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)