# 所以你觉得你懂 JavaScript？

> 原文：<https://dev.to/aman_singh/so-you-think-you-know-javascript-5c26>

JavaScript 是一种有趣的语言，我们都喜欢它，因为它的本质。浏览器是 JavaScript 的家，两者一起为我们服务。
JS 有几个概念，人们往往会掉以轻心，有时可能会摔倒。像原型、闭包和事件循环这样的概念仍然是大多数 JS 开发人员绕道而行的模糊领域之一。正如我们所知道的“一知半解是一件危险的事情”，它可能会导致犯错误。

我们来玩一个小游戏，我会问你几个问题，你必须试着回答所有的问题。即使你不知道答案或者超出你的知识范围，也要猜一猜。记下你的答案，然后检查下面相应的答案。每个正确答案给自己打 1 分。开始了。

> 免责声明:我在下面的一些代码片段中特意使用了“var ”,这样你们就可以将它们复制粘贴到浏览器的控制台中，而不会遇到语法错误问题。但是我不赞成使用' var '声明变量。“let”和“const”声明的变量使您的代码健壮且不易出错。

* * *

**问题 1:浏览器控制台上会打印什么？**

```
var a = 10;
function foo() {
    console.log(a); // ??
    var a = 20;
}
foo(); 
```

**问题 2:如果我们用 let 或 const 代替 var，输出会一样吗？**

```
var a = 10;
function foo() {
    console.log(a); // ??
    let a = 20;
}
foo(); 
```

**问题 3:“新阵列”将包含哪些元素？**

```
var array = [];
for(var i = 0; i <3; i++) {
 array.push(() => i);
}
var newArray = array.map(el => el());
console.log(newArray); // ?? 
```

**问题 4:如果我们在浏览器控制台运行‘foo’函数，会不会造成堆栈溢出错误？**

```
function foo() {
  setTimeout(foo, 0); // will there by any stack overflow error?
}; 
```

问题 5:如果我们在控制台中运行以下功能，页面(选项卡)的用户界面会保持响应吗？

```
function foo() {
  return Promise.resolve().then(foo);
}; 
```

问题 6:我们能否在不导致类型错误的情况下，对下面的语句使用 spread 语法？

```
var obj = { x: 1, y: 2, z: 3 };
[...obj]; // TypeError 
```

**问题 7:当我们运行下面的代码片段时，控制台上会显示什么？**

```
var obj = { a: 1, b: 2 };
Object.setPrototypeOf(obj, {c: 3});
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });

// what properties will be printed when we run the for-in loop?
for(let prop in obj) {
    console.log(prop);
} 
```

**问题 xGetter()会打印什么值？**

```
 var x = 10;
var foo = {
  x: 90,
  getX: function() {
    return this.x;
  }
};
foo.getX(); // prints 90
var xGetter = foo.getX;
xGetter(); // prints ?? 
```

* * *

### 答案

现在，让我们试着从上到下回答每个问题。我会给你一个简单的解释，同时尝试用一些参考资料来揭开这些行为的神秘面纱。

**答案 1:** *未定义*。
T5】解释:用 var 关键字声明的变量在 JavaScript 中被[提升](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)，在内存中被赋值为 *undefined* 。但是初始化恰恰发生在您在代码中键入它们的地方。同样， *var 声明的*变量是[函数作用域的](http://2ality.com/2011/02/javascript-variable-scoping-and-its.html)，而 *let* 和 **const** 是块作用域的。所以，这个过程看起来是这样的:

```
var a = 10; // global scope
function foo() {
// Declaration of var a will be hoisted to the top of function.
// Something like: var a;

console.log(a); // prints undefined

// actual initialisation of value 20 only happens here
   var a = 20; // local scope
} 
```

* * *

**答案二:** *引用错误:a 未定义*。
**解释:** *let* 和 *const* 允许您声明范围仅限于使用它的块、语句或表达式的变量。与 *var* 不同，这些变量没有被提升，有一个所谓的[时间死区](http://exploringjs.com/es6/ch_variables.html#sec_temporal-dead-zone) (TDZ)。试图访问 *TDZ* 中的这些变量将抛出 *ReferenceError* ，因为它们只能在执行到达声明时才能被访问。阅读更多关于 JavaScript 中的[词法范围](http://2ality.com/2015/02/es6-scoping.html)和[执行上下文&堆栈](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)的信息。

```
var a = 10; // global scope
function foo() { // enter new scope, TDZ starts

// Uninitialised binding for 'a' is created
    console.log(a); // ReferenceError

// TDZ ends, 'a' is initialised with value of 20 here only
    let a = 20;
} 
```

下表概述了与 JavaScript 中使用的不同关键字相关联的提升行为和范围(*致谢:[阿克塞尔·劳施迈尔](https://twitter.com/rauschma)的博客[帖子](http://exploringjs.com/es6/ch_variables.html#_ways-of-declaring-variables)* )。