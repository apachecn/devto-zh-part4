# 一个“简单”的打字稿例子

> 原文：<https://dev.to/skube/a-simple-typescript-example-7i7>

当学习 [TypeScript](https://www.typescriptlang.org/docs/handbook/functions.html#function-types) 时，如果应用到极端，它可能看起来冗长和混乱。以一个简单的`add`函数为例...

在 JavaScript 中:

```
function add(x, y) { 
  return x + y;
};

// or as function expression
let add = function (x, y) {
  return x + y;
};

// or with ES6 fat-arrow
let add = (x, y) => x + y; 
```

Enter fullscreen mode Exit fullscreen mode

在 TypeScript 中，可以用几种方式定义类型:

```
function add(x: number, y: number): number { 
  return x + y;
};

// or as function expression (type inferred on the _left_ operand)
let add = function (x: number, y: number): number {
  return x + y;
};

// or as function expression (type inferred on the _right_ operand)
let add: (x: number, y: number) => number = function (x, y) {
  return x + y;
};

// or as function expression (explicitly and somewhat redundantly)
let add: (x: number, y: number) => number = function (x: number, y: number): number {
  return x + y;
};

// or with ES6 fat-arrow (inference on left)
let add = (x: number, y: number): number => x + y;

// or with ES6 fat-arrow (inference on right)
let add: (x: number, y: number) => number = (x, y) => x + y;

// or with ES6 fat-arrow (😳)
let add: (x: number, y: number) => number = (x: number, y: number): number => x + y; 
```

Enter fullscreen mode Exit fullscreen mode

呀！这是一个非常简单、纯粹的功能！

但实际上，我们可以缩小类型(通过使用`const`)并让 TS 尽可能多地推断:

```
function add(x: number, y: number) { 
  return x + y;
};
// Inferred type is function add(x: number, y: number): number

const add = (x: number, y: number) => x + y;
// Inferred type is let add: (x: number, y: number) => number 
```

Enter fullscreen mode Exit fullscreen mode

所有相同类型的安全，但最少的冗长！FTW。