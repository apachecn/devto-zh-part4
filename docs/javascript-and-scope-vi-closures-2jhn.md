# JavaScript 和作用域 VI -闭包

> 原文：<https://dev.to/tchaffee/javascript-and-scope-vi-closures-2jhn>

在 [JavaScript 和 Scope II](https://blog.toddbiz.com/blog/2019-06-03-javascript-and-scope-ii/) 中，我们看到函数作用域不是由函数运行的位置决定的。相反，它是由函数的声明位置决定的。

```
var i = 10;

function foo() {
  var i = 2;

  function bar() {
    i = i + 1;
    console.log ('value of i when bar is declared inside foo: ', i);
  }

  bar();

}

foo(); // logs 3 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/x3f1Ln8e/)

但是上面的例子没有完全说明词法范围，因为`bar`函数也是`foo`函数中的 *run* 。让我们在`foo`函数之外运行`bar`函数。

```
var i = 10;

function foo() {
  var i = 2;

  function bar() {
    i = i + 1;
    console.log ('value of i when bar is declared inside foo: ', i);
  }

  return bar;
}

var barFunc  = foo();

barFunc(); // Runs in global scope but logs 3 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/so324k6x/)

在上面的代码中，我们不再在`foo`内部运行`bar`。相反，我们返回内部函数而不运行它。然后我们在最后一行代码上运行函数:它在全局范围内运行。最重要的是，它在 `foo`完成运行后*运行。但是它不使用全局`i`变量。相反，它使用的是已经运行完的函数中的`i`变量！*

你刚才看到的东西的官方名称是终结。这就是简单的结束。所有的闭包意味着，即使一个函数在声明它的作用域之外运行，它仍然使用声明它的作用域。在这种情况下，内部函数`bar`使用外部函数`foo`的范围。即使在我们运行`bar`时`foo`已经停止运行，这也是事实。

这打破了我们对`foo`停止运行时作用域不再存在的预期。