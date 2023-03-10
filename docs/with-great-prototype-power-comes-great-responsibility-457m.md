# 伟大的原型力量会带来巨大的责任

> 原文：<https://dev.to/karataev/with-great-prototype-power-comes-great-responsibility-457m>

假设我们有一个简单的 JavaScript 对象。

```
let obj = {a: 1};
console.log(obj.a); 
```

Enter fullscreen mode Exit fullscreen mode

访问一个对象的属性有 O(1)的复杂度，也就是说，速度非常快。但事情没那么简单。如果找不到属性，则在对象的[原型](http://javascript.info/prototype-inheritance)中进行搜索。如果没有再次找到，这个过程继续，直到到达原型链根。

因此，如果对象的原型链很长，属性查找过程可能会非常耗时。

这里有一个例子

```
function getObjectWithLongPrototypeChain(depth = 1) {
  let nextObj = {a: 1};

  for (let i = 0; i < depth; i++) {
    nextObj = Object.create(nextObj);
  }
  return nextObj;
}

let regularObject = {a: 1};
let longObject = getObjectWithLongPrototypeChain(1000000);

let t0;
let t1;
t0 = performance.now();
console.log(regularObject.a);
t1 = performance.now();
console.log(`Property lookup in the regular object took ${(t1 - t0)} milliseconds.`);

t0 = performance.now();
console.log(longObject.a);
t1 = performance.now();
console.log(`Property lookup in the object with long prototype chain took ${(t1 - t0)} milliseconds.`); 
```

Enter fullscreen mode Exit fullscreen mode

而结果是

```
Property lookup in the regular object took 0.21500000730156898 milliseconds.
Property lookup in the object with long prototype chain took 42.249999998603016 milliseconds. 
```

Enter fullscreen mode Exit fullscreen mode

因此，第二个对象的属性查找的基本操作花费了令人难以置信的时间！虽然在产品代码中几乎不可能有这么长原型链的对象，但我认为了解 JS engine 如何解决对象的属性查找是很好的。