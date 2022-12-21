# TypeScript:基本工作流

> 原文：<https://dev.to/miku86/typescript-basic-workflow-pe>

## [T1】简介](#intro)

在本文中，我们将了解编写打字稿的基本工作流程。

* * *

## 概述

3 个步骤是必要的:

1.  编写打字稿代码
2.  编译成 JavaScript
3.  运行 JavaScript 代码

* * *

## 1。编写打字稿代码

```
// explicit typing
let myAge: string = 'thirty'; // set it explicitly to a string
myAge = 'thirty-one'; // that works

// implicit typing / type inference
let yourAge = 30; // typescript infers the type as number
yourAge = myAge; // trying to set it to a string, does not work

console.log(myAge);
console.log(yourAge); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 2。编译成 JavaScript

```
tsc index.ts // tries to create a file index.js from index.ts
index.ts:7:1 - error TS2322: Type 'string' is not assignable to type 'number'.

7 yourAge = myAge; // trying to set it to a string, does not work

Found 1 error. 
```

Enter fullscreen mode Exit fullscreen mode

因为有一个错误，你必须修复代码。

* * *

## 搞定它

```
// explicit typing
let myAge: string = 'thirty'; // set it explicitly to a string
myAge = 'thirty-one'; // that works

// fix by using explicit typing
let yourAge: number | string = 30; // set it explicitly to a number or a string
yourAge = myAge; // trying to set it to a string, works

console.log(myAge);
console.log(yourAge); 
```

Enter fullscreen mode Exit fullscreen mode

再次编译，直到没有错误。

* * *

## 3。运行 JavaScript 代码

```
node index.js
thirty-one
thirty-one 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下一部分

我们将学习如何配置`TypeScript`编译器。

* * *

## 进一步阅读

[打字首页](https://www.typescriptlang.org)
[打字维基](https://en.wikipedia.org/wiki/TypeScript)
[打字 5 分钟](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
[基本类型](https://www.typescriptlang.org/docs/handbook/basic-types.html)
[各种样本](https://www.typescriptlang.org/samples/index.html)