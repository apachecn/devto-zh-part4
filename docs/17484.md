# 用现代 API 映射 JavaScript 对象

> 原文：<https://dev.to/n1ru4l/mapping-javascript-objects-with-modern-apis-1115>

`Object.entries`允许你将一个对象转换成一个数组结构:

```
console.log(Object.entries({prop1: 1, prop2: 2 }))
// [ ["prop1", 1], ["prop2", 2] ] 
```

这个方法是 ECMAScript 2017 规范的一部分，已经被很多开发者(包括我)大量使用。

直到最近，还没有方便的方法可以将这种结构转换回对象。

现在，我们终于有了允许从由`Object.entries`返回的结构中构造一个对象的`Object.fromEntries`。

这使得映射对象非常方便:

**实现:**

```
const mapValues = (input, mapper) =>
  Object.fromEntries(
    Object.entries(input).map(([key, value]) => [
      key,
      mapper(value, key, input)
    ])
  ); 
```

**用法举例:**

```
const input = {
  prop1: 1,
  prop2: 4,
};

const output = mapValues(input, value => value * 2);

expect(output).toEqual({
  prop1: 2,
  prop2: 8,
}); 
```

**支持**

浏览器支持:[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/from entries # Browser _ compatibility](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries#Browser_compatibility)
节点支持:[https://node.green/#ES2019-features-Object-from entries](https://node.green/#ES2019-features--Object-fromEntries)