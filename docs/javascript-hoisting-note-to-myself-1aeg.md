# 给自己的 Javascript 提升笔记

> 原文：<https://dev.to/selengora/javascript-hoisting-note-to-myself-1aeg>

变量可以在声明之前使用。 [*](https://www.w3schools.com/js/js_hoisting.asp)

```
x = 24; // Assign 24 to x
console.log(x); // 24
var x; // Declare x 
```

## var，let，const 差异

`var`声明阶段和初始化阶段是同一级别。`var`变量被吊起。

`let`声明阶段在未初始化状态之后就来到了初始化阶段。

对于`let`变量(包括常量和类)，提升**无效**。
初始化前，变量处于时间死区，不可访问。 [*](https://dmitripavlutin.com/variables-lifecycle-and-why-let-is-not-hoisted/)

稍微深入一下`let`、`const`变量，实际上它们是提升的，但是…

> 如果在初始化之前访问变量，即使访问代码在声明之下(例如，在过早调用的提升函数声明中)，也会抛出异常。

用户 Bergi 在 stackoverflow 上有解释

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:变量是用 let 还是 const 声明的？](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-hoisted/31222689#31222689)

Jul 4 '15[![](img/e3f0373ec76330150a340eacd410b600.png)352![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-hoisted/31222689#31222689) </header>

@thefourtheye 说的对，这些变量**在声明之前不能访问**。然而，事情比这要复杂一点。

> 用`let`或`const`声明的变量没有被提升吗？这到底是怎么回事？

**所有申报** ( `var`、`let`、`const`、`function`、`function*`……

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-hoisted/31222689#31222689)</button>

## 功能提升？

函数声明被**吊起**

```
helloFunction(); // Hello hoisting
// function declaration
function helloFunction() {
    console.log('Hello hoisting');
} 
```

赋值函数(函数表达式)被**未吊**

```
myNewFunction(); //Uncaught TypeError: myNewFunction is not a function
// function expression
let myNewFunction = function(){
    console.log('Hello hoisting expression');
} 
```

参考资料:
[w3schools](https://www.w3schools.com/js/js_hoisting.asp)
[YDKJS](https://github.com/getify/You-Dont-Know-JS/blob/master/up%20%26%20going/ch2.md)
[@ freecodecamp](https://www.freecodecamp.org/news/what-is-variable-hoisting-differentiating-between-var-let-and-const-in-es6-f1a70bb43d)
[@ freecodecamp](https://www.freecodecamp.org/news/function-hoisting-hoisting-interview-questions-b6f91dbc2be8)