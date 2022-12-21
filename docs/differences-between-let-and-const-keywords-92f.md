# “let”和“const”关键字的区别？

> 原文：<https://dev.to/nunocpnp/differences-between-let-and-const-keywords-92f>

在我的上一篇文章中，我谈到了<u>**【var】**和**【let】**</u>的区别，现在是时候谈谈<u>**【let】**和**【const】**</u>关键词的区别了。

*让我们开始吧！*

**【let】****【const】**都是用来定义变量的。

### ***差 nr 1！***

使用关键字**“const”**您可以定义常量变量，这意味着，在第一次赋值后，您不能再次赋值。

使用关键字**“let”**您可以根据需要多次重新分配值，如果需要，您甚至可以更改值的类型。

我们来看一些代码:

```
let l = 1;
l = 2;
console.log(l); 
// 2

const c = 1;
c = 2;
Console.log(c); 
// Error: Uncaught TypeError: Assignment to constant variable 
```

## ***差 nr 2！***

用 **"const"** 关键字定义的变量不能在没有任何值的情况下初始化。您必须在声明它的同一个语句中指定它的值(这是有意义的，因为它以后不能更改)。

我们来看看这个例子:

```
let l
l = 1
console.log(l)
// 1

const c;
c = 1;
console.log(c);
// Error: Uncaught TypeError: Missing initializer on constant variable 
```

## ***差 nr 3！***

**"const"** 声明创建一个值的只读引用。这并不意味着它保存的值是不可变的，只是变量标识符不能被重新赋值。例如，在内容是对象的情况下，这意味着对象的内容可以被改变。

让我们来看看这是怎么回事:

```
const c = [ 1, 2 ];
c.push(3);
console.log(c);
// [ 1, 2, 3 ]

Const c = [ 1, 2 ];
C = [ 1, 2, 3 ];
console.log(c);
// Error: Uncaught TypeError: Assignment to constant variable 
```

*就是这样！再次简单但重要的概念保持新鲜在你的脑海里。*

*稍后见，了解更多提示！*