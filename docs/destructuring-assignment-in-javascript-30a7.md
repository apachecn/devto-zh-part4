# JavaScript 中的析构赋值

> 原文：<https://dev.to/savagepixie/destructuring-assignment-in-javascript-30a7>

JavaScript ES6 让我头疼的一件事是大量的表达式使用了像=、= >和{}这样的符号，而没有任何关键字来解释它们。我想随着时间的推移，它会让事情变得更简单，但现在我只是很难适应它。所以今天我要讲其中的一个表达式:**析构赋值**语法。

### 什么是析构赋值？

这是一种从数组中提取单个值或从对象中提取属性并将它们转化为独立变量的方法(即 *distinct* )。

对于数组和对象，析构赋值的语法略有不同，所以让我们分别来看看它们。

### 数组解构

假设我们有一个数组，里面有三种蔬菜。但是我们希望有三个独立的变量，每个变量包含一个蔬菜，而不是一个数组。

```
//This is what we've got
const arr = ["pepper", "carrot", "onion"];

//This is what we want
const veggieA = "pepper";
const veggieB = "carrot";
const veggieC = "onion"; 
```

析构赋值允许我们这样做。语法是这样的:

```
const [var1, var2, varN] = array; 
```

注意，我们也可以用`let`或`var`代替`const`来声明新变量。现在让我们把它应用到我们的例子中:

```
const arr = ["pepper", "carrot", "onion"];

const [veggieA, veggieB, veggieC] = arr;
console.log(veggieA); //"pepper"
console.log(veggieB); //"carrot"
console.log(veggieC); //"onion" 
```

### 物体毁坏

类似地，如果我们有一个对象，它有几个属性，我们想把它们变成不同的变量，用 ES5 我们会做这样的事情:

```
var book = { title: "The Hobbit", author: "JRR Tolkien", year: 1937 };
var title = book.title;
var author = book.author;
var year = book.year; 
```

使用析构语法，我们可以用下面的语句达到同样的效果:

```
const book = { title: "The Hobbit", author: "JRR Tolkien", year: 1937 };
const { title, author, year } = book; 
```

如果我们愿意，我们甚至可以指定新的变量名:

```
const book = { title: "The Hobbit", author: "JRR Tolkien", year: 1937 };
const { title : bookTitle , author : bookAuthor, year: bookYear } = book; 
```

因此，析构数组和析构对象之间的主要区别是:

1.  我们用括号(`[]`)表示数组，花括号(`{}`)表示对象。
2.  我们可以将变量名重新分配给对象属性(对于数组，我们总是给它们一个新的名字)。

### 奖金

我们也可以使用析构语法一次给多个变量赋值:

```
let a, b;
[a, b] = ["carrot", "onion"]; 
```

我们甚至可以用它来交换两个变量的值:

```
let a = 1;
let b = 5;
[a, b] = [b, a];
console.log(a); //5
console.log(b); //1 
```

### 结论

析构赋值语法是一种将数组或对象属性转化为不同变量的简单方法。它还允许我们一次给多个变量赋值，或者交换两个变量的值。