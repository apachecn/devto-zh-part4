# JavaScript 和 TypeScript 中的 void

> 原文：<https://dev.to/ddprrt/void-in-javascript-and-typescript-4o4h>

*本文原载于[Fett blog](https://fettblog.eu)T3】*

如果你来自传统的强类型语言，你可能熟悉`void`的概念:这种类型告诉你函数和方法在被调用时不返回任何东西。

`void`在 JavaScript 中作为操作符存在，在 TypeScript 中作为基本类型存在。在这两个世界中,`void`的工作方式与大多数人习惯的略有不同。

## JavaScript 中的 void

JavaScript 中的`void`是一个运算符，用于计算它旁边的表达式。无论计算哪个表达式，`void`总是返回`undefined`。

```
let i = void 2; // i === undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们为什么需要这样的东西？首先，在早期，人们能够覆盖`undefined`并给它一个实际值。`void`总是返回*真实的*未定义。

第二，这是一个很好的方法来调用立即被调用的函数:

```
void function() {
  console.log('What')
}() 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都不会污染全局命名空间:

```
void function aRecursion(i) {
  if(i > 0) {
    console.log(i--)
    aRecursion(i)
  }
}(3)

console.log(typeof aRecursion) // undefined 
```

Enter fullscreen mode Exit fullscreen mode

因为`void`总是返回`undefined`，并且`void`总是计算它旁边的表达式，所以你有一个非常简洁的方法从一个函数返回而不返回值，但是仍然调用一个回调函数例如:

```
// returning something else than undefined would crash the app
function middleware(nextCallback) {
  if(conditionApplies()) {
    return void nextCallback();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我想到了`void`最重要的用例:它是你的应用程序的安全门。当您的函数应该总是返回`undefined`时，您可以确保情况总是如此。

```
button.onclick = () => void doSomething(); 
```

Enter fullscreen mode Exit fullscreen mode

## 在打字稿中作废

TypeScript 中的`void`是`undefined`的子类型。JavaScript 中的函数总是会返回一些东西。
要么是一个值，要么是`undefined` :

```
function iHaveNoReturnValue(i) {
  console.log(i)
} // returns undefined 
```

Enter fullscreen mode Exit fullscreen mode

由于没有返回值的函数总是返回`undefined`，而`void`在 JavaScript 中总是返回未定义的，因此 TypeScript 中的`void`是告诉开发人员该函数返回`undefined` :
的合适类型

```
declare function iHaveNoReturnValue(i: number): void 
```

Enter fullscreen mode Exit fullscreen mode

`void` as 类型也可以用于参数和所有其他声明。唯一可以传递的值是`undefined` :

```
declare function iTakeNoParameters(x: void): void

iTakeNoParameters() // 👍
iTakeNoParameters(undefined) // 👍
iTakeNoParameters(void 2) // 👍 
```

Enter fullscreen mode Exit fullscreen mode

所以`void`和`undefined`差不多。虽然有一点点不同，但这一点很重要:`void`作为一个返回类型，可以用不同的类型替换，以支持高级回调模式:

```
function doSomething(callback: () => void) {
  let c = callback() // at this position, callback always returns undefined
  //c is also of type undefiend
}

// this function returns a number
function aNumberCallback(): number {
  return 2;
}

// works 👍 type safety is ensured in doSometing
doSomething(aNumberCallback) 
```

Enter fullscreen mode Exit fullscreen mode

这是期望的行为，经常在 JavaScript 应用程序中使用。在我的其他文章中可以读到更多关于这种叫做[可替代性](https://fettblog.eu/typescript-substitutability/)的模式。

如果你想确保传递只返回`undefined`(比如“nothing”)的函数，一定要修改你的回调方法签名:

```
- function doSomething(callback: () => void) { + function doSomething(callback: () => undefined) { /* ... */ } 
function aNumberCallback(): number { return 2; }

// 💥 types don't match
doSomething(aNumberCallback) 
```

Enter fullscreen mode Exit fullscreen mode

大多数时候你可能会和`void`相处得很好。