# JavaScript 和范围 I -简介

> 原文：<https://dev.to/tchaffee/javascript-and-scope-i-1e7h>

作用域决定了变量在程序中的存在和不存在。

```
function bar () {
  var foo = 2;
  console.log(foo); // 2
}

bar();

console.log(foo); // ReferenceError: foo is not defined; 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/L4h60y79/6/)

当我们有两个名称相同的*不同的*变量时，作用域也决定了使用哪个值。

```
var foo = 1;

function bar () {
  var foo = 2;
  console.log('foo inside bar: ', foo); // 2
}

bar();

console.log('foo outside bar: ', foo) // 1; 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/0oy9jc1n/2/)

如果您运行上面的示例，您将首先看到“2”记录到控制台，然后您将看到“1”。在`bar`函数中创建的`foo`与在程序顶部创建的`foo`是不同的变量。

在程序顶部声明的`foo`变量在全局范围内。函数中声明的`foo`变量仅在该函数的范围内。我们说在函数*中声明的`foo`变量隐藏了全局变量*。

函数参数呢？它们是否隐藏了更高层次范围的变量？

```
var foo = 1;
function bar (foo) {  foo = foo + 2;
  console.log('foo inside bar: ', foo); // logs 7
}

bar(5);

console.log('foo outside bar: ', foo); // logs 1 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/cqt2dxh4/)

当涉及到作用域和遮蔽时，参数名的行为就像变量一样，这一事实在以后关于作用域和闭包的文章中很重要。

在 [JavaScript 和 scope II - Functions](https://blog.toddbiz.com/blog/2019-06-03-javascript-and-scope-ii/) 中了解更多关于 Scope 的信息