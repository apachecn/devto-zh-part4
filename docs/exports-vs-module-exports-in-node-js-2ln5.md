# Node.js 中的 exports vs module.exports

> 原文：<https://dev.to/cadoangelus/exports-vs-module-exports-in-node-js-2ln5>

```
exports.id = 1; //this is ok

exports = {id: 1}; //this is not ok

module.exports = {id: 1}; //this is ok 
```

## 为什么？

`exports`只是对`module.exports`的变量引用。所以我们可以改变`exports`对象的属性，但是如果我们改变整个对象，它将不再是对`module.exports`的引用