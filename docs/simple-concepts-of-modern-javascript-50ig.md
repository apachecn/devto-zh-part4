# 现代 JavaScript 的简单概念

> 原文：<https://dev.to/pachicodes/simple-concepts-of-modern-javascript-50ig>

在我开始之前，请注意，这篇文章是我试图向自己解释这些概念，所以请确保我完全理解它们，并在我需要时有这些笔记。这些概念还有很多，但我的笔记将总结为非常基本的解释。

概念有:
*箭头函数、
*模板文字、
*Var、Let 和 Const、
*三元(条件)运算符、
*速记对象赋值和
*Spread 运算符。

**箭头函数**
简单来说，箭头函数是用更短的形式写出来的函数。他们之所以有这个名字，是因为他们用箭头符号 **= >** 代替了函数这个词。

```
//regular function
function hello() {
  console.log('Hello');
}

//arrow function
const hello = () => {console.log('Hello')} 
```

Enter fullscreen mode Exit fullscreen mode

模板文字
我使用它们已经有一段时间了，但不知道它们叫什么。这是一种创建字符串的新方法。老实说，我不太确定如何解释，所以我将展示:

```
const name = 'Ana';
const age = 32;
//regular string
console.log(name + " is " + age + " years "+ "old");

//template literal
console.log(`${name} is ${age} years old`); 
```

Enter fullscreen mode Exit fullscreen mode

结果是一样的，但是作为模板文字编写更容易，我不用担心空格，代码看起来很整洁。

**Var，Let 和 Const**
总是使用 **const** 除非你确定你要重新赋值，它们**使用** let。基本上 var 就是死 x.x

**三元(或条件)运算符**
这些都很酷，就像这里的大多数其他概念一样。它是唯一使用 3 个操作数的运算符，通常用于替换 if/them。
下面是如何

```
//Using if
var todayIsFriday = false;
if(todayIsFriday){
  console.log('It is Fri-yay')
} else{
  console.log('Not quite there yet')
}

// using Ternary Operator
var todayIsMonday = true;
console.log(todayIsMonday ? 'Yey, another week' : 'Yey, it is not Monday') 
```

Enter fullscreen mode Exit fullscreen mode

**速记对象赋值**
如果你想定义一个对象，它的键与作为属性传递的变量同名，你可以使用速记，只需传递键名:

```
//regular
const abc = {
  a:a,
  b:b,
  c:c,
};

//shorthand
const efg = { e, f, g}; 
```

Enter fullscreen mode Exit fullscreen mode

**传符**
最后一个，传符...另一个解释起来有点棘手。基本上，它接受一个数组或对象并扩展它。扩展运算符的语法是**...**

```
const obj1 = {a: 'a', b: 'b'};
const obj2 = {c: 'c', ...obj1};
console.log(obj2);

//The console will log: Object {  a: "a",  b: "b",  c: "c"} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我今天的全部内容。也许我的小笔记可以帮助像我这样的人导航 JavaScript。
我的 codepen 上有所有这些代码，如果你想自己练习的话
:[https://codepen.io/collection/DLMWOM/](https://codepen.io/collection/DLMWOM/)

周一快乐，伙计们！