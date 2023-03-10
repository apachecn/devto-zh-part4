# JavaScript 和作用域 IV -块

> 原文：<https://dev.to/tchaffee/javascript-and-scope-iv-blocks-300>

在 [JavaScript 和范围 I、II 和 III](https://blog.toddbiz.com/tags/javascript-scope-series/) 中，我们看了范围和*函数*。其他使用块的语句呢？由花括号`{}`括起来的代码段。

例如，我们经常用块在`for`循环中声明变量。

```
var buzz = 3;

for (var buzz = 1; buzz < 10; buzz++) {
  if (buzz === 9) { 
    console.log('for loop buzz:', buzz); // logs 9
  }
}

console.log('global scope buzz:', buzz); // logs 10 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/5m4wk8hu/)

当您运行上面的代码时，您会看到只有一个`buzz`变量。在全局范围内声明的那个。`for`循环改变全局变量。当最后一个`console.log`运行时，`buzz`变量的值将为`10`。

`if`语句呢？

```
var cute = 'not';

if (true) {
  var cute = 'cat';
}

console.log(cute); // logs 'cat' 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/Lutdkqsx/)

同样，只有一个`cute`变量，该变量在全局范围内的最终值将是`'cat'`。在块中重新声明变量不会创建新的范围和变量。

上面的两个例子都是我们看到的函数的*和*的反义词。函数为变量创建了一个新的范围。块不*不*为用`var`声明的变量创建新的作用域。

您知道吗，您可以不用使用`if`或`for`语句或其他任何东西来声明所有的块？我将留给你最后一个非常简单的例子来帮助你记住这条规则:*块不会为用`var`* 声明的变量创建新的作用域。

```
var toto = 'wants';

{
  var toto = 'a puppy';
}

console.log(toto); // logs 'a puppy' 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/17st5cmk/)