# JavaScript 和 Scope II -函数

> 原文：<https://dev.to/tchaffee/javascript-and-scope-ii-4i4e>

正如您在[上一篇关于 JavaScript 作用域](https://blog.toddbiz.com/blog/2019-06-01-javascript-and-scope/)的文章中所看到的，当我们有两个同名的不同变量时，函数可以创建一个新的作用域来确定要使用的值。

但是作用域是由函数声明的位置决定的，还是由函数运行的位置决定的呢？

```
var i = 10;

function foo() {
  var i = 2;
}

function bar() {
  i = i + 1;
  console.log ('value of i when bar is declared outside foo: ', i);
}

foo();

bar(); 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/0jwyg6za/)

在上面的例子中，很明显当`console.log`运行时`i`将有一个值`11`。但是如果我们在`foo`内部运行`bar`会发生什么呢？如果您是 JavaScript 新手，答案可能会让您大吃一惊。

```
var i = 10;

function foo() {
  var i = 2;

  bar();

}

function bar() {
  i = i + 1;
  console.log ('value of i when bar is declared outside foo: ', i);
}

foo(); 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/bue5zf9w/)

当`console.log`运行时，`i`变量将再次具有值`11`。

因为`bar`是在全局范围内声明的，所以它改变了存在于同一个全局范围内的`i`变量的值。即使它在`foo`内部运行，它也不会引用在`foo`范围内声明的`i`。

那么如果我们在`foo`函数中声明 bar 函数会发生什么呢？

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

foo(); 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/x3f1Ln8e/)

希望你能猜到`console.log`将显示`i`的值为`3`。`bar`函数是在`foo`函数的范围内声明的，所以它会改变并输出在`foo`范围内声明的`i`变量的值。

许多编程语言都是这样工作的，但并不是所有的语言都是这样，所以了解这方面的词汇是有好处的。当作用域由函数声明的位置决定时(函数在源代码中的编写位置)，我们称之为词法作用域。JavaScript 使用词法范围。

在 [JavaScript 和 scope III - Arrow 函数](https://blog.toddbiz.com/blog/2019-06-07-javascript-and-scope-iii/)中了解更多关于 Scope 的信息